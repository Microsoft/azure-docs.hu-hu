---
title: Ügyfél által felügyelt kulcs használata Azure-lemezek titkosításához az Azure Kubernetes szolgáltatásban (ak)
description: Saját kulcsok (BYOK-EK) használatával titkosíthatja az AK-OS operációs rendszert és az adatlemezeket.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 4b1c311132cc812ccb2bbbc95c4b7414b108008c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499203"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Saját kulcsok (BYOK) használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat, amelyekkel az AK-fürtökhöz tartozó operációs rendszer és adatlemezek esetében az inaktív adatok titkosítását is használhatja. További információ az ügyfél által felügyelt kulcsokról [Linux][customer-managed-keys-linux] és [Windows][customer-managed-keys-windows]rendszeren.

## <a name="limitations"></a>Korlátozások
* Az adatlemez titkosításának támogatása a 1,17-es vagy újabb Kubernetes-verziót futtató AK-fürtökre korlátozódik.
* Az operációs rendszer és az adatlemez titkosítása az ügyfél által felügyelt kulcsokkal csak ak-fürt létrehozásakor engedélyezhető.

## <a name="prerequisites"></a>Előfeltételek
* Ha Key Vault használatával titkosítja a felügyelt lemezeket, engedélyeznie kell a Soft Delete (Törlés) és a kiürítési *Azure Key Vault* védelmét.
* Szüksége lesz az Azure CLI 2.11.1 vagy újabb verziójára.

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault példány létrehozása

A kulcsok tárolásához használjon Azure Key Vault-példányt.  Igény szerint az [ügyfél által felügyelt kulcsok konfigurálására][byok-azure-portal] is használhatja a Azure Portal Azure Key Vault

Hozzon létre egy új *erőforráscsoportot*, majd hozzon létre egy új *Key Vault* példányt, és engedélyezze a Soft delete és a kiürítés védelmet.  Ügyeljen arra, hogy az egyes parancsokhoz ugyanazt a régiót és erőforráscsoport-nevet használja.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet-példány létrehozása

Cserélje le a *myKeyVaultName* nevet a kulcstartó nevére.  A következő lépések elvégzéséhez szüksége lesz egy Azure Key Vault tárolt *kulcsra* is.  Tárolja a meglévő kulcsot az előző lépésekben létrehozott Key Vaulton, vagy [hozzon létre egy új kulcsot][key-vault-generate] , és cserélje le az alábbi *myKeyName* a kulcs nevével.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>A DiskEncryptionSet hozzáférésének engedélyezése a Key vaulthoz

Használja az előző lépésekben létrehozott DiskEncryptionSet és erőforráscsoportokat, és adja meg a DiskEncryptionSet-erőforráshoz való hozzáférést a Azure Key Vaulthoz.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Új AK-fürt létrehozása és az operációsrendszer-lemez titkosítása

Hozzon létre egy **új erőforráscsoportot** és egy AK-fürtöt, majd használja a kulcsot az operációsrendszer-lemez titkosításához. Az ügyfél által felügyelt kulcsok csak a 1,17-nél nagyobb Kubernetes-verziókban támogatottak. 

> [!IMPORTANT]
> Győződjön meg arról, hogy létrehoz egy új erőforrások-csoportot az AK-fürthöz

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Ha új csomópont-készleteket ad hozzá a fent létrehozott fürthöz, a létrehozás során megadott ügyfél által felügyelt kulcs az operációsrendszer-lemez titkosítására szolgál.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Az AK-fürt adatlemezének titkosítása (nem kötelező)
Az operációs rendszer lemezének titkosítási kulcsa az adatlemez titkosítására szolgál, ha a kulcs nincs megadva a v 1.17.2 található adatlemezhez, és az AK-adatlemezeket is titkosíthatja a többi kulccsal.

> [!IMPORTANT]
> Győződjön meg arról, hogy megfelelő AK-beli hitelesítő adatokkal rendelkezik. A felügyelt identitásnak közreműködői hozzáféréssel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a diskencryptionset telepítve van. Ellenkező esetben hibaüzenet jelenik meg, amely arra utal, hogy a felügyelt identitásnak nincs engedélye.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Hozzon létre egy **byok-Azure-Disk. YAML** nevű fájlt, amely az alábbi információkat tartalmazza.  Cserélje le az értékeket a myAzureSubscriptionId, a myResourceGroup és a myDiskEncrptionSetName értékekre, és alkalmazza a YAML.  Ügyeljen arra, hogy az erőforráscsoportot használja, ahol a DiskEncryptionSet telepítve van.  Ha a Azure Cloud Shell használja, akkor ez a fájl a VI vagy a nano használatával hozható létre, mintha virtuális vagy fizikai rendszeren dolgozik:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Ezután futtassa ezt az üzembe helyezést az AK-fürtben:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Következő lépések

[Az AK-fürtök biztonságával kapcsolatos ajánlott eljárások][best-practices-security] áttekintése

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions