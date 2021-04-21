---
title: A Container Storage Interface (CSI) illesztőprogramjainak használata az Azure Disks on Azure Kubernetes Service (AKS) esetében
description: Megtudhatja, hogyan használhatja a Container Storage Interface (CSI) illesztőprogramokat az Azure-lemezekhez egy Azure Kubernetes Service -fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776230"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Az Azure Disk Container Storage Interface (CSI) illesztőprogramjainak használata Azure Kubernetes Service (előzetes verzió)
Az Azure Disk Container Storage Interface (CSI) [](https://github.com/container-storage-interface/spec/blob/master/spec.md)illesztőprogram egy CSI-specifikációnak megfelelő illesztőprogram, amelyet az Azure Kubernetes Service (AKS) az Azure-lemezek életciklusának kezeléséhez használ.

A CSI egy olyan szabvány, amely tetszőleges blokk- és fájltároló rendszereket tár fel a Kubernetes tárolóba ezett számítási feladatai számára. A CSI alkalmazásával és használatával az AKS képes beépülő modulokat írni, üzembe helyezni és iterálni a Kubernetesben található új tárolórendszerek felfedése vagy javítása érdekében anélkül, hogy hozzá kellene érnie az alapvető Kubernetes-kódot, és meg kellene várnia a kiadási ciklusokat.

CSI-illesztőprogramot támogató AKS-fürt létrehozásához lásd: [Enable CSI drivers for Azure disks and Azure Files on AKS (CSI-illesztőprogramok engedélyezése Azure-lemezekhez és Azure Files AKS-hez).](csi-storage-drivers.md)

>[!NOTE]
> *A fastruktúrában található illesztőprogramok* az alapvető Kubernetes-kód részét képezi, és az új CSI-illesztőprogramok, azaz a beépülő modulok.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>ÁLLANDÓ CSI-kötetek használata Azure-lemezekkel

Az [állandó kötet](concepts-storage.md#persistent-volumes) (PV) a Kubernetes-podokkal való használatra kiépített tárolóegység. A PV-t egy vagy több pod is használhatja, és dinamikusan vagy statikusan kiépíthet. Ez a cikk bemutatja, hogyan hozhat létre dinamikusan PV-ket Azure-lemezekkel egy AKS-fürt egyetlen podja számára. A statikus kiépítéssel kapcsolatos további információért lásd: Kötet manuális létrehozása és [használata Azure-lemezekkel.](azure-disk-volume.md)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Azure-beli lemezes virtuális merevlemezek dinamikus létrehozása a beépített tárolóosztályok használatával

A tárolóosztályok meghatározzák, hogyan jön létre dinamikusan egy tárolóegység egy állandó köteten. A Kubernetes-tárolóosztályokkal kapcsolatos további információkért lásd: [Kubernetes-tárolóosztályok.][kubernetes-storage-classes] Ha tárolási CSI-illesztőprogramokat használ az AKS-ben, két további beépített eszköz használja az `StorageClasses` Azure Disk CSI-tárolóillesztőket. A további CSI-tárolóosztályok a fürthöz a fán belül alapértelmezett tárolási osztályokkal együtt vannak létrehozva.

- `managed-csi`: Az Azure standard SSD helyileg redundáns tárolást (LRS) használ egy felügyelt lemez létrehozásához.
- `managed-csi-premium`: Az Azure Premium LRS használatával hoz létre felügyelt lemezt.

A visszaigénylési szabályzat mindkét tárolási osztályban biztosítja, hogy a mögöttes Azure-lemez törlődik a megfelelő PV törlésekor. A tárolóosztályok a PV-ket is kibonthatóként konfigurálják. Csak szerkesztenie kell az állandó kötetigénylést (PVC) az új mérettel.

Ezeknek a tárolóosztályoknak a kihasználás érdekében hozzon létre egy [OLYAN POD-t](concepts-storage.md#persistent-volume-claims) és megfelelő podot, amely hivatkozik rájuk, és használja őket. A RENDSZER EGY ADOTT TÁROLÓosztály alapján automatikusan kiépíti a tárolót. A PVC az előre létrehozott tárolóosztályok vagy egy felhasználó által definiált tárolási osztály használatával létrehozhat egy Azure által felügyelt lemezt a kívánt termékváltozathoz és mérethez. Amikor poddefiníciót hoz létre, a rendszer a KÍVÁNT tároló lekérésére adja meg a PVC-t.

Hozzon létre egy példában használt podot és a megfelelő PVC-t a [kubectl apply paranccsal:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Miután a pod fut, hozzon létre egy nevű új `test.txt` fájlt.

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Most már ellenőrizheti, hogy a lemez megfelelően van-e csatlakoztatva, ha futtatja a következő parancsot, és ellenőrzi, hogy látja-e a fájlt a `test.txt` kimenetben:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolóosztály létrehozása

Az alapértelmezett tárolási osztályok megfelelnek a leggyakoribb forgatókönyveknek, de nem az összesnek. Bizonyos esetekben érdemes lehet saját tárolóosztályt testre szabni a saját paramétereivel. Van például egy forgatókönyvünk, amelyben érdemes lehet módosítani az `volumeBindingMode` osztályt.

Olyan osztályt is használhat, amely garantálja, hogy az azonnal megtörténik `volumeBindingMode: Immediate` az EGYSZERes létrehozás után. Ha a csomópontkészletek topológiája korlátozott, például rendelkezésre állási zónák használata esetén a PV-k a pod ütemezési követelményeinek ismerete nélkül lesznek kötve vagy kiépítve (ebben az esetben egy adott zónában vannak).

Ennek a forgatókönyvnek a megoldásában használhatja a et, amely késlelteti a PV kötését és létesítét, amíg létre nem jön egy POD, amely a `volumeBindingMode: WaitForFirstConsumer` PV-t használja. Így a PV megfelel és a pod ütemezési megkötései által meghatározott rendelkezésre állási zónában (vagy más topológiában) lesz kiépítve. Az alapértelmezett tárolási osztályok a `volumeBindingMode: WaitForFirstConsumer` osztályt használják.

Hozzon létre egy nevű `sc-azuredisk-csi-waitforfirstconsumer.yaml` fájlt, és illessze be a következő jegyzékfájlt.
A tárolási osztály megegyezik a tárolási `managed-csi` osztálysal, de más `volumeBindingMode` osztálysal.

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

Hozza létre a storage osztályt a [kubectl apply paranccsal,][kubectl-apply] és adja meg a `sc-azuredisk-csi-waitforfirstconsumer.yaml` fájlt:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Kötet-pillanatképek

Az Azure Disk CSI illesztőprogram támogatja a tartós kötetek [pillanatképének létrehozását.](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) Ennek a képességnek a részeként az  illesztő teljes vagy növekményes pillanatképeket készíthet a paraméterben beállított értéktől függően (ez alapértelmezés szerint [  ](../virtual-machines/disks-incremental-snapshots.md) `incremental` igaz).

Az összes paraméter részleteiért tekintse meg a [kötet pillanatfelvétel-osztályának paramétereit.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)

### <a name="create-a-volume-snapshot"></a>Kötet pillanatképének létrehozása

Erre a képességre példaként hozzon létre egy kötet-pillanatképosztályt [a](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) [kubectl apply paranccsal:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Most hozzunk létre egy kötet-pillanatképet [a](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) TUTORIAL-ból, amit dinamikusan hoztunk létre az oktatóanyag [elején,](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Ellenőrizze, hogy a pillanatkép megfelelően lett-e létrehozva:

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

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Új SNAPSHOT létrehozása kötet-pillanatkép alapján

Kötet-pillanatkép alapján létrehozhat egy új SNAPSHOT-t. Használja az előző lépésben létrehozott pillanatképet, és hozzon létre egy új [PILLANATKÉPet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) és egy új [podot](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) a felhasználáshoz.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Végül a tartalom ellenőrzésével győződjön meg arról, hogy ez a korábban létrehozott UGYANAZ a PVC.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Ahogy vártuk, továbbra is látható a korábban létrehozott `test.txt` fájl.

## <a name="clone-volumes"></a>Kötetek klónozása

A klónozott kötet egy meglévő Kubernetes-kötet duplikált köteteként van definiálva. A kötetek Kubernetesben való klónozásának további információiért tekintse meg a kötetek klónozásának fogalmi [dokumentációját.](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)

Az Azure-lemezek CSI-illesztőprogramja támogatja a kötet klónozását. A szemléltetéshez hozzon létre [](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) egy [klónozott kötetet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) a korábban létrehozottból, és egy `azuredisk-pvc` új podot, amely [felhasználja azt.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Most a következő parancs futtatásával ellenőrizhetjük a klónozott kötet tartalmát, és ellenőrizhetjük, hogy továbbra is látható-e a `test.txt` létrehozott fájl.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Állandó kötet átméretezése

Ehelyett nagyobb kötetet is kérhet a PVC-hez. Szerkessze a PVC objektumot, és adjon meg egy nagyobb méretet. Ez a változás elindítja a PV alapjátul szolgáló mögöttes kötet bővítését.

> [!NOTE]
> A rendszer soha nem hoz létre új PV-t a jogcím kielégítéséhez. Ehelyett átméretez egy meglévő kötetet.

Az AKS-ben a beépített tárolóosztály már lehetővé teszi a bővítést, ezért használja a korábban ezzel a tárolóosztálysal létrehozott `managed-csi` [PVC-t.](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) A PVC egy 10 Gi állandó kötetet kért. Ezt a következő futtatásával ellenőrizhetjük:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Az Azure Disk CSI illesztőprogram jelenleg csak podok nélkül támogatja a PVC-k átméretezését (és a kötet nincs csatlakoztatva egy adott csomóponthoz).

Ezért töröljük a korábban létrehozott podot:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Bontsuk ki a PVC-t a mező `spec.resources.requests.storage` növelésével:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Győződjön meg arról, hogy a kötet most nagyobb:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Az ÚJ MÉRET mindaddig nem tükrözi az új méretet, amíg nincs hozzá ismét társítva pod.

Hozzunk létre egy új podot:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Végül erősítse meg a ÉS a POD méretét:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Megosztott lemez

[Az Azure-beli](../virtual-machines/disks-shared.md) megosztott lemezek az Azure felügyelt lemezei szolgáltatás, amely lehetővé teszi egy Azure-lemez egyidejű csatolását az ügynökcsomópontokhoz. Ha egy felügyelt lemezt több ügynökcsomóponthoz csatol, lehetővé teszi például új fürtözött alkalmazások üzembe helyezését vagy meglévő fürtözött alkalmazások áttelepítését az Azure-ba.

> [!IMPORTANT] 
> Jelenleg az Azure Disk CSI-illesztőprogram csak a nyers blokkeszközt ( `volumeMode: Block` ) támogatja. Az alkalmazásoknak kezelni kell az írások, olvasások, zárolások, gyorsítótárak, csatlakoztatások és elkerítések koordinációját és vezérlését a nyers blokkeszközként elérhető megosztott lemezen.

Hozzunk létre egy nevű fájlt a következő parancs másolása által, amely a megosztott lemezes tárolási osztályt és `shared-disk.yaml` a COPY osztályt tartalmazza:

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

Hozza létre a storage osztályt a [kubectl apply paranccsal,][kubectl-apply] és adja meg a `shared-disk.yaml` fájlt:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Most hozzunk létre egy nevű fájlt a `deployment-shared.yml` következő parancs másolása segítségével:

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

Hozza létre az üzemelő példányt a [kubectl apply paranccsal,][kubectl-apply] és adja meg a `deployment-shared.yml` fájlt:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Végül ellenőrizzük a blokkeszközt a podon belül:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-tárolók

Az Azure Disk CSI illesztőprogram a Windows-csomópontokat és -tárolókat is támogatja. Ha Windows-tárolókat szeretne használni, [](windows-container-cli.md) kövesse a Windows-tárolók oktatóanyagát egy Windows-csomópontkészlet hozzáadásához.

Ha már rendelkezik Windows-csomópontkészletekkel, már használhatja a beépített tárolóosztályokat, például a `managed-csi` osztályt. Üzembe helyezhet egy [Windows-alapú](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) állapotalapú készletet, amely időbélyegeket ment a fájlba a következő parancs `data.txt` [kubectl apply paranccsal][kubectl-apply] történő üzembe helyezésével:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Most már ellenőrizheti a kötet tartalmát a következő futtatásával:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- A CSI-illesztőprogramok a csi-Azure Files: Use Azure Files with CSI drivers (Az Azure Files [használata CSI-illesztőprogramokkal).](azure-files-csi.md)
- További információ a tárolással kapcsolatos ajánlott eljárásokról: Ajánlott tárolási és biztonsági mentési eljárások a [Azure Kubernetes Service.][operator-best-practices-storage]


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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
