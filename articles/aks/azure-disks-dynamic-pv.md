---
title: Azure-lemezkötet dinamikus létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre dinamikusan állandó kötetet Azure-lemezekkel a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 066a52024e91610882889bb7fbe6b20efa262b71
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776140"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Állandó kötet dinamikus létrehozása és használata Azure-lemezekkel a Azure Kubernetes Service (AKS)

Az állandó kötetek olyan tárolók, amelyek Kubernetes-podokkal való használatra vannak kiépítve. Az állandó köteteket egy vagy több pod is használhatja, és dinamikusan vagy statikusan is kiépítheti őket. Ez a cikk bemutatja, hogyan hozhat létre dinamikusan állandó köteteket Azure-lemezekkel egy Azure Kubernetes Service -fürt egyetlen podja számára.

> [!NOTE]
> Az Azure-lemezek csak  *ReadWriteOnce* típusú hozzáférési móddal csatlakoztathatóak, ami elérhetővé teszi az AKS egy csomópontja számára. Ha egy állandó kötetet kell megosztania több csomópont között, használja a [Azure Files.][azure-files-pvc]

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="built-in-storage-classes"></a>Beépített tárolóosztályok

A tárolóosztályok meghatározzák, hogyan jön létre dinamikusan egy tárolóegység egy állandó köteten. A Kubernetes tárolási osztályokkal kapcsolatos további információkért lásd: [Kubernetes Storage-osztályok.][kubernetes-storage-classes]

Mindegyik AKS-fürt négy előre létrehozott tárolóosztályt tartalmaz, amelyek közül kettő Azure-lemezekkel való használatra van konfigurálva:

* Az *alapértelmezett tárolási* osztály egy standard SSD Azure-lemezt hoz ki.
    * A standard szintű tárolás standard SZINTŰ SSD-ket biztosít, és költséghatékony tárolást biztosít, miközben megbízható teljesítményt nyújt. 
* A *felügyelt prémium szintű tárolási* osztály prémium szintű Azure-lemezt hoz üzembe.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha a fürtben lévő AKS-csomópontok prémium szintű tárolót használ, válassza a *managed-premium osztályt.*
    
Ha az alapértelmezett tárolóosztályok valamelyikét használja, a tárolóosztály létrehozása után nem tudja frissíteni a kötet méretét. Ha a tárolóosztály létrehozása után frissíteni tudja a kötet méretét, adja hozzá a sort az egyik alapértelmezett tárolóosztályhoz, vagy hozzon létre egy saját egyéni `allowVolumeExpansion: true` tárolóosztályt. Vegye figyelembe, hogy az ADATvesztés elkerülése érdekében nem támogatott az EGYED méretének csökkentése. Meglévő tárolóosztályt az paranccsal `kubectl edit sc` szerkeszthet. 

Ha például 4 TiB méretű lemezt szeretne használni, létre kell hoznia egy tárolóosztályt, amely meghatározza, hogy a lemez-gyorsítótárazás nem támogatott a 4 TiB vagy nagyobb méretű `cachingmode: None` [lemezeken.](../virtual-machines/premium-storage-performance.md#disk-caching)

További információ a tárolási osztályokról és a saját tárolóosztály létrehozásáról: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][storage-class-concepts]

A [kubectl get sc paranccsal][kubectl-get] tekintse meg az előre létrehozott tárolási osztályokat. Az alábbi példa az AKS-fürtön belül elérhető, előre létrehozott tárolási osztályokat mutatja be:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Az állandó kötetek jogcímei a GiB-ban vannak megadva, de az Azure-beli felügyelt lemezek számlázása termékváltozat alapján, adott méret esetén. Ezek az S4- vagy P4-lemezek 32GiB és S80 vagy P80 lemezek esetén 32TiB-osak (előzetes verzióban). A prémium szintű felügyelt lemezek átviteli sebességének és IOPS-teljesítményének mértéke az AKS-fürt csomópontjainak termékváltozatától és példányméretétől is függ. További információ: Díjszabás és teljesítmény [Managed Disks.][managed-disk-pricing-performance]

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímének létrehozása

Az állandó kötet jogcímek (PERSISTENT) segítségével a rendszer automatikusan kiépíti a tárolót egy tárolóosztály alapján. Ebben az esetben a PVC az előre létrehozott tárolóosztályok egyikével létrehozhat egy standard vagy prémium szintű felügyelt Azure-lemezt.

Hozzon létre egy nevű `azure-premium.yaml` fájlt, és másolja be a következő jegyzékfájlt. A jogcím egy 5 GB méretű lemezt kér `azure-managed-disk` *ReadWriteOnce hozzáféréssel.*  A *managed-premium tárolási* osztály tárolási osztályként van megadva.

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
> Standard szintű tárolót használó lemez létrehozásához a managed-premium helyett használja `storageClassName: default` *a-t.*

Hozza létre az állandó kötet jogcímét a [kubectl apply paranccsal,][kubectl-apply] és adja meg az *azure-premium.yaml fájlt:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Az állandó kötet jogcímének létrehozása és a lemez sikeres üzembe állítása után egy pod is létre lehet hozva a lemezhez való hozzáféréssel. A következő jegyzékfájl létrehoz egy alapszintű NGINX-podot, amely az *azure-managed-disk* nevű állandó kötet jogcímet használja az Azure-lemez csatlakoztatáshoz a elérési `/mnt/azure` úton. Windows Server-tárolók esetén adjon meg egy *mountPath* elérési utat a Windows elérési útjának konvenciója alapján, például: *"D:".*

Hozzon létre egy nevű `azure-pvc-disk.yaml` fájlt, és másolja be az alábbi jegyzékfájlt.

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

Hozza létre a podot a [kubectl apply paranccsal][kubectl-apply] az alábbi példában látható módon:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Most már van egy futó podja, amely az Azure-lemezt csatlakoztatja a `/mnt/azure` könyvtárhoz. Ez a konfiguráció akkor látható, amikor a segítségével megvizsgálja a podot, ahogy az a következő rövid `kubectl describe pod mypod` példában látható:

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

## <a name="use-ultra-disks"></a>A ultralemezek
Az ultralemezek használatát [lásd: Use ultralemezek on Azure Kubernetes Service (AKS) (Az ultralemezek on Azure Kubernetes Service (AKS) használata).](use-ultra-disks.md)

## <a name="back-up-a-persistent-volume"></a>Tartós kötet biztonságiának biztonsági

Az állandó köteten lévő adatok biztonsági felvételéhez pillanatfelvételt készíthet a kötet felügyelt lemezről. Ezután ezzel a pillanatképtel létrehozhat egy visszaállított lemezt, és csatlakoztathatja a podokat az adatok visszaállításához.

Először szerezze be a kötet nevét az paranccsal, például az `kubectl get pvc` *azure-managed-disk* nevű MAJD parancshoz:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ez a kötetnév képezi a mögöttes Azure-lemez nevét. A lemezazonosítót az [az disk list][az-disk-list] használatával kell lekérdezni, és meg kell adni aŰTŰN kötetének nevét az alábbi példában látható módon:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezazonosítóval hozzon létre egy pillanatképlemezt [az az snapshot create használatával.][az-snapshot-create] Az alábbi példa létrehoz egy *pillanatképet blobSnapshot* névvel ugyanabban az erőforráscsoportban, mint az AKS-fürt (*MC_myResourceGroup_myAKSCluster_eastus*). Engedélyekkel kapcsolatos problémákba ütközhet, ha olyan erőforráscsoportokban hoz létre pillanatképeket és visszaállít lemezeket, amelyekhez az AKS-fürt nem rendelkezik hozzáféréssel.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően a pillanatkép létrehozása eltarthat néhány percig.

## <a name="restore-and-use-a-snapshot"></a>Pillanatkép visszaállítása és használata

A lemez visszaállításához és Kubernetes-podokkal való használathoz használja forrásként a pillanatképet, amikor az az disk create használatával hoz létre [lemezt.][az-disk-create] Ez a művelet megőrzi az eredeti erőforrást, ha ezután hozzá kell férni az eredeti adat-pillanatképhez. Az alábbi példában létrehozunk egy *pillanatképből egy pillanatképből egystored nevű lemezt, amely a* *következő:*

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Ha a visszaállított lemezt podokkal együtt használja, adja meg a lemez azonosítóját a jegyzékfájlban. Az az disk show paranccsal [szerezze be a lemezazonosítót.][az-disk-show] Az alábbi példa lekérte az előző lépésben *létrehozottstored* lemezazonosítót:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Hozzon létre egy nevű `azure-restored.yaml` podjegyzéket, és adja meg az előző lépésben lekért lemez URI-t. Az alábbi példa egy alapszintű NGINX-webkiszolgálót hoz létre, amely a visszaállított lemezt kötetként csatlakoztatja az */mnt/azure helyre:*

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

Hozza létre a podot a [kubectl apply paranccsal][kubectl-apply] az alábbi példában látható módon:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

A használatával megtekintheti a pod részleteit, például a következő tömör példát, amely a `kubectl describe pod mypodrestored` kötet adatait jeleníti meg:

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

A kapcsolódó ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való tároláshoz és biztonsági [mentéshez.][operator-best-practices-storage]

További információ az Azure-lemezeket használó állandó Kubernetes-kötetekkel kapcsolatban.

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
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
