---
title: Az ultra Disk támogatásának engedélyezése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan engedélyezheti és konfigurálhatja az ultra-lemezeket egy Azure Kubernetes Service-(ak-) fürtben
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: c743162ed3f75386287e050443e82069e797ced9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502569"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Az Azure Ultra Disks használata az Azure Kubernetes Service-ben (előzetes verzió)

Az [Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md) nagy teljesítményű, magas IOPS és konzisztens, kis késésű lemezes tárolást biztosít az állapot-nyilvántartó alkalmazások számára. Az ultra Disks szolgáltatás egyik legfőbb előnye, hogy dinamikusan megváltoztathatja az SSD teljesítményét a számítási feladatokkal együtt anélkül, hogy újra kellene indítani az ügynök csomópontjait. Az ultra-lemezek nagy adatigényes számítási feladatokhoz alkalmasak.

## <a name="before-you-begin"></a>Előkészületek

Ez a funkció csak a fürt létrehozásakor vagy a csomópont-készlet létrehozási idején állítható be.

> [!IMPORTANT]
> Az Azure Ultra Disks szolgáltatáshoz a rendelkezésre állási zónákban és az ezeket a lemezeket támogató nodepools, valamint csak adott virtuálisgép-sorozatok szükségesek. Tekintse meg az [**Ultra Disks GA hatókörét és korlátozásait**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Az `EnableUltraSSD` előzetes verzió funkciójának regisztrálása

Ha egy AK-fürtöt vagy egy olyan csomópont-készletet szeretne létrehozni, amely képes az ultra-lemezek kihasználása, engedélyeznie kell a `EnableUltraSSD` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `EnableUltraSSD` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Ha olyan AK-fürtöt vagy csomópont-készletet szeretne létrehozni, amely Ultra-lemezeket használhat, szüksége lesz a legújabb *AK-előnézeti CLI-* bővítményre. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Korlátozások
- Lásd az [ **Ultra Disks GA hatókörét és korlátozásait**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Az ultra-lemezek számára támogatott méretkorlát 100 és 1500 közé esik

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultra Disks-t használó új fürt létrehozása

Hozzon létre egy AK-fürtöt, amely az alábbi CLI-parancsokkal képes kihasználni az ultra-lemezeket. A `--aks-custom-headers` szolgáltatás beállításához használja a jelzőt `EnableUltraSSD` .

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Hozza létre az AK-fürtöt Ultra-lemezek támogatásával.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ha olyan fürtöket szeretne létrehozni, amelyek nem támogatják az ultra Disk támogatást, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Ultra-lemezek engedélyezése meglévő fürtön

A meglévő fürtökön engedélyezheti az ultravékony lemezeket, ha új csomópont-készletet ad hozzá a fürthöz, amely támogatja az ultra-lemezeket. Konfigurálja az új csomópont-készletet, hogy a jelző használatával Ultra-lemezeket használjon `--aks-custom-headers` .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ha új csomópont-készleteket szeretne létrehozni az ultra-lemezek támogatása nélkül, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Az ultra-lemezek dinamikus használata tárolási osztállyal

Ha Ultra-lemezeket kíván használni az üzemelő példányokban vagy az állapot-nyilvántartó készletekben, használhat [tárolási osztályt a dinamikus](azure-disks-dynamic-pv.md)kiépítéshez.

### <a name="create-the-storage-class"></a>A tárolási osztály létrehozása

A tárolási osztály segítségével határozható meg, hogy egy adott tárolási egység hogyan legyen dinamikusan létrehozva állandó kötettel. További információ a Kubernetes tárolásával kapcsolatban: [Kubernetes Storage classs][kubernetes-storage-classes].

Ebben az esetben hozzunk létre egy olyan tárolási osztályt, amely az ultra-lemezekre hivatkozik. Hozzon létre egy nevű fájlt `azure-ultra-disk-sc.yaml` , és másolja a következő jegyzékbe.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal, és adja meg az *Azure-Ultra-Disk-SC. YAML* fájlt:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímek létrehozása

A tárolási osztályok alapján a tárolók automatikus kiépítéséhez állandó mennyiségi jogcím (PVC) használatos. Ebben az esetben a PVC a korábban létrehozott Storage osztály használatával hozza létre az ultra-lemezt.

Hozzon létre egy nevű fájlt `azure-ultra-disk-pvc.yaml` , és másolja a következő jegyzékbe. A jogcím a `ultra-disk` *ReadWriteOnce* -hozzáféréssel rendelkező *1000 GB* méretű lemezt kér. Az *Ultra-Disk-SC* tárolási osztály a tárolási osztályként van megadva.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Hozza létre az állandó kötet jogcímet a [kubectl Apply][kubectl-apply] paranccsal, és adja meg az *Azure-Ultra-Disk-PVC. YAML* fájlt:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Miután létrehozta az állandó kötet jogcímet, és a lemez sikeresen kiépítve, a lemezhez hozzáféréssel rendelkező Pod hozható létre. A következő jegyzékfájl létrehoz egy alapszintű NGINX-Pod-t, amely az *Ultra-Disk* nevű állandó kötet-jogcím használatával csatlakoztatja az Azure-lemezt az elérési útban `/mnt/azure` .

Hozzon létre egy nevű fájlt `nginx-ultra.yaml` , és másolja a következő jegyzékbe.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, ahogy az az alábbi példában is látható:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Most már rendelkezik egy futó Pod lemezzel, amely a `/mnt/azure` címtárban van csatlakoztatva. Ez a konfiguráció megtekinthető a pod-on keresztüli vizsgálat során `kubectl describe pod nginx-ultra` , ahogy az az alábbi tömörített példában is látható:

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>Következő lépések

- További információ az ultra Disks [szolgáltatásról: Azure Ultra Disks használata](../virtual-machines/disks-enable-ultra-ssd.md).
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-storage]

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
