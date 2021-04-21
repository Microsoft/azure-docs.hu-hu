---
title: Állandó kötet jogcímek titkosítása ügyfél által felügyelt kulccsal (CMK) a Azure Red Hat OpenShift (ARO)
description: Saját kulcs (BYOK) / Ügyfél által felügyelt kulcs (CMK) üzembe helyezési útmutatója a Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: titkosítás, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783520"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Állandó kötet jogcímek titkosítása ügyfél által felügyelt kulccsal (CMK) a Azure Red Hat OpenShift (előzetes verzió)

Az Azure Storage kiszolgálóoldali titkosítással [](../storage/common/storage-service-encryption.md) (SSE) titkosítja automatikusan az adatokat, amikor megőrzik őket a felhőben. Alapértelmezés szerint az adatok titkosítása a Microsoft platform által felügyelt kulcsokkal történik. A titkosítási kulcsok további szabályozása érdekében saját, ügyfél által felügyelt kulcsokat is meg lehet majd adatokat titkosítani a Azure Red Hat OpenShift fürtökben.

> [!NOTE]
> Jelenleg csak az állandó ARO-kötetek ügyfél által kezelt kulcsokkal való titkosítása támogatott. Ez a szolgáltatás jelenleg nem érhető el a fő vagy munkavégző csomópont operációsrendszer-lemezei számára.

> [!IMPORTANT]
> Az ARO előzetes verziójú funkciói önkiszolgáló, lehetőség szerint érhetők el. Az előzetes verziók az "adott időben" és "rendelkezésre állóként" érhetők el, és ki vannak zárva a szolgáltatásiszint-szerződésekből és a korlátozott jótállásból. Az ARO előzetes verzióit az ügyfélszolgálat az elérhető legjobb megoldásokkal részben lefedi. Ezért ezek a funkciók nem éles környezetben használhatók.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk a következőt feltételezi:

* Az OpenShift 4.4-es (vagy újabb) verziójában már van egy meglévő ARO-fürt.

* Telepítve van az **oc** OpenShift parancssori eszköz, a base64 (a coreutils része) és az **az** Azure CLI.

* Az oc használatával globális fürt-rendszergazdai felhasználóként (kubeadmin) jelentkezik be az ARO-fürtbe. 

* Az **az** használatával van bejelentkezve az Azure CLI-be, és rendelkezik egy olyan fiókkal, amely jogosult közreműködői hozzáférést engedélyezni ugyanabban az előfizetésben, mint az ARO-fürt.

## <a name="limitations"></a>Korlátozások

* Az ügyfél által felügyelt kulcstitkosítás rendelkezésre állása régióspecifikus. Egy adott Azure-régió állapotának ellenőrzéséhez tekintse meg az [Azure-régiókat.][supported-regions]
* Ha szeretné használni a ultralemezek, először engedélyeznie kell őket az előfizetésében, mielőtt hozzá kezd.

## <a name="declare-cluster--encryption-variables"></a>Fürt titkosítási & deklarálva
Az alábbi változókat az önnek megfelelő ARO-fürtre kell konfigurálnia, amelyben engedélyezni szeretné az ügyfél által felügyelt titkosítási kulcsokat:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Az előfizetés azonosítójának beszerzése
Az Azure-előfizetés azonosítóját többször is használja a CMK konfigurációjában. Szerezze be, és tárolja változóként:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Új Azure Key Vault létrehozása
A Azure Key Vault tárolópéldányt kell használnia. Hozzon létre egy új Key Vault engedélyezett végleges kiürítés elleni védelemmel. Ezután hozzon létre egy új kulcsot a tárolóban a saját egyéni kulcsának tárolására:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure Disk Encryption-készlet létrehozása

Az Azure Disk Encryption Set szolgál referenciapontként az ARO lemezei számára. Csatlakozik az előző Azure Key Vault létrehozott kulcshoz, és az ügyfél által felügyelt kulcsokat fog lekért helyről.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Hozzáférés megadása a lemeztitkosítási készlet számára Key Vault
Használja az előző lépésekben létrehozott lemeztitkosítási készletet, és adjon hozzáférést a lemeztitkosítási készletnek a Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>A szerepkör-hozzárendeléshez szükséges egyéb kiosztási adatok beszerzése
Engedélyezni kell, hogy az ARO-fürt a lemeztitkosítási készlettel titkosítsa az ARO-fürt állandó kötet-jogcímeit (PVC-ket). Ehhez egy új felügyeltszolgáltatás-identitást (MSI) hozunk létre. Egyéb engedélyeket is beállítunk az ARO MSI-hez és a lemeztitkosítási készlethez.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>A CMK-titkosításhoz szükséges egyéb szerepkör-hozzárendelések megvalósítása
Alkalmazza a szükséges szerepkör-hozzárendeléseket az előző lépésben lekért változók használatával:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>K8s Storage-osztály létrehozása a titkosított Prémium & ultralemezek számára
Hozzon létre tárolóosztályokat a cmk-hez a Premium_LRS és UltraSSD_LRS számára:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Ezután futtassa ezt az üzembe helyezést az ARO-fürtben a tárolóosztály konfigurációjának alkalmazáshoz:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Titkosítás tesztelése ügyfél által kezelt kulcsokkal (nem kötelező)
Annak ellenőrzéséhez, hogy a fürt felhasználó által felügyelt kulcsot használ-e a SSL-titkosításhoz, létrehozunk egy állandó kötetigénylést az új tárolóosztály használatával. Az alábbi kódrészlet egy podot hoz létre, és prémium szintű lemezek használatával csatlakoztat egy állandó kötetigénylést.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>A tesztpod konfigurációs fájljának alkalmazása (nem kötelező)
Hajtsa végre az alábbi parancsokat a teszt podkonfiguráció alkalmazáshoz, és adja vissza az új állandó kötet jogcím UID-ját. A rendszer az UID használatával ellenőrzi, hogy a lemez titkosítása a CMK használatával történt-e.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Előfordulhat, hogy a szerepkör-hozzárendelések alkalmazása kismát mértékben késhet a Azure Active Directory. Az utasítások végrehajtása sebességétől függően előfordulhat, hogy a "Teljes Azure-lemeznév meghatározása" parancs nem fog sikerülni. Ha ez történik, tekintse át az **oc describe mypod-with-cmk-encryption-pvc** kimenetét, és győződjön meg arról, hogy a lemez sikeresen ki lett építve. Ha a szerepkör-hozzárendelés propagálása még  nem fejeződött be, előfordulhat, hogy törölnie kell, majd újra alkalmaznia kell a podot & YAML-fájlban.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Ellenőrizze, hogy a SSL-lemez az "EncryptionAtRestWithCustomerKey" beállítással van-e konfigurálva (nem kötelező)
A podnak létre kell hoznia egy állandó kötetigénylést, amely a CMK-tárolóosztályra hivatkozik. A következő parancs futtatásával ellenőrizheti, hogy a RENDSZER-lemezkép a várt módon lett-e telepítve:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions