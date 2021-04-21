---
title: A Container Storage Interface (CSI) illesztőprogramok használata a Azure Files (AKS) Azure Kubernetes Service való használathoz
description: Megtudhatja, hogyan használhatja a Container Storage Interface (CSI) illesztőprogramokat Azure Files egy Azure Kubernetes Service -fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776158"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>A Azure Files Container Storage Interface (CSI) illesztőprogramok használata Azure Kubernetes Service (előzetes verzió)

A Azure Files Container Storage Interface (CSI) illesztőprogram [](https://github.com/container-storage-interface/spec/blob/master/spec.md)egy CSI-specifikációnak megfelelő illesztőprogram, amelyet a Azure Kubernetes Service (AKS) a Azure Files-megosztások életciklusának kezeléséhez használ.

A CSI egy olyan szabvány, amely tetszőleges blokk- és fájltároló rendszereket tár fel a Kubernetes tárolóba ezett számítási feladatai számára. A CSI alkalmazásával és használatával az AKS mostantól anélkül tud beépülő modulokat írni, üzembe helyezni és iterálni, hogy új tárolórendszereket tessen elérhetővé a Kubernetesben, vagy tovább fejlesztse a meglévő tárolórendszereket anélkül, hogy hozzá kellene érnie az alapvető Kubernetes-kódot, és várnia kellene a kiadási ciklusra.

CSI-illesztőprogramot támogató AKS-fürt létrehozásához lásd: [Enable CSI drivers for Azure disks and Azure Files on AKS (CSI-illesztőprogramok engedélyezése Azure-lemezekhez és Azure Files AKS-hez).](csi-storage-drivers.md)

>[!NOTE]
> *A fastruktúrában található illesztőprogramok* az alapvető Kubernetes-kód részét képezi, és az új CSI-illesztőprogramok, azaz a beépülő modulok.

## <a name="use-a-persistent-volume-with-azure-files"></a>Állandó kötet használata a Azure Files

Az [állandó kötet (PV)](concepts-storage.md#persistent-volumes) a Kubernetes-podokkal való használatra kiépített tárolóegység. A PV-t egy vagy több pod is használhatja, és dinamikusan vagy statikusan kiépíthet. Ha több pod egyidejű hozzáférésre van szüksége ugyananhoz a tárolókötethez, a Azure Files használatával csatlakozhat a [Server Message Block (SMB) protokollal.][smb-overview] Ez a cikk bemutatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást, amelyet több pod használhat egy AKS-fürtben. A statikus kiépítéssel kapcsolatos további információért lásd: Kötet manuális létrehozása és használata [Azure Files megosztással.](azure-files-volume.md)

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dinamikusan hozhat Azure Files PV-ket a beépített tárolóosztályok használatával

A tárolóosztályok a Azure Files létrehozási mikéntét határozzák meg. A rendszer automatikusan létrehoz [][node-resource-group] egy tárfiókot a csomópont-erőforráscsoportban, hogy az a tárosztályhoz használja a Azure Files tárolásához. Válasszon egyet a következő [Azure Storage redundancia-termékváltozatok][storage-skus] közül a *skuName esetében:*

* **Standard_LRS:** Standard helyileg redundáns tárolás
* **Standard_GRS:** Standard georedundáns tárolás
* **Standard_ZRS:** Standard zónaredundáns tárolás
* **Standard_RAGRS:** Standard írási hozzáférésű georedundáns tárolás
* **Premium_LRS:** Prémium helyileg redundáns tárolás

> [!NOTE]
> Azure Files támogatja az Azure Premium Storage. A minimális prémium fájlmegosztás 100 GB.

Ha tárolási CSI-illesztőprogramokat használ az AKS-ben, két további beépített eszköz használja a Azure Files `StorageClasses` CSI-tárolóillesztőket. A további CSI-tárolóosztályok a fürthöz a fán belül alapértelmezett tárolási osztályokkal együtt vannak létrehozva.

- `azurefile-csi`: A Azure Standard Storage hoz létre egy Azure Files megosztást.
- `azurefile-csi-premium`: Az Azure Premium Storage használatával hoz létre Azure Files megosztást.

A mindkét tárolási osztályra vonatkozó visszaigénylési szabályzat biztosítja, hogy a mögöttes Azure Files a megfelelő PV törlésekor törlődik. A tárolóosztályok a fájlmegosztásokat is bővíthetőként konfigurálják, csak szerkesztenie kell az állandó kötet jogcímét (AZAZ AZAZ) az új mérettel.

Ezeknek a tárolóosztályoknak a használatával hozzon létre egy [OLYAN POD-t](concepts-storage.md#persistent-volume-claims) és megfelelő podot, amely hivatkozik rájuk, és használja őket. A RENDSZER EGY ADOTT TÁROLÓosztály alapján automatikusan kiépíti a tárolót. A PVC az előre létrehozott tárolóosztályok vagy egy felhasználó által definiált tárolási osztály használatával létrehozhat egy Azure Files a kívánt termékváltozathoz és mérethez. Amikor poddefiníciót hoz létre, a rendszer a KÍVÁNT tároló lekérésére adja meg a PVC-t.

Hozzon létre [egy példát a PVC-re és a `outfile` podra,](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) amely az aktuális dátumot írja ki egy -be a [kubectl apply paranccsal:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Miután a pod fut, a következő parancs futtatásával ellenőrizheti, hogy a fájlmegosztás megfelelően van-e csatlakoztatva, és ellenőrzi, hogy a kimenet tartalmazza-e a `outfile` következőt:

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolóosztály létrehozása

Az alapértelmezett tárolási osztályok megfelelnek a leggyakoribb forgatókönyveknek, de nem mindegyiknek. Bizonyos esetekben érdemes lehet saját tárolóosztályt testre szabni a saját paramétereivel. A fájlmegosztáshoz például a következő jegyzékfájlt `mountOptions` használhatja.

A *fileMode* és *a dirMode* alapértelmezett értéke *0777* a Kubernetes által csatlakoztatott fájlmegosztások esetében. A tárolási osztály objektumán megadhatja a különböző csatlakoztatási beállításokat.

Hozzon létre egy nevű `azure-file-sc.yaml` fájlt, és illessze be a következő példajegyzéket:

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

Hozza létre a storage osztályt a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

A Azure Files CSI-illesztőprogram támogatja az állandó kötetek és a [mögöttes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) fájlmegosztások pillanatképének létrehozását.

Hozzon létre [egy kötet-pillanatképosztályt](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) a [kubectl apply paranccsal:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Hozzon létre [egy kötet-pillanatképet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) az oktatóanyag elején dinamikusan létrehozott [SNAPSHOT-ből.](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile`


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Ellenőrizze, hogy a pillanatkép megfelelően lett-e létrehozva:

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

Ennél nagyobb kötetet is kérhet a LESZŰK-hez. Szerkessze a PVC objektumot, és adjon meg egy nagyobb méretet. Ez a változás elindítja a PV alapjátul szolgáló mögöttes kötet bővítését.

> [!NOTE]
> A jogcím kielégítéséhez soha nem jön létre új PV. Ehelyett átméretez egy meglévő kötetet.

Az AKS-ben a beépített tárolóosztály már támogatja a bővítést, ezért használja a korábban ezzel a tárolóosztálysal létrehozott `azurefile-csi` [PVC-t.](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) A PVC 100Gi-fájlmegosztást kért. Ezt a következő futtatásával ellenőrizhetjük:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Bontsa ki a PVC-t a mező `spec.resources.requests.storage` növelésével:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Ellenőrizze, hogy a POD és a podon belüli fájlrendszer is az új méretet mutatja-e:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-fájlmegosztások
[Azure Files már támogatja az NFS v4.1 protokollt.](../storage/files/storage-files-how-to-create-nfs-shares.md) A Azure Files NFS 4.1-támogatása teljes körűen felügyelt NFS-fájlrendszert biztosít szolgáltatásként, amely egy magas rendelkezésre áll és tartós elosztott rugalmas tárolási platformra épül.

 Ez a beállítás a véletlenszerű hozzáférésű számítási feladatokhoz van optimalizálva, amelyeken adatfrissítések vannak, és teljes körű POSIX fájlrendszer-támogatást nyújt. Ez a szakasz bemutatja, hogyan használhatók az NFS-megosztások az Azure File CSI-illesztővel egy AKS-fürtön.

Ellenőrizze a korlátozásokat [és a](../storage/files/storage-files-compare-protocols.md#limitations) régiók rendelkezésre állását [az](../storage/files/storage-files-compare-protocols.md#regional-availability) előzetes verzió fázisában.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Az előzetes `AllowNfsFileShares` verziójú funkció regisztrálása

NfS 4.1-et kihasználó fájlmegosztás létrehozásához engedélyeznie kell a funkciójelölőt `AllowNfsFileShares` az előfizetésen.

Regisztrálja `AllowNfsFileShares` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.Storage* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Tárfiók létrehozása az NFS-fájlmegosztáshoz

[Hozzon létre egy `Premium_LRS` Azure Storage-fiók](../storage/files/storage-how-to-create-file-share.md) az alábbi konfigurációval az NFS-megosztások támogatásához:
- fióktípus: FileStorage
- biztonságos átvitelre van szükség (csak HTTPS-forgalom engedélyezése): false
- Válassza ki az ügynökcsomópontok virtuális hálózatát a Tűzfalak és virtuális hálózatok csomópontok között, ezért érdemesebb lehet a Tárfiókot létrehozni a MC_ erőforráscsoportban.

### <a name="create-nfs-file-share-storage-class"></a>NFS-fájlmegosztás tárolási osztályának létrehozása

Mentsen `nfs-sc.yaml` egy fájlt az alábbi jegyzékfájllal a megfelelő helyőrzők szerkesztésével.

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

A fájl szerkesztése és mentése után hozza létre a storage osztályt a [kubectl apply paranccsal:][kubectl-apply]

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Üzemelő példány létrehozása NFS-alapú fájlmegosztással
A következő parancs [](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) `data.txt` [kubectl apply][kubectl-apply] paranccsal történő üzembe helyezésével üzembe helyezhet egy állapot-halmazt, amely időbélyegeket ment egy fájlba:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Ellenőrizze a kötet tartalmát a következő futtatásával:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Vegye figyelembe, hogy mivel az NFS-fájlmegosztás prémium szintű fiókban található, a fájlmegosztás minimális mérete 100 GB. Ha kis tárolómérettel hoz létre EGY EGYSZERŰ fájltárolót, a következő hibaüzenet jelenhet meg: "Nem sikerült létrehozni a fájlmegosztást... size (5)...".


## <a name="windows-containers"></a>Windows-tárolók

A Azure Files CSI-illesztőprogram a Windows-csomópontokat és -tárolókat is támogatja. Ha Windows-tárolókat szeretne használni, [](windows-container-cli.md) kövesse a Windows-tárolók oktatóanyagát egy Windows-csomópontkészlet hozzáadásához.

Miután létrehozott egy Windows-csomópontkészletet, használja a beépített tárolóosztályokat(például) vagy `azurefile-csi` hozzon létre egyénieket. A következő parancs kubectl apply paranccsal történő üzembe helyezésével üzembe helyezhet egy [Windows-alapú](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) állapotalapú készletet, amely időbélyegeket ment egy `data.txt` [][kubectl-apply] fájlba:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Ellenőrizze a kötet tartalmát a következő futtatásával:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- A CSI-illesztőprogramok Azure-lemezekhez való használatával lásd: [Azure-lemezek használata CSI-illesztőprogramokkal.](azure-disk-csi.md)
- További információ a tárolással kapcsolatos ajánlott eljárásokról: Ajánlott tárolási és biztonsági mentési eljárások a [Azure Kubernetes Service.][operator-best-practices-storage]


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md