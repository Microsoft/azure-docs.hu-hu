---
title: Azure Disks-kötet dinamikus létrehozása
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre dinamikusan állandó kötetet Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ad51bfdf8c494e763921de880926b839cdb7be62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021639"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Állandó kötet létrehozása és használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)

Az állandó kötet a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. Egy állandó kötetet egy vagy több hüvely is használhat, és dinamikusan vagy statikusan kiépíthető. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan állandó köteteket az Azure-lemezekkel az Azure Kubernetes Service-(ak-) fürtben található egyetlen Pod használatával.

> [!NOTE]
> Az Azure-lemezeket csak a ReadWriteOnce *hozzáférési móddal* lehet csatlakoztatni, ami elérhetővé teszi az AK-ban lévő egyik csomópont számára. Ha egy állandó kötetet több csomóponton kell megosztania, használja a [Azure Files][azure-files-pvc].

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="built-in-storage-classes"></a>Beépített tárolási osztályok

A tárolási osztály segítségével határozható meg, hogy egy adott tárolási egység hogyan legyen dinamikusan létrehozva állandó kötettel. További információ a Kubernetes tárolásával kapcsolatban: [Kubernetes Storage classs][kubernetes-storage-classes].

Mindegyik AK-fürt négy előre létrehozott tárolási osztályt tartalmaz, amelyek közül kettő az Azure-lemezekkel való együttműködésre van konfigurálva:

* Az *alapértelmezett* Storage osztály egy standard SSD Azure-lemezt foglal le.
    * A standard szintű SSD-k a standard szintű SSD-k által támogatottak, és költséghatékony tárterületet biztosítanak, miközben továbbra is megbízható teljesítményt nyújt. 
* A *felügyelt Premium* Storage osztály egy prémium szintű Azure-lemezt foglal le.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha a fürt AK-csomópontjai a Premium Storage-t használják, válassza a *felügyelt prémium* osztályt.
    
Ha az alapértelmezett tárolási osztályok egyikét használja, a kötet mérete nem frissíthető a tárolási osztály létrehozása után. Ahhoz, hogy a tárolási osztály létrehozása után frissíteni lehessen a kötet méretét, adja hozzá a sort az `allowVolumeExpansion: true` egyik alapértelmezett tárolási osztályhoz, vagy létrehozhat saját egyéni tárolási osztályt is. Vegye figyelembe, hogy a PVC méretének csökkentése nem támogatott (az adatvesztés elkerülése érdekében). A meglévő tárolási osztályokat a parancs használatával szerkesztheti `kubectl edit sc` . 

Ha például 4 TiB méretű lemezt szeretne használni, létre kell hoznia egy tárolási osztályt, amely meghatározza, hogy a `cachingmode: None` [lemezes gyorsítótárazás nem támogatott a 4 TIB és nagyobb lemezek esetén](../virtual-machines/premium-storage-performance.md#disk-caching).

További információ a tárolási osztályokról és a saját tárolási osztály létrehozásáról: az [AK-beli alkalmazások tárolási beállításai][storage-class-concepts].

Az előre létrehozott tárolási osztályok megjelenítéséhez használja a [kubectl Get SC][kubectl-get] parancsot. Az alábbi példa egy AK-fürtön belül elérhető, előre létrehozott tárolási osztályokat mutatja be:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Az állandó mennyiségi jogcímek a GiB-ban vannak megadva, de az Azure Managed Disks szolgáltatás egy adott méretű SKU-ra van kiszámlázva. Ezek az SKU-32GiB az S4 vagy a P4 lemezeken a S80-vagy P80-lemezek 32TiB (előzetes verzió). A prémium szintű felügyelt lemez átviteli sebessége és IOPS teljesítménye az AK-fürtben található csomópontok SKU-jának és példányának méretétől függ. További információ: [Managed Disks díjszabása és teljesítménye][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímek létrehozása

A tárolási osztályok alapján a tárolók automatikus kiépítéséhez állandó mennyiségi jogcím (PVC) használatos. Ebben az esetben a PVC az előre létrehozott tárolási osztályok egyikét használja egy standard vagy prémium szintű Azure Managed Disk létrehozásához.

Hozzon létre egy nevű fájlt `azure-premium.yaml` , és másolja a következő jegyzékbe. A jogcím a `azure-managed-disk` *ReadWriteOnce* -hozzáféréssel rendelkező, *5 GB* méretű lemezt kér. A *felügyelt Premium* Storage osztály a tárolási osztályként van megadva.

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
> Szabványos tárolót használó lemez létrehozásához használja a `storageClassName: default` nem *felügyelt prémium szintűt*.

Hozza létre az állandó kötet jogcímet a [kubectl Apply][kubectl-apply] paranccsal, és adja meg az *Azure-Premium. YAML* fájlt:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Miután létrehozta az állandó kötet jogcímet, és a lemez sikeresen kiépítve, a lemezhez hozzáféréssel rendelkező Pod hozható létre. A következő jegyzékfájl létrehoz egy alapszintű NGINX-Pod-t, amely az *Azure-Managed-Disk* nevű állandó mennyiségi jogcímet használja az Azure-lemez csatlakoztatásához az elérési úton `/mnt/azure` . Windows Server-tárolók esetén a Windows PATH Convention (például *'d:*) használatával válasszon egy *mountPath* .

Hozzon létre egy nevű fájlt `azure-pvc-disk.yaml` , és másolja a következő jegyzékbe.

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
        claimName: azure-managed-disk
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, ahogy az az alábbi példában is látható:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Most már rendelkezik egy futó Pod lemezzel, amely a `/mnt/azure` címtárban van csatlakoztatva. Ez a konfiguráció megtekinthető a pod-on keresztüli vizsgálat során `kubectl describe pod mypod` , ahogy az az alábbi tömörített példában is látható:

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

## <a name="use-ultra-disks"></a>Ultra-lemezek használata
Az ultra Disk kihasználása lásd: az [Azure Kubernetes Service (ak) szolgáltatáson](use-ultra-disks.md)keresztüli Ultra Disks használata.

## <a name="back-up-a-persistent-volume"></a>Állandó kötet biztonsági mentése

Az állandó köteten lévő adatok biztonsági mentéséhez készítsen pillanatképet a kötet felügyelt lemezéről. Ezt a pillanatképet használhatja a visszaállított lemez létrehozásához és a hüvelyekhez való csatolásához az adatok visszaállításához.

Először szerezze be a kötet nevét a `kubectl get pvc` paranccsal, például az *Azure által felügyelt lemez* nevű PVC esetében:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ez a kötet neve az alapul szolgáló Azure-lemez nevét képezi. Kérdezze le a lemez AZONOSÍTÓját az [az Disk List][az-disk-list] paranccsal, és adja meg a PVC-kötet nevét az alábbi példában látható módon:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemez azonosítójával hozzon létre egy pillanatkép-lemezt az [az Snapshot Create][az-snapshot-create]paranccsal. A következő példa létrehoz egy *pvcSnapshot* nevű pillanatképet ugyanabban az erőforráscsoporthoz, mint az AK-fürtöt (*MC_myResourceGroup_myAKSCluster_eastus*). Ha pillanatképeket hoz létre, és helyreállítja a lemezeket azokon az erőforráscsoportok között, amelyekhez az AK-fürt nem fér hozzá, akkor az engedélyek problémákba ütközhet.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően a pillanatkép létrehozása néhány percet is igénybe vehet.

## <a name="restore-and-use-a-snapshot"></a>Pillanatkép visszaállítása és használata

A lemez visszaállításához és a Kubernetes-Pod használatával történő használatához használja a pillanatképet forrásként, amikor létrehoz egy lemezt az [az Disk Create][az-disk-create]paranccsal. Ez a művelet megőrzi az eredeti erőforrást, ha ezt követően el kell érnie az eredeti adatpillanatképet. A következő példa létrehoz egy *pvcRestored* nevű lemezt a *pvcSnapshot* nevű pillanatképből:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

A helyreállított lemez Pod-vel való használatához határozza meg a lemez AZONOSÍTÓját a jegyzékfájlban. Szerezze be a lemez AZONOSÍTÓját az az [Disk show][az-disk-show] paranccsal. Az alábbi példa lekéri az előző lépésben létrehozott *pvcRestored* tartozó lemez azonosítóját:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Hozzon létre egy nevű Pod-jegyzékfájlt `azure-restored.yaml` , és határozza meg az előző lépésben beszerzett lemez URI-ját. Az alábbi példa egy alapszintű NGINX-webkiszolgálót hoz létre, amelynek a visszaállított lemeze kötetként van csatlakoztatva a */mnt/Azure*-ben:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, ahogy az az alábbi példában is látható:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

A (z) használatával `kubectl describe pod mypodrestored` megtekintheti a pod részleteit, például a következő, a kötetre vonatkozó információkat bemutató tömörített példát:

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

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

További információ az állandó kötetek Kubernetes az Azure-lemezek használatával.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul Azure-lemezekhez][azure-disk-volume]

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
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
