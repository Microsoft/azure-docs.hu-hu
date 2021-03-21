---
title: A (z) Azure Files Azure Kubernetes Service-ben (ak) található Container Storage Interface (CSI) illesztőprogramok használata
description: Ismerje meg, hogyan használható a Container Storage Interface (CSI) illesztőprogramja Azure Fileshoz egy Azure Kubernetes-szolgáltatás (ak) fürtjében.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174061"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>A Azure Files Container Storage Interface (CSI) illesztőprogramjainak használata az Azure Kubernetes Service-ben (ak) (előzetes verzió)

A Azure Files Container Storage Interface (CSI) illesztőprogramja az Azure Kubernetes Service (ak) által használt [CSI-specifikációnak](https://github.com/container-storage-interface/spec/blob/master/spec.md)megfelelő illesztőprogram a Azure Files-megosztások életciklusának kezeléséhez.

A CSI egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszereket helyez el a Kubernetes-alapú tároló-munkaterhelések számára. A CSI bevezetésével és használatával mostantól írhat, telepíthet és megismételheti a beépülő modulokat, hogy új vagy javítsa a meglévő tárolási rendszereket a Kubernetes anélkül, hogy meg kellene érintenie az alapvető Kubernetes-kódot, és várnia kell a kiadási ciklusokra.

Ha a CSI-illesztőprogram támogatásával szeretne létrehozni egy AK-fürtöt, tekintse [meg a CSI-illesztőprogramok engedélyezése az Azure-lemezekhez és a Azure Files az AK](csi-storage-drivers.md)

>[!NOTE]
> A *fában lévő illesztőprogramok* az alapszintű Kubernetes-kód részét képező, az új CSI-illesztőprogramok, amelyek beépülő modulok.

## <a name="use-a-persistent-volume-with-azure-files"></a>Állandó kötet használata Azure Files

Az [állandó kötet (PV)](concepts-storage.md#persistent-volumes) a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. A PV-t egy vagy több hüvely is használhatja, és dinamikusan vagy statikusan kiépíthető. Ha több hüvelynek egyidejű hozzáférésre van szüksége ugyanahhoz a tárolási kötethez, akkor a Azure Files használatával csatlakozhat a [Server Message Block (SMB) protokollal][smb-overview]. Ez a cikk bemutatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást egy AK-fürt több hüvelye általi használatra. Statikus kiépítés esetén tekintse [meg a kötetek manuális létrehozása és használata Azure Files megosztással](azure-files-volume.md)című témakört.

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Azure Files PVs dinamikus létrehozása a beépített tárolási osztályok használatával

A tárolási osztályok a Azure Files megosztások létrehozásának módját határozzák meg. A rendszer automatikusan létrehoz egy Storage-fiókot a [csomópont-erőforráscsoport][node-resource-group] számára a tárolási osztállyal való használatra a Azure Files megosztások tárolásához. Válasszon az alábbi [Azure Storage-redundancia SKU][storage-skus] - *skuName* közül:

* **Standard_LRS**: standard, helyileg redundáns tárolás
* **Standard_GRS**: standard geo-redundáns tárolás
* **Standard_ZRS**: standard zóna – redundáns tárolás
* **Standard_RAGRS**: standard olvasási hozzáférésű geo-redundáns tárolás
* **Premium_LRS**: prémium helyileg redundáns tárolás

> [!NOTE]
> Azure Files támogatja az Azure Premium Storaget. A prémium fájlmegosztás minimális megosztása 100 GB.

Ha a Storage CSI-illesztőprogramokat használja az AK-on, két további beépített, `StorageClasses` amely a Azure Files CSI Storage-illesztőprogramokat használja. A további CSI-tárolási osztályok a fürttel együtt a fában alapértelmezett tárolási osztályok mellett jönnek létre.

- `azurefile-csi`: Az Azure standard Storage használatával hoz létre Azure Files megosztást.
- `azurefile-csi-premium`: Az Azure Premium Storage használatával hoz létre egy Azure Files-megosztást.

A visszaigénylési házirend mindkét tárolási osztályban biztosítja, hogy az alapul szolgáló Azure Files-megosztás törölve legyen a megfelelő PV törlésekor. A tárolási osztályok azt is konfigurálja, hogy a fájlmegosztás bővíthető legyen, csak az állandó mennyiségi jogcímet (PVC) kell szerkesztenie az új mérettel.

A tárolási osztályok használatához hozzon létre egy [PVC](concepts-storage.md#persistent-volume-claims) -t és egy megfelelő Pod-t, amely hivatkozik és használja azokat. A virtuális gépek a tárolók alapján automatikusan kiépítik a tárterületet. A PVC az előre létrehozott tárolási osztályok egyikét vagy egy felhasználó által definiált tárolási osztályt használhat Azure Files megosztás létrehozásához a kívánt SKU-hoz és mérethez. Ha létrehoz egy Pod-definíciót, a rendszer a PVC-t úgy adja meg, hogy kérje a kívánt tárterületet.

Hozzon létre egy [példát a PVC és a pod objektumra, `outfile` amely az aktuális dátumot](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) a [kubectl Apply][kubectl-apply] paranccsal kinyomtatja:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Ha a pod futó állapotban van, ellenőrizheti, hogy a fájlmegosztás megfelelően van-e csatlakoztatva az alábbi parancs futtatásával, és a kimenet ellenőrzése a következőt tartalmazza `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolási osztály létrehozása

Az alapértelmezett tárolási osztályok a leggyakoribb forgatókönyvek, de nem az összes. Bizonyos esetekben előfordulhat, hogy saját tárolási osztályt szeretne használni a saját paraméterekkel. Használja például a következő jegyzékfájlt a fájlmegosztás konfigurálásához `mountOptions` .

A *fileMode* és a *dirMode* alapértelmezett értéke *0777* a Kubernetes csatlakoztatott fájlmegosztás esetében. Megadhatja a különböző csatlakoztatási beállításokat a tárolási osztály objektumon.

Hozzon létre egy nevű fájlt `azure-file-sc.yaml` , és illessze be a következő példa jegyzékfájlt:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

A Azure Files CSI-illesztőprogram támogatja az állandó kötetek és a mögöttes fájlmegosztás [pillanatképének](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) létrehozását.

Hozzon létre egy [mennyiségi pillanatkép-osztályt](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Hozzon létre egy [kötet-pillanatképet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) az [oktatóanyag elején dinamikusan létrehozott](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)PVC-ből `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Ellenőrizze, hogy a pillanatkép helyesen lett-e létrehozva:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Állandó kötet átméretezése

A PVC számára nagyobb kötetet igényelhet. Szerkessze a PVC objektumot, és válasszon nagyobb méretet. Ez a módosítás elindítja az alapul szolgáló kötet kiterjesztését, amely a PV-t támogatja.

> [!NOTE]
> Egy új PV soha nem jön létre, hogy kielégítse a jogcímet. Ehelyett a rendszer átméretezi egy meglévő kötetet.

Az AK-ban a beépített `azurefile-csi` tárolási osztály már támogatja a bővítést, ezért használja a [korábban létrehozott PVC-t ezzel a tárolási osztállyal](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). A PVC kért egy 100Gi-fájlmegosztást. A futtatásával a következőket ellenőrizheti:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Bontsa ki a PVC-t a mező növelésével `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Győződjön meg arról, hogy mind a PVC, mind a fájlrendszer a pod-on belül az új méretet mutatja:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-fájlmegosztás
[A Azure Files mostantól támogatja az NFS v 4.1 protokollt](../storage/files/storage-files-how-to-create-nfs-shares.md). Az NFS 4,1-támogatás a Azure Files számára egy teljes körűen felügyelt NFS-fájlrendszert biztosít, amely egy olyan szolgáltatás, amely magasan elérhető és nagyon tartós, elosztott, rugalmasan fenntartható tárolási platformra épül.

 Ez a beállítás a helyi adatfrissítésekkel rendelkező, véletlenszerű hozzáférésű számítási feladatokhoz van optimalizálva, és teljes körű POSIX fájlrendszer-támogatást biztosít. Ez a szakasz bemutatja, hogyan használhatók az NFS-megosztások az Azure file CSI-illesztőprogrammal egy AK-fürtön.

Győződjön meg arról, hogy a [korlátozásokat](../storage/files/storage-files-compare-protocols.md#limitations) és a [régió elérhetőségét](../storage/files/storage-files-compare-protocols.md#regional-availability) az előzetes verzió fázisában ellenőrzi.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Az `AllowNfsFileShares` előzetes verzió funkciójának regisztrálása

Az NFS 4,1-et használó fájlmegosztás létrehozásához engedélyeznie kell a `AllowNfsFileShares` szolgáltatás jelölőjét az előfizetésén.

Regisztrálja a `AllowNfsFileShares` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. Storage* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Hozzon létre egy Storage-fiókot az NFS-fájlmegosztás számára

[Hozzon létre egy `Premium_LRS` ](../storage/files/storage-how-to-create-file-share.md) Az NFS-megosztások támogatásához a következő konfigurációkat tartalmazó Azure Storage-fiók:
- fiók típusa: FileStorage
- biztonságos átvitel szükséges (csak HTTPS-forgalom engedélyezése): false
- Válassza ki az ügynök csomópontjainak virtuális hálózatát a tűzfalakban és a virtuális hálózatokban – ezért érdemes lehet a Storage-fiókot a MC_ erőforráscsoporthoz létrehozni.

### <a name="create-nfs-file-share-storage-class"></a>NFS-fájlmegosztás tárolási osztályának létrehozása

A `nfs-sc.yaml` megfelelő helyőrzők szerkesztésével mentse az alábbi jegyzékfájlt tartalmazó fájlt.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

A fájl szerkesztése és mentése után hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Központi telepítés létrehozása NFS-alapú fájlmegosztás esetén
A [](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) `data.txt` következő parancs az [kubectl Apply][kubectl-apply] paranccsal történő üzembe helyezésével olyan állapot-nyilvántartó készletet helyezhet üzembe, amely időbélyegeket ment egy fájlba:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

A kötet tartalmának ellenőrzése a futtatásával:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Vegye figyelembe, hogy mivel az NFS-fájlmegosztás Premium-fiókban van, a fájlmegosztás minimális mérete 100 GB. Ha kisebb tárterülettel rendelkező PVC-t hoz létre, a következő hibaüzenet jelenhet meg: "nem sikerült létrehozni a fájlmegosztást... méret (5)... "


## <a name="windows-containers"></a>Windows-tárolók

A Azure Files CSI-illesztőprogram a Windows-csomópontokat és-tárolókat is támogatja. Ha Windows-tárolókat szeretne használni, kövesse a [Windows-tárolók oktatóanyagot](windows-container-cli.md) a Windows-csomópontok hozzáadásához.

A Windows-csomópontok készletének használata után használja a beépített tárolási osztályokat, `azurefile-csi` vagy hozzon létre egyéni fájlokat. Olyan [Windows-alapú állapot-nyilvántartó készletet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) helyezhet üzembe, amely időbélyegeket ment egy fájlba, ha a `data.txt` következő parancsot telepíti a [kubectl Apply][kubectl-apply] paranccsal:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

A kötet tartalmának ellenőrzése a futtatásával:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- Az Azure-lemezek CSI-illesztőprogramjainak használatáról további információt az [Azure-lemezek használata a CSI-illesztőprogramokkal](azure-disk-csi.md)című témakörben talál.
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md