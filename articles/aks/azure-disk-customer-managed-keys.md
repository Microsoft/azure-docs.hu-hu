---
title: Ügyfél által felügyelt kulcs használata Az Azure-lemezek titkosítása Azure Kubernetes Service (AKS)
description: Saját kulcsok használata (BYOK) az AKS operációs rendszer és az adatlemezek titkosításához.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776190"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Saját kulcsok használata (BYOK) Azure-lemezekkel az Azure Kubernetes Service (AKS)

Az Azure Storage egy tárfiókban tárolt összes adatot titkosítja. Alapértelmezés szerint az adatok titkosítása Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához ügyfél által kezelt kulcsokat is használhat az AKS-fürtök operációs rendszeréhez és adatlemezéhez használt titkosításhoz. További információ az ügyfél által kezelt [kulcsokról Linux és][customer-managed-keys-linux] [Windows rendszeren.][customer-managed-keys-windows]

## <a name="limitations"></a>Korlátozások
* Az adatlemez titkosításának támogatása a Kubernetes 1.17-es vagy újabb verzióját futtató AKS-fürtökre korlátozódik.
* Az operációs rendszer és az adatlemez ügyfél által kezelt kulcsokkal való titkosítása csak AKS-fürt létrehozásakor engedélyezhető.

## <a name="prerequisites"></a>Előfeltételek
* Engedélyeznie kell a szoftveres törlés és végleges törlés elleni védelmet a *Azure Key Vault,* amikor Key Vault felügyelt lemezek titkosításához használja.
* Az Azure CLI 2.11.1-es vagy újabb verziójára lesz szüksége.

## <a name="create-an-azure-key-vault-instance"></a>Új Azure Key Vault létrehozása

Használjon egy Azure Key Vault-példányt a kulcsok tárolására.  Igény szerint használhatja a Azure Portal ügyfél által kezelt kulcsok [konfigurálás Azure Key Vault][byok-azure-portal]

Hozzon létre egy *új erőforráscsoportot,* majd hozzon létre *egy új* Key Vault, és engedélyezze a törlés és végleges törlés elleni védelmet.  Győződjön meg arról, hogy minden parancshoz ugyanazt a régió- és erőforráscsoport-nevet használja.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet példányának létrehozása

Cserélje *le a myKeyVaultName* helyére a kulcstartó nevét.  A következő lépések befejezéséhez *szüksége* lesz egy, a Azure Key Vault tárolt kulcsra is.  Tárolja a meglévő kulcsot az előző Key Vault létrehozott kulcsban, vagy hozzon létre egy új kulcsot, és cserélje le az alábbi *myKeyName* helyére [a][key-vault-generate] kulcs nevét.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Hozzáférés megadása a DiskEncryptionSet számára a Key Vaulthoz

Használja az előző lépésekben létrehozott DiskEncryptionSet és erőforráscsoportokat, és adjon hozzáférést a DiskEncryptionSet erőforrásnak a Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Új AKS-fürt létrehozása és az operációsrendszer-lemez titkosítása

Hozzon **létre egy új erőforráscsoportot** és AKS-fürtöt, majd használja a kulcsot az operációsrendszer-lemez titkosításához. Az ügyfél által felügyelt kulcsok csak az 1.17-esnél újabb Kubernetes-verziókban támogatottak. 

> [!IMPORTANT]
> Győződjön meg arról, hogy új elosztási csoportot hoz létre az AKS-fürthöz

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Amikor új csomópontkészleteket ad hozzá a fent létrehozott fürthöz, a rendszer a létrehozás során megadott, felhasználó által felügyelt kulcsot használja az operációsrendszer-lemez titkosításához.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Az AKS-fürt adatlemezének titkosítása (nem kötelező)
Ha az 1.17.2-es verzióból származó adatlemezhez nincs megtéve kulcs, akkor az operációsrendszer-lemeztitkosítási kulccsal titkosíthatja az adatlemezeket, és az AKS-adatlemezeket más kulcsokkal is titkosíthatja.

> [!IMPORTANT]
> Győződjön meg arról, hogy a megfelelő AKS-hitelesítő adatokkal kell rendelkeznie. A felügyelt identitásnak közreműködői hozzáféréssel kell rendelkezik ahhoz az erőforráscsoporthoz, amelyben a diskencryptionset üzembe van helyezni. Ellenkező esetben hibaüzenetet kap, amely szerint a felügyelt identitás nem rendelkezik engedélyekkel.

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

Hozzon létre egy **byok-azure-disk.yaml nevű fájlt,** amely a következő információkat tartalmazza.  Cserélje le a myAzureSubscriptionId, myResourceGroup és myDiskEncrptionSetName értékeket a saját értékeire, és alkalmazza a yaml-fájlt.  Ügyeljen arra, hogy azt az erőforráscsoportot használja, amelyben a DiskEncryptionSet üzembe van helyezni.  Ha a virtuális Azure Cloud Shell, ez a fájl a vi vagy a nano használatával, virtuális vagy fizikai rendszeren való munkavégzéshez használható:

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
Ezután futtassa ezt az üzembe helyezést az AKS-fürtben:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Következő lépések

Az [AKS-fürtök biztonságával kapcsolatos ajánlott eljárások áttekintése][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions