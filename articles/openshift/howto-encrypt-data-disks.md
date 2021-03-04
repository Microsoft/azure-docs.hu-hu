---
title: Az állandó mennyiségi jogcímek titkosítása egy ügyfél által felügyelt kulccsal (CMK) az Azure Red Hat OpenShift (ARO)
description: Saját kulcs használata (BYOK)/ügyfél által felügyelt kulcs (CMK) üzembe helyezési utasítások az Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: titkosítás, byok, ARO, openshift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054782"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Az állandó mennyiségi jogcímek titkosítása ügyfél által felügyelt kulccsal (CMK) az Azure Red Hat OpenShift (ARO) (előzetes verzió)

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint a rendszer az operációs rendszert és az adatlemezeket tartalmazó Microsoft platform által felügyelt kulcsokkal titkosítja az adatvédelmet. A titkosítási kulcsok részletesebb szabályozásához megadhatja az ügyfél által felügyelt kulcsokat az Azure Red Hat OpenShift-fürtökön tárolt adattitkosításhoz.

> [!NOTE]
> Ebben a szakaszban a támogatás csak az ARO állandó kötetek titkosítására használható az ügyfél által felügyelt kulcsokkal. Ez a funkció nem érhető el az operációs rendszer lemezei számára.

> [!IMPORTANT]
> Az ARO előzetes verziójának funkciói önkiszolgáló, választható módon érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatási szintű szerződésekből és a korlátozott jótállásból. Az ARO-előnézetek részlegesen az ügyfélszolgálat által támogatottak. Ezért ezek a szolgáltatások éles használatra nem használhatók.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk azt feltételezi, hogy:

* Már van egy meglévő ARO-fürt a 4,4-es OpenShift-verzióban (vagy nagyobb).

* A "oC" OpenShift parancssori eszköz, Base64 (a Core utils része) és az "az" Azure CLI telepítve van.

* Az ARO-fürtre az *oC* -ot globális fürt-rendszergazda felhasználóként (kubeadmin) kell bejelentkeznie.

* Be van jelentkezve az Azure CLI *-vel az az-* val egy olyan fiókkal, amely jogosult a "közreműködő" hozzáférés megadására ugyanabban az előfizetésben, mint az ARO-fürtöt.

## <a name="limitations"></a>Korlátozások

* Az ügyfél által felügyelt kulcs titkosításának rendelkezésre állása régióra jellemző. Egy adott Azure-régió állapotának megtekintéséhez tekintse meg az [Azure-régiókat][supported-regions].
* Ha Ultra-lemezeket használ, az első lépések előtt engedélyezze az előfizetéshez tartozó ultravékony lemezeket.

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault példány létrehozása
A kulcsok tárolására Azure Key Vault példányt kell használni. Hozzon létre egy új Key Vault a kiürítési védelemmel, és engedélyezze a helyreállítható törlést. Ezután hozzon létre egy új kulcsot a tárolóban a saját egyéni kulcsának tárolásához:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure Disk Encryption-készlet létrehozása

Az Azure Disk Encryption-készletet az ARO lemezek hivatkozási pontjaként használják. Csatlakoztatva van az előző lépésben létrehozott Azure Key Vaulthoz, és lekéri az ügyfél által felügyelt kulcsokat az adott helyről.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Adja meg a lemez titkosítási készletének hozzáférését Key Vault
Használja az előző lépésekben létrehozott lemezes titkosítási készletet, és adja meg a lemez titkosítási készletének hozzáférését Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>A szerepkör-hozzárendelésekhez szükséges egyéb azonosítók beszerzése
Lehetővé kell tenni az ARO-fürt számára, hogy a lemez titkosítási készletét használja az állandó mennyiségi jogcímek (PVC-EK) az ARO-fürtben való titkosításához. Ehhez létre kell hozni egy új Managed Service Identity (MSI). Az ARO MSI-hez és a lemezes titkosítási készlethez is beállíthat más engedélyeket.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>A BYOK/CMK titkosításhoz szükséges egyéb szerepkör-hozzárendelések implementálása
Alkalmazza a szükséges szerepkör-hozzárendeléseket az előző lépésben beszerzett változók használatával:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>K8s tárolási osztály létrehozása a titkosított prémium & Ultra-lemezek számára (opcionális)
BYOK/CMK használandó tárolási osztályok előállítása Premium_LRS és UltraSSD_LRS lemezekhez:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>A tárolási osztály konfigurációjának beállítása
Helyettesítse be az ARO-fürt egyedi változóit a két tárolási osztály konfigurációs fájljába:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Ezután futtassa ezt az üzemelő példányt az ARO-fürtön a tárolási osztály konfigurációjának alkalmazásához:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Titkosítás tesztelése az ügyfél által felügyelt kulcsokkal
Annak vizsgálatához, hogy a fürt használ-e egy ügyfél által felügyelt kulcsot a PVC-titkosításhoz, egy állandó mennyiségi jogcímet hozunk létre a megfelelő tárolási osztály használatával. Az alábbi kódrészlet létrehoz egy Pod-t, és állandó mennyiségi jogcímet csatlakoztat a standard lemezekkel
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
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
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>A teszt Pod konfigurációs fájl alkalmazása
Hajtsa végre az alábbi parancsokat a test Pod konfigurációjának alkalmazásához, és az új állandó mennyiségi jogcím UID azonosítójának visszaküldéséhez. A rendszer a UID azonosítóval ellenőrzi, hogy a lemez titkosítva van-e a BYOK/CMK használatával.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Ellenőrizze, hogy a PVC-lemez "EncryptionAtRestWithCustomerKey"-vel van-e konfigurálva 
A pod olyan állandó mennyiségi jogcímet hoz létre, amely a BYOK/CMK tárolási osztályra hivatkozik. A következő parancs futtatása ellenőrzi, hogy a PVC a várt módon lett-e telepítve:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Következő lépések

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

