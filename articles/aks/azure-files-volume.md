---
title: Azure Files megosztás manuális létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre manuálisan olyan köteteket, Azure Files amelyekkel több párhuzamos hüvely használható az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 4e009c5de2e24c1b0bd94fb4c11b0c52a3bc378d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609073"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Azure Files megosztással rendelkező kötet manuális létrehozása és használata az Azure Kubernetes szolgáltatásban (ak)

A tároló-alapú alkalmazásoknak gyakran kell elérniük és megőrizniük az adatmennyiséget egy külső adatköteten. Ha több hüvelynek egyidejű hozzáférésre van szüksége ugyanahhoz a tárolási kötethez, akkor a Azure Files használatával csatlakozhat a [Server Message Block (SMB) protokollal][smb-overview]. Ebből a cikkből megtudhatja, hogyan hozhat létre manuálisan egy Azure Files-megosztást, és hogyan csatlakoztathatja egy Pod-hez az AK-ban.

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

A Azure Files Kubernetes-kötetként való használata előtt létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. A következő parancsok létrehoznak egy *myAKSShare* nevű erőforráscsoportot, egy Storage-fiókot és egy *aksshare* nevű fájlmegosztás:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Jegyezze fel a parancsfájl kimenetének végén látható Storage-fiók nevét és kulcsát. Ezek az értékek akkor szükségesek, ha a Kubernetes-kötetet a következő lépések egyikében hozza létre.

## <a name="create-a-kubernetes-secret"></a>Kubernetes titkos kód létrehozása

A Kubernetes hitelesítő adatokra van szüksége az előző lépésben létrehozott fájlmegosztás eléréséhez. Ezeket a hitelesítő adatokat egy [Kubernetes titkos kulcs][kubernetes-secret]tárolja, amelyet a rendszer a Kubernetes Pod létrehozásakor hivatkozik.

A `kubectl create secret` parancs használatával hozza létre a titkos kulcsot. Az alábbi példa létrehoz egy *Azure-Secret* nevű megosztott nevet, és feltölti a *azurestorageaccountname* és a *azurestorageaccountkey* az előző lépésből. Meglévő Azure Storage-fiók használatához adja meg a fiók nevét és kulcsát.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Fájlmegosztás csatlakoztatása beágyazott kötetként
> Megjegyzés: az 1.18.15, a 1.19.7, a 1.20.2, a 1.21.0 és a beágyazott kötet titkos névterét `azureFile` csak `default` névtérként lehet beállítani, ha másik titkos névteret szeretne megadni, használja inkább az alábbi állandó kötetet.

Ha a Azure Files-megosztást a pod-ba szeretné csatlakoztatni, konfigurálja a kötetet a tároló spec-ban. Hozzon létre egy nevű új fájlt `azure-files-pod.yaml` a következő tartalommal. Ha módosította a fájlmegosztás vagy a titkos kód nevét, frissítse a *megosztásnév* és a *secretName*. Ha szükséges, frissítse a `mountPath` -t, amely a fájlok megosztásának elérési útja a pod-ban. Windows Server-tárolók esetén a Windows PATH Convention (például *'d:*) használatával válasszon egy *mountPath* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

A `kubectl` parancs használatával hozza létre a pod-t.

```console
kubectl apply -f azure-files-pod.yaml
```

Most már rendelkezik egy futó Pod Azure Files-megosztással, amely a */mnt/Azure*-ben van csatlakoztatva. `kubectl describe pod mypod`A használatával ellenőrizheti, hogy a megosztás sikeresen csatlakoztatva van-e. A következő összefoglalt példa kimenet a tárolóban csatlakoztatott kötetet mutatja:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Fájl megosztásának állandó kötetként való csatlakoztatása
 - Csatlakoztatási beállítások

A *fileMode* és a *dirMode* alapértelmezett értéke *0777* a 1,15-es és újabb verziójú Kubernetes esetében. Az alábbi példa a *0755* -es készletet állítja be a *PersistentVolume* objektumon:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

A csatlakoztatási beállítások frissítéséhez hozzon létre egy *azurefile-mount-options-PV. YAML* fájlt egy *PersistentVolume*. Például:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Hozzon létre egy *azurefile-mount-options-PVC. YAML* fájlt egy olyan *PersistentVolumeClaim* , amely a *PersistentVolume* használja. Például:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

A `kubectl` *PersistentVolume* és a *PersistentVolumeClaim* létrehozásához használja a parancsokat.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Ellenőrizze, hogy létrejött-e a *PersistentVolumeClaim* , és kötődik-e a *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

A tároló specifikációjának frissítésével hivatkozhat a *PersistentVolumeClaim* , és frissítheti a pod-t. Például:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

Az AK-fürtökkel kapcsolatos további információk a Azure Fileskal együttműködve: [Azure Files Kubernetes beépülő modulja][kubernetes-files].

A tárolási osztályok paramétereinek megtekintéséhez lásd: [statikus kiépítés (saját fájlmegosztás használata)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
