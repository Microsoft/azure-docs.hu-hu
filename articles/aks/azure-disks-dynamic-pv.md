---
title: Dinamikusan hozhat létre egy lemez kötet több podok Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan dinamikusan hozhat létre egy tartós kötet a több egyidejű podok Azure Kubernetes Service (AKS) segítségével az Azure disks szolgáltatással
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 735be71faecb9882b13f6f536d43715139d0f4db
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65071984"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dinamikusan létrehozása és a egy tartós kötet használata Azure-lemezek az Azure Kubernetes Service (AKS)

Tartós kötet egy Kubernetes-podok való használatra vett tárolási részét jelöli. Tartós kötet egy vagy több podok által használható, és hogy statikusan vagy dinamikusan bővítheti. Ez a cikk bemutatja, hogyan állandó kötetek dinamikusan létrehozására az Azure-lemezek egy podot az Azure Kubernetes Service (AKS)-fürt általi használatra.

> [!NOTE]
> Egy Azure-lemez csak csatlakoztathatók a *hozzáférési mód* típus *ReadWriteOnce*, amely lehetővé teszi az csak egy podot az aks-ben. Ha a tartós kötet megosztása több podok van szüksége, használja a [Azure Files][azure-files-pvc].

A Kubernetes-köteteken további információkért lásd: [tárolási lehetőségek az aks-ben alkalmazásokhoz][concepts-storage].

## <a name="before-you-begin"></a>Előzetes teendők

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="built-in-storage-classes"></a>A beépített storage osztályai

Tárolási osztály hogyan tárolási egység dinamikusan jön létre egy állandó mennyiségi meghatározására szolgál. A Kubernetes storage osztályai további információkért lásd: [Kubernetes Storage osztályai][kubernetes-storage-classes].

Minden egyes AKS-fürt tartalmazza a két előre létrehozott storage osztályai, is konfigurál az Azure-lemezek használata:

* A *alapértelmezett* tárolási osztály egy az Azure standard disk építi ki.
    * Standard szintű storage meghajtókra, és költséghatékony tárolási megoldás kínál a nagy teljesítményű ugyanakkor továbbra is. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.
* A *felügyelt prémium szintű* tárolási osztály egy prémium szintű Azure-lemez építi ki.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha az AKS-csomópontok a fürtben használni a prémium szintű storage, válassza ki a *felügyelt prémium szintű* osztály.

Használja a [kubectl get sc] [ kubectl-get] paranccsal tekintheti meg az előre létrehozott storage osztályai. A következő példa bemutatja a storage osztályai és a egy AKS-fürtön belül elérhető előzetes létrehozása:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Tartós kötet jogcímek megadott GiB-ban, de az Azure managed disks számlázása Termékváltozat által egy adott méretet. Ezen SKU-k és közé eső 32GiB S4 vagy P4 szintű lemezek 32TiB S80 vagy P80 lemezek (az előzetes verzió). Az átviteli sebesség és IOPS-teljesítmény egy prémium szintű felügyelt lemez attól függ, a mind a Termékváltozat és a példány mérete az AKS-fürt csomópontja. További információkért lásd: [díjszabás és a felügyelt lemezek teljesítményének][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tartós kötet jogcím létrehozása

Tartós kötet jogcím (PVC) automatikusan üzembe egy tárolási osztály alapján történik. Ebben az esetben egy PVC egyikét használhatja az előre létrehozott storage osztályai, hozzon létre egy standard vagy prémium szintű Azure felügyelt lemezt.

Hozzon létre egy fájlt `azure-premium.yaml`, és másolja a következő jegyzékfájlban. A jogcím-kérelmek nevű lemez `azure-managed-disk` , amely *5 GB-os* méretű *ReadWriteOnce* hozzáférést. A *felügyelt prémium szintű* tárolási osztály van megadva, a tárolási osztály.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Hozzon létre egy lemezt, amely a standard szintű tárolást használ, használja a `storageClassName: default` helyett *felügyelt prémium szintű*.

Hozzon létre a tartós kötet jogcímet a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a *azure-premium.yaml* fájlt:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Használja a tartós kötet

Ha a tartós kötet jogcím létrejött, és sikeresen üzembe lett helyezve a lemez lehet létrehozni egy pod a lemez elérésére. A következő jegyzékfájl hoz létre egy alapszintű NGINX-pod a tartós kötet jogcím nevű használó *azure managed disk* csatlakoztatása az Azure-lemez elérési útja a `/mnt/azure`.

Hozzon létre egy fájlt `azure-pvc-disk.yaml`, és másolja a következő jegyzékfájlban.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
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
        claimName: azure-managed-disk
```

A pod-létrehozása a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, az alábbi példában látható módon:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Az Azure csatlakoztatott lemezzel most már rendelkezik egy futó pod a `/mnt/azure` könyvtár. Ez a konfiguráció látható, amikor a pod-n keresztül vizsgálatával `kubectl describe pod mypod`, ahogyan az a következő sűrített példához:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Készítsen biztonsági másolatot egy tartós kötet

Biztonsági másolatot készíteni a tartós kötet adatait, hajtsa végre a kötet a felügyelt lemez pillanatképét. Ezután használhatja ezt a pillanatfelvételt a visszaállított lemez létrehozása és csatlakoztatása a podok, hogy az adatok visszaállítása.

Először kérje le a kötet nevét a `kubectl get pvc` parancsot, mint például a nevű permanens *azure managed disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

A kötet neve képezi a mögöttes Azure-lemez nevét. A lemez azonosítójának a lekérdezés [az Lemezlista] [ az-disk-list] , és adja meg a PVC kötet neve, az alábbi példában látható módon:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Pillanatkép lemez létrehozásához használja a Lemezazonosítót [az pillanatkép létrehozása][az-snapshot-create]. Az alábbi példa létrehoz egy pillanatképet nevű *pvcSnapshot* ugyanabban az erőforráscsoportban, az AKS-fürtöt a (*MC_myResourceGroup_myAKSCluster_eastus*). Ha pillanatképeket létrehozni, és az AKS-fürt nem rendelkezik hozzáféréssel az erőforrás-csoportok a lemezek visszaállítása engedély problémák merülhetnek fel.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően a pillanatkép létrehozása néhány percig is eltarthat.

## <a name="restore-and-use-a-snapshot"></a>Visszaállítás és a egy pillanatkép használata

Állítsa vissza a lemezt, és a egy Kubernetes-podok használhatja, használja a pillanatkép forrásként lemez létrehozásakor [az lemez létrehozása][az-disk-create]. Ez a művelet megőrzi az eredeti erőforrás, ha szeretne hozzáférni az eredeti adatok pillanatképet. A következő példában létrehozunk egy nevű lemez *pvcRestored* a nevű pillanatkép *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

A visszaállított lemez használata a pod, adja meg a lemez Azonosítóját a jegyzékfájlban. A lemez Azonosítójának lekéréséhez a [az disk show] [ az-disk-show] parancsot. Az alábbi példa lekéri a Lemezazonosítót az *pvcRestored* az előző lépésben létrehozott:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Nevű pod jegyzék létrehozásához `azure-restored.yaml` , és adja meg a lemezt az előző lépésben lekért URI. A következő példában létrehozunk egy alapszintű NGINX-webkiszolgálót, és a visszaállított lemez kötet fürtkötetként */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

A pod-létrehozása a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, az alábbi példában látható módon:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Használhat `kubectl describe pod mypodrestored` a pod, például a következő sűrített példához, amely megjeleníti a rendszerkötet-információkat a részletek megtekintéséhez:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>További lépések

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a storage és az aks-ben biztonsági mentések][operator-best-practices-storage].

További tudnivalók a Kubernetes Azure-lemezek használatával állandó köteteket.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul az Azure-lemezek][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
