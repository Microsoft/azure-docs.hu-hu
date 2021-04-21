---
title: Dinamikusan hozzon létre Azure Files megosztást
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre dinamikusan állandó kötetet Azure Files és több egyidejű podhoz a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f301a01e479d03647bebf7cb042564a258e9250e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776122"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Tartós kötet dinamikus létrehozása és használata Azure-fájlokkal az Azure Kubernetes Service-ben (AKS)

Az állandó kötetek olyan tárolók, amelyek Kubernetes-podokkal való használatra vannak kiépítve. Az állandó köteteket egy vagy több pod is használhatja, és dinamikusan vagy statikusan kiépítheti őket. Ha több pod egyidejű hozzáférésre van szüksége ugyanannak a tárolókötetnek a eléréséhez, Azure Files server [message block (SMB) protokollal csatlakozhat.][smb-overview] Ez a cikk bemutatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást, amelyet több pod használhat egy Azure Kubernetes Service -fürtben.

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-a-storage-class"></a>Tárolóosztály létrehozása

A tárolóosztályok az Azure-fájlmegosztások létrehozási mikéntét határozzák meg. A rendszer automatikusan létrehoz [][node-resource-group] egy tárfiókot a csomópont-erőforráscsoportban az Azure-fájlmegosztásokat tároló storage osztályhoz. Válassza ki a következő [Azure Storage-redundanciát][storage-skus] az *skuName esetében:*

* *Standard_LRS* – standard helyileg redundáns tárolás (LRS)
* *Standard_GRS* – standard georedundáns tárolás (GRS)
* *Standard_ZRS* – standard zónaredundáns tárolás (ZRS)
* *Standard_RAGRS* – standard írási hozzáférésű georedundáns tárolás (RA-GRS)
* *Premium_LRS* – prémium helyileg redundáns tárolás (LRS)
* *Premium_ZRS* – prémium zónaredundáns tárolás (ZRS)

> [!NOTE]
> Azure Files a Prémium szintű tárolást a Kubernetes 1.13-as vagy újabb verzióval futó AKS-fürtökben, a prémium szintű fájlmegosztás minimális díja 100 GB

További információ a kubernetes tárolóosztályokkal kapcsolatban a [Azure Files: Kubernetes Storage Classes ( Kubernetes Storage-osztályok).][kubernetes-storage-classes]

Hozzon létre egy nevű `azure-file-sc.yaml` fájlt, és másolja be az alábbi példajegyzékbe. A csatlakoztatásilehetőségekkel *kapcsolatos további információkért* lásd a Csatlakoztatási [beállítások szakaszt.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Hozza létre a storage osztályt a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímének létrehozása

A perzisztens kötetigénylés (PERSISTENT) a storage class objektum használatával dinamikusan kiépít egy Azure-fájlmegosztást. A következő YAML-lel létrehozhat egy *5 GB* méretű állandó kötet jogcímet *ReadWriteMany hozzáféréssel.* További információ a hozzáférési módokról: [A Kubernetes állandó kötetének dokumentációja.][access-modes]

Most hozzon létre egy nevű fájlt, `azure-file-pvc.yaml` és másolja be a következő YAML-fájlt. Győződjön meg arról, hogy *a storageClassName megegyezik* az előző lépésben létrehozott tárolási osztálysal:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Ha a tárolási *Premium_LRS* termékváltozatát használja, a tároló  minimális értékének *100Gi értékűnek kell lennie.*

Hozza létre az állandó kötet jogcímét a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Ha elkészült, a fájlmegosztás létrejön. A rendszer létrehoz egy Kubernetes titkos adatokat is, amely tartalmazza a kapcsolati adatokat és a hitelesítő adatokat. A [kubectl get paranccsal][kubectl-get] megtekintheti a PVC állapotát:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

A következő YAML létrehoz egy podot, amely a *my-azurefile* állandó kötet jogcímet használja az Azure-fájlmegosztás */mnt/azure elérési úton való csatlakoztatáshoz.* Windows Server-tárolók esetén adjon meg egy *mountPath útvonalat* a Windows elérésiút-konvenciójának (például *"D:" ) használatával.*

Hozzon létre egy nevű `azure-pvc-files.yaml` fájlt, és másolja be a következő YAML-fájlt. Győződjön meg arról, hogy *a claimName (jogcímnév)* megegyezik az előző lépésben létrehozott PVC-vel.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
        claimName: my-azurefile
```

Hozza létre a podot a [kubectl apply paranccsal.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Most már van egy futó podja, Azure Files a */mnt/azure könyvtárban* van csatlakoztatva. Ez a konfiguráció akkor látható, ha a segítségével vizsgálja a `kubectl describe pod mypod` podot. Az alábbi rövid példakimenet a tárolóhoz csatlakoztatott kötetet mutatja:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Csatlakoztatási beállítások

A *fileMode* és *a dirMode* alapértelmezett értéke *0777* a Kubernetes 1.13.0-s és újabb verzióiban. Ha dinamikusan hoz létre állandó kötetet egy tárolóosztálysal, a tárolási osztály objektumán csatlakozási beállításokat is meg lehet adni. Az alábbi példa a *0777-et állítja be:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való tároláshoz és biztonsági [mentéshez.][operator-best-practices-storage]

A tárolóosztály paramétereivel kapcsolatos további információért lásd: [Dinamikus kiépítés.](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision)

További információ az állandó Kubernetes-kötetről a Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/aks#az_aks_show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
