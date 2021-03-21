---
title: Az Azure Kubernetes Service-ben (ak) lévő Azure-lemezek tároló-illesztőprogramjainak használata
description: Megtudhatja, hogyan használhatja az Azure-lemezek Container Storage Interface-(CSI-) illesztőprogramjait az Azure Kubernetes Service (ak) fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178881"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Az Azure Disk Container Storage Interface (CSI) illesztőprogramjainak használata az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)
Az Azure Disk Container Storage Interface (CSI) illesztőprogramja az Azure Kubernetes szolgáltatás (ak) által az Azure-lemezek életciklusának kezeléséhez használt [CSI-specifikációnak](https://github.com/container-storage-interface/spec/blob/master/spec.md)megfelelő illesztőprogram.

A CSI egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszereket helyez el a Kubernetes-alapú tároló-munkaterhelések számára. A CSI használatával a következő lépésekkel írhat, helyezhet üzembe és navigálhat olyan beépülő modulokat, amelyek lehetővé teszik a Kubernetes meglévő tárolási rendszereinek új vagy fejlesztését anélkül, hogy meg kellene érintenie az alapvető Kubernetes-kódot, és várnia kell a kiadási ciklusokra.

Ha a CSI-illesztőprogram támogatásával szeretne létrehozni egy AK-fürtöt, tekintse [meg a CSI-illesztőprogramok engedélyezése az Azure-lemezekhez és a Azure Files az AK](csi-storage-drivers.md)

>[!NOTE]
> A *fában lévő illesztőprogramok* az alapszintű Kubernetes-kód részét képező, az új CSI-illesztőprogramok, amelyek beépülő modulok.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>A CSI állandó köteteinek használata az Azure-lemezekkel

Az [állandó kötet](concepts-storage.md#persistent-volumes) (PV) a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. A PV-t egy vagy több hüvely is használhatja, és dinamikusan vagy statikusan kiépíthető. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan PVs az Azure-lemezekkel, ha egyetlen Pod-t használ egy AK-fürtben. Statikus kiépítés esetén lásd: [kötetek manuális létrehozása és használata az Azure Disks](azure-disk-volume.md)szolgáltatással.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Azure Disk PVs dinamikus létrehozása a beépített tárolási osztályok használatával

A tárolási osztály segítségével határozható meg, hogy egy adott tárolási egység hogyan legyen dinamikusan létrehozva állandó kötettel. További információ a Kubernetes tárolásával kapcsolatban: [Kubernetes Storage classs][kubernetes-storage-classes]. Ha a Storage CSI-illesztőprogramokat használja az AK-on, két további beépített eszköz van, `StorageClasses` amelyek az Azure Disk CSI-tároló illesztőprogramjait használják. A további CSI-tárolási osztályok a fürttel együtt a fában alapértelmezett tárolási osztályok mellett jönnek létre.

- `managed-csi`: Az Azure standard SSD helyileg redundáns tárolást (LRS) használja egy felügyelt lemez létrehozásához.
- `managed-csi-premium`: Az Azure Premium LRS használatával felügyelt lemezt hozhat létre.

A visszaigénylési házirend mindkét tárolási osztályban biztosítja, hogy az alapul szolgáló Azure-lemez törölve legyen a megfelelő PV törlésekor. A tárolási osztályok azt is megadhatják, hogy a PVs kibontható legyen. Csak az állandó mennyiségi jogcímet (PVC) kell szerkesztenie az új mérettel.

A tárolási osztályok kihasználása érdekében hozzon létre egy [PVC](concepts-storage.md#persistent-volume-claims) -t és egy megfelelő Pod-t, amely hivatkozik és használja azokat. A virtuális gépek a tárolók alapján automatikusan kiépítik a tárterületet. A PVC az előre létrehozott tárolási osztályok egyikét vagy egy felhasználó által definiált tárolási osztályt használhat egy Azure által felügyelt lemez létrehozásához a kívánt SKU-hoz és mérethez. Ha létrehoz egy Pod-definíciót, a rendszer a PVC-t úgy adja meg, hogy kérje a kívánt tárterületet.

Hozzon létre egy példát a pod és a megfelelő PVC-re a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Ha a pod futó állapotban van, hozzon létre egy nevű új fájlt `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Mostantól ellenőrizheti, hogy a lemez megfelelően van-e csatlakoztatva a következő parancs futtatásával, és ellenőrzi, hogy megjelenik-e a `test.txt` fájl a kimenetben:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolási osztály létrehozása

Az alapértelmezett tárolási osztályok a leggyakoribb forgatókönyvek, de nem az összes. Bizonyos esetekben előfordulhat, hogy saját tárolási osztályt szeretne használni a saját paraméterekkel. Például van egy olyan forgatókönyv, amelyben érdemes lehet módosítani az `volumeBindingMode` osztályt.

Az alapértelmezett tárolási osztályok olyan `volumeBindingMode: Immediate` osztályt használnak, amely garantálja, hogy a rendszer azonnal megtörténjen a PVC létrehozása után. Azokban az esetekben, amikor a csomópont-készletek topológia korlátozottak, például a rendelkezésre állási zónák használata, a PVs kötve vagy kiépítve lenne a pod ütemezési követelményeinek ismerete nélkül (ebben az esetben egy adott zónában kell lennie).

Ennek a forgatókönyvnek a megoldásához használhatja a `volumeBindingMode: WaitForFirstConsumer` -t, amely késlelteti a PV kötését és kiépítési folyamatát, amíg a PVC-t használó Pod-t nem kell létrehozni. Így a PV a pod ütemezési korlátai által meghatározott rendelkezésre állási zónában (vagy más topológiában) lesz kiépítve.

Hozzon létre egy nevű fájlt `sc-azuredisk-csi-waitforfirstconsumer.yaml` , és illessze be a következő jegyzékfájlt.
A tárolási osztály megegyezik a `managed-csi` tárolási osztállyal, de egy másik `volumeBindingMode` osztállyal.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a `sc-azuredisk-csi-waitforfirstconsumer.yaml` fájlt:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Mennyiségi Pillanatképek

Az Azure Disk CSI-illesztőprogram támogatja [az állandó kötetek pillanatképének](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)létrehozását. Ennek a funkciónak a részeként az illesztőprogram a paraméterben megadott értéktől függően *teljes* vagy [ *növekményes* pillanatképeket](../virtual-machines/disks-incremental-snapshots.md) is végrehajthat `incremental` (alapértelmezés szerint igaz).

Az összes paraméterrel kapcsolatos részletekért lásd a [mennyiségi Pillanatképek osztályának paramétereit](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Kötet pillanatképének létrehozása

Erre a képességre például hozzon létre egy [mennyiségi pillanatkép-osztályt](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Most hozzon létre egy [kötet-pillanatképet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) az [oktatóanyag elején dinamikusan létrehozott](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)PVC-ből `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Győződjön meg arról, hogy a pillanatkép helyesen lett létrehozva:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Új PVC létrehozása kötet-pillanatkép alapján

Kötet-pillanatkép alapján létrehozhat egy új PVC-t. Használja az előző lépésben létrehozott pillanatképet, és hozzon létre egy [új PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) -t és egy [új Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) -ot.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Végül győződjön meg róla, hogy ugyanaz a PVC, amelyet a tartalom ellenőrzése előtt hozott létre.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

A várt módon továbbra is láthatjuk a korábban létrehozott `test.txt` fájlt.

## <a name="clone-volumes"></a>Kötetek klónozása

A klónozott kötetek egy meglévő Kubernetes-kötet duplikálása vannak meghatározva. További információ a Kubernetes található kötetek klónozásáról: a kötetek [klónozásának](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)fogalmi dokumentációja.

Az Azure-lemezek CSI-illesztőprogramja támogatja a kötetek klónozását. A bemutatóhoz hozzon létre egy [klónozott kötetet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) a [korábban létrehozott](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` és [egy új Pod használatával](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Most már megvizsgálhatja a klónozott kötet tartalmát a következő parancs futtatásával, és megerősíti, hogy a létrehozott fájl továbbra is látható `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Állandó kötet átméretezése

Ehelyett nagyobb kötetet igényelhet egy PVC számára. Szerkessze a PVC objektumot, és válasszon nagyobb méretet. Ez a módosítás elindítja az alapul szolgáló kötet kiterjesztését, amely a PV-t támogatja.

> [!NOTE]
> Egy új PV soha nem jön létre, hogy kielégítse a jogcímet. Ehelyett a rendszer átméretezi egy meglévő kötetet.

Az AK-ban a beépített `managed-csi` tárolási osztály már lehetővé teszi a bővítést, ezért használja a [korábban létrehozott PVC-t ezzel a tárolási osztállyal](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). A PVC 10 GI állandó kötetet kért. A futtatásával a következőket ellenőrizheti:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Az Azure Disk CSI-illesztőprogram jelenleg csak a hüvelyek által társított (és a kötet nem egy adott csomóponthoz csatlakoztatott) átméretezését támogatja.

Ezért töröljük a korábban létrehozott Pod-t:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Bontsa ki a PVC-t a `spec.resources.requests.storage` mező növelésével:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Erősítse meg, hogy a kötet már nagyobb:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> A PVC nem tükrözi az új méretet, amíg hozzá nem kapcsolódik a pod.

Hozzunk létre egy új Pod-t:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Végezetül pedig erősítse meg a PVC méretét és a pod-on belül:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Megosztott lemez

Az Azure [Shared](../virtual-machines/disks-shared.md) Disks egy Azure Managed Disks szolgáltatás, amely lehetővé teszi az Azure-lemezek egyidejű csatlakoztatását az ügynökök csomópontjaihoz. A felügyelt lemezek több ügynök-csomóponthoz való csatolásával például új vagy meglévő fürtözött alkalmazásokat telepíthet az Azure-ba.

> [!IMPORTANT] 
> Jelenleg csak a nyers blokk eszköz ( `volumeMode: Block` ) támogatott az Azure Disk CSI-illesztőprogramban. Az alkalmazásoknak kezelniük kell az írások, olvasások, zárolások, gyorsítótárak, csatlakoztatások és kerítések koordinálását és felügyeletét a megosztott lemezen, amely nyers blokk-eszközként van kitéve.

Hozzon létre egy nevű fájlt a `shared-disk.yaml` következő parancs másolásával, amely a megosztott lemez tárolási osztályát és a PVC-t tartalmazza:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a `shared-disk.yaml` fájlt:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Most hozzon létre egy nevű fájlt `deployment-shared.yml` a következő parancs másolásával:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Hozza létre az üzemelő példányt a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a `deployment-shared.yml` fájlt:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Végül nézzük meg az eszközt a pod-on belül:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-tárolók

Az Azure Disk CSI-illesztőprogram a Windows-csomópontokat és-tárolókat is támogatja. Ha Windows-tárolókat szeretne használni, kövesse a [Windows-tárolók oktatóanyagot](windows-container-cli.md) a Windows-csomópontok hozzáadásához.

A Windows-csomópontok készletének használata után már használhatja a beépített tárolási osztályokat, például a következőt: `managed-csi` . A következő parancs az kubectl Apply paranccsal történő telepítésével olyan [Windows-alapú állapot-nyilvántartó készletet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) helyezhet üzembe, amely időbélyegeket ment a fájlba `data.txt` : [][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

A kötet tartalmát mostantól a (z) parancs futtatásával is ellenőrizheti:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- A Azure Fileshez készült CSI-illesztőprogramok használatáról további információt a [Azure Files használata a CSI-illesztőprogramokkal](azure-files-csi.md)című témakörben talál.
- További információ a Storage ajánlott eljárásairól: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register