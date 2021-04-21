---
title: Megosztás manuális Azure Files létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre manuálisan egy kötetet Azure Files használatával több egyidejű podhoz a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776087"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Kötet manuális létrehozása és használata Azure Files megosztással Azure Kubernetes Service (AKS)

A tárolóalapú alkalmazásoknak gyakran kell hozzáférniük az adatokhoz, és egy külső adatkötetben meg kellőrzeni az adatokat. Ha több pod egyidejű hozzáférésre van szüksége ugyananhoz a tárolókötethez, a Azure Files [SMB][smb-overview]protokollal csatlakozhat. Ez a cikk bemutatja, hogyan hozhat létre manuálisan egy Azure Files-megosztást, és hogyan csatolhatja azt egy podhoz az AKS-ban.

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Mielőtt kubernetes Azure Files kötetként használhat egy tárfiókot, létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. Az alábbi parancsok létrehoznak egy *myAKSShare* nevű erőforráscsoportot, egy tárfiókot és egy *aksshare nevű fájlmegosztást:*

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

Jegyezze fel a szkript kimenetének végén látható tárfióknevet és -kulcsot. Ezekre az értékekre akkor van szükség, amikor az alábbi lépések valamelyikében létrehozza a Kubernetes-kötetet.

## <a name="create-a-kubernetes-secret"></a>Kubernetes titkos gombra való létrehozása

A Kubernetesnek hitelesítő adatokra van szüksége az előző lépésben létrehozott fájlmegosztás eléréséhez. Ezek a hitelesítő adatok egy [Kubernetes][kubernetes-secret]titkos beli titkos adatokat tartalmaznak, amelyre a Kubernetes-podok létrehozásakor hivatkoznak.

A titkos `kubectl create secret` gombra a paranccsal hozhat létre. Az alábbi példa létrehoz egy *azure-secret* nevű megosztott kulcsot, és feltölti az előző lépésben létrehozott *azurestorageaccountname* és *azurestorageaccountkey* adatokat. Meglévő Azure Storage-fiók használatának érdekében adja meg a fiók nevét és kulcsát.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Fájlmegosztás csatlakoztatása beágyazott kötetként
> Megjegyzés: az 1.18.15- és 1.19.7-es, 1.20.2-es és 1.21.0-s értéktől kezdődően a beágyazott kötetben található titkos névtér csak névtérként adható meg, más titkos névtér megadásához használja helyette az alábbi állandó kötetes `azureFile` `default` példát.

Az adattároló Azure Files a podban való csatlakoztatáshoz konfigurálja a kötetet a tároló specifikációi között. Hozzon létre egy nevű új `azure-files-pod.yaml` fájlt az alábbi tartalommal. Ha módosította a Fájlok megosztás vagy titkos kulcs nevét, frissítse a *shareName* és *a secretName nevet.* Szükség esetén frissítse a fájlt, amely az az elérési út, ahol a fájlok megosztása `mountPath` csatlakoztatva van a podhoz. Windows Server-tárolók esetén adjon meg egy *mountPath útvonalat* a Windows elérésiút-konvenciójának (például *"D:" ) használatával.*

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

A pod `kubectl` létrehozásához használja az parancsot.

```console
kubectl apply -f azure-files-pod.yaml
```

Most már van egy futó podja, amely Azure Files *az /mnt/azure hez csatlakoztatva.* A használatával `kubectl describe pod mypod` ellenőrizheti, hogy a megosztás sikeresen csatlakoztatva van-e. Az alábbi rövid példakimenet a tárolóhoz csatlakoztatott kötetet mutatja:

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

## <a name="mount-file-share-as-an-persistent-volume"></a>Fájlmegosztás csatlakoztatása állandó kötetként
 - Csatlakoztatási beállítások

A *fileMode* és *a dirMode* alapértelmezett értéke *0777* a Kubernetes 1.15-ös és újabb verzióiban. Az alábbi példa a *0755-öt állítja be* a *PersistentVolume objektumon:*

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

A csatlakoztatási beállítások frissítéséhez hozzon létre egy *azurefile-mount-options-pv.yaml fájlt* egy *PersistentVolume lal.* Például:

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

Hozzon létre *egy azurefile-mount-options-pvc.yaml fájlt* egy *PersistentVolumeClaim résszel,* amely a *PersistentVolume et használja.* Például:

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

Használja `kubectl` a parancsokat a *PersistentVolume* és *PersistentVolumeClaim létrehozásához.*

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Ellenőrizze, *hogy a PersistentVolumeClaim* létrejött-e, és hogy a *PersistentVolume -hez van-e kötve.*

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Frissítse a tároló specifikációját a *PersistentVolumeClaim hivatkozáshoz,* és frissítse a podot. Például:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való tároláshoz és biztonsági [mentéshez.][operator-best-practices-storage]

Az AKS-fürtök és a Azure Files a [Kubernetes][kubernetes-files]beépülő modul for Azure Files.

A tárolásiosztály-paraméterekért [lásd: Static Provision(bring your own file share) (Statikus kiépítés (saját fájlmegosztás létrehozása)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share)).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
