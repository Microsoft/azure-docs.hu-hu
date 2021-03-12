---
title: Az állandó mennyiségi jogcímek titkosítása egy ügyfél által felügyelt kulccsal (CMK) az Azure Red Hat OpenShift (ARO)
description: Saját kulcs használata (BYOK)/ügyfél által felügyelt kulcs (CMK) üzembe helyezési utasítások az Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: titkosítás, byok, ARO, CMK, openshift, Red Hat
ms.openlocfilehash: fa84096dcc44e668a6cf7ebd0369c6d3631c28d2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555618"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Az állandó mennyiségi jogcímek titkosítása ügyfél által felügyelt kulccsal (CMK) az Azure Red Hat OpenShift (ARO) (előzetes verzió)

Az Azure Storage kiszolgálóoldali titkosítás (SSE) használatával automatikusan [titkosítja](../storage/common/storage-service-encryption.md) az adatait, amikor a felhőben is megmarad. Alapértelmezés szerint az adattitkosítás a Microsoft platform által felügyelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja a saját ügyfelek által felügyelt kulcsokat az Azure Red Hat OpenShift-fürtökön tárolt adattitkosításhoz.

> [!NOTE]
> Ebben a szakaszban a támogatás csak az ARO állandó kötetek titkosítására használható az ügyfél által felügyelt kulcsokkal. Ez a funkció jelenleg nem érhető el a fő-vagy feldolgozói csomópont operációsrendszer-lemezei számára.

> [!IMPORTANT]
> Az ARO előzetes verziójának funkciói önkiszolgáló, választható módon érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatási szintű szerződésekből és a korlátozott jótállásból. Az ARO-előnézetek részlegesen az ügyfélszolgálat által támogatottak. Ezért ezeket a funkciókat nem éles használatra szánták.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk azt feltételezi, hogy:

* Már van egy meglévő ARO-fürt a 4,4-es OpenShift-verzióban (vagy nagyobb).

* Az **oC** OpenShift parancssori eszköz, a Base64 (a coreutils része) és az **Az Azure CLI** telepítve van.

* Az ARO-fürtre a globális fürt – rendszergazda felhasználó (kubeadmin **) használatával van** bejelentkezve.

* A (z) **az az Azure CLI-vel való** beléptetésével egy olyan fiókkal rendelkezik, amely jogosult a közreműködők hozzáférésének megadására az ARO-fürttel megegyező előfizetésben.

## <a name="limitations"></a>Korlátozások

* Az ügyfél által felügyelt kulcs titkosításának rendelkezésre állása régióra jellemző. Egy adott Azure-régió állapotának megtekintéséhez tekintse meg az [Azure-régiókat][supported-regions].
* Ha Ultra-lemezeket szeretne használni, először engedélyeznie kell őket az előfizetésben az első lépések megkezdése előtt.

## <a name="declare-cluster--encryption-variables"></a>Fürt & titkosítási változók deklarálása
Az alábbi változókat úgy kell konfigurálnia, hogy a felhasználó által felügyelt titkosítási kulcsok lehetővé tételéhez használt ARO-fürtön legyenek.
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Előfizetés-azonosító beszerzése
Az Azure-előfizetési azonosítót többször is használják a CMK konfigurációjában. Szerezze be és tárolja változóként:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault példány létrehozása
A kulcsok tárolására Azure Key Vault példányt kell használni. Hozzon létre egy új Key Vault, amelyen engedélyezve van a kiürítési védelem. Ezután hozzon létre egy új kulcsot a tárolóban a saját egyéni kulcsának tárolásához:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Azure Disk Encryption-készlet létrehozása

A rendszer az ARO-ban lévő lemezek hivatkozási pontként használja a Azure Disk Encryption készletet. Csatlakoztatva van az előző lépésben létrehozott Azure Key Vaulthoz, és lekéri az ügyfél által felügyelt kulcsokat az adott helyről.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Adja meg a lemez titkosítási készletének hozzáférését Key Vault
Használja az előző lépésekben létrehozott lemezes titkosítási készletet, és adja meg a lemez titkosítási készletének hozzáférését Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>A szerepkör-hozzárendelésekhez szükséges egyéb azonosítók beszerzése
Lehetővé kell tenni az ARO-fürt számára, hogy a lemez titkosítási készletét használja az állandó mennyiségi jogcímek (PVC-EK) az ARO-fürtben való titkosításához. Ehhez létre kell hozni egy új Managed Service Identity (MSI). Emellett az ARO MSI-hez és a lemezes titkosítási készlethez is meg kell adni egyéb engedélyeket.

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

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>A CMK-titkosításhoz szükséges egyéb szerepkör-hozzárendelések implementálása
Alkalmazza a szükséges szerepkör-hozzárendeléseket az előző lépésben beszerzett változók használatával:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>K8s tárolási osztály létrehozása a titkosított prémium & Ultra-lemezek számára
Premium_LRS-és UltraSSD_LRS-lemezek CMK használandó tárolási osztályok előállítása:

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

Ezután futtassa ezt az üzemelő példányt az ARO-fürtön a tárolási osztály konfigurációjának alkalmazásához:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Titkosítás tesztelése az ügyfél által felügyelt kulcsokkal (nem kötelező)
Annak a megkereséséhez, hogy a fürt a PVC-titkosításhoz használt ügyfél által felügyelt kulcsot használ-e, állandó mennyiségi jogcímet hozunk létre az új tárolási osztály használatával. Az alábbi kódrészlet létrehoz egy Pod-t, és a prémium szintű lemezekkel csatlakoztat egy állandó mennyiségi jogcímet.
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
### <a name="apply-the-test-pod-configuration-file-optional"></a>A teszt Pod konfigurációs fájl alkalmazása (nem kötelező)
Hajtsa végre az alábbi parancsokat a test Pod konfigurációjának alkalmazásához, és az új állandó mennyiségi jogcím UID azonosítójának visszaküldéséhez. A rendszer a UID azonosítóval ellenőrzi, hogy a lemez titkosítva van-e a CMK használatával.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Alkalmanként előfordulhat, hogy a szerepkör-hozzárendelések Azure Active Directoryon belüli alkalmazása némi késéssel jár. Attól függően, hogy milyen sebességgel hajtja végre ezeket az utasításokat, az "a teljes Azure-lemez nevének megállapítása" parancs sikertelen lehet. Ha ez történik, tekintse át az oC kimenetét, és **írja le a PVC mypod-with-CMK-encryption-PVC** -t a lemez sikeres üzembe helyezésének biztosításához. Ha a szerepkör-hozzárendelés propagálása nem fejeződött be, előfordulhat, hogy *törölnie* kell, majd újra *alkalmaznia* kell a pod & PVC-YAML.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Ellenőrizze, hogy a PVC lemez konfigurálva van-e a "EncryptionAtRestWithCustomerKey" (opcionális)
A pod olyan állandó mennyiségi jogcímet hoz létre, amely a CMK tárolási osztályra hivatkozik. A következő parancs futtatása ellenőrzi, hogy a PVC a várt módon lett-e telepítve:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

