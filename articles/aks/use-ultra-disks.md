---
title: A utralemez (AKS) Azure Kubernetes Service engedélyezése
description: Megtudhatja, hogyan engedélyezheti és konfigurálhatja ultralemezek egy Azure Kubernetes Service -fürtben
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767348"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Azure ultralemezek használata a Azure Kubernetes Service (előzetes verzió)

[Az Azure ultralemezei](../virtual-machines/disks-enable-ultra-ssd.md) magas átviteli sebességet, magas IOPS-t és konzisztensen alacsony késésű lemeztárolást nyújtanak az állapot-alapú alkalmazások számára. Az ultralemezek egyik fő előnye, hogy az SSD teljesítményét dinamikusan módosíthatja a számítási feladatokkal együtt anélkül, hogy újra kellene indítania az ügynökcsomópontokat. Az ultralemezek nagy adatigényű számítási feladatokhoz megfelelőek.

## <a name="before-you-begin"></a>Előkészületek

Ez a szolgáltatás csak a fürt vagy a csomópontkészlet létrehozásakor lehet beállítani.

> [!IMPORTANT]
> Az Azure-beli ultralemezek rendelkezésre állási zónákban és régiókban üzembe helyezett csomópontkészleteket igényelnek, amelyek támogatják ezeket a lemezeket, valamint csak bizonyos virtuálisgép-sorozatokat. Lásd: [**Ultralemezek GA hatóköre és korlátozásai.**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)

### <a name="register-the-enableultrassd-preview-feature"></a>Az előzetes `EnableUltraSSD` verziójú funkció regisztrálása

Az ultralemezeket kihasználó AKS-fürt vagy csomópontkészlet létrehozásához engedélyeznie kell a funkciójelölőt `EnableUltraSSD` az előfizetésén.

Regisztrálja `EnableUltraSSD` a funkciójelölőt az [az feature register paranccsal][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* A regisztráció állapotát az az feature list paranccsal [ellenőrizheti:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Amikor elkészült, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját [az az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Ha olyan AKS-fürtöt vagy csomópontkészletet kell létrehoznia, amely ultralemezek, a legújabb *aks-preview CLI-bővítményre* lesz szüksége. Telepítse az *aks-preview* Azure CLI-bővítményt az [az extension add][az-extension-add] paranccsal, vagy telepítse az elérhető frissítéseket az az extension update [paranccsal:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Korlátozások
- Lásd: [ **Ultralemezek GA hatóköre és korlátozásai**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Az ultralemezek támogatott mérettartománya 100 és 1500 közé esik

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultralemezeket használni képes új fürt létrehozása

Hozzon létre egy AKS-fürtöt, amely képes ultralemezek parancssori felület alábbi parancsokkal való használatával. A funkció `--aks-custom-headers` beállítását a `EnableUltraSSD` jelzővel használhatja.

Hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Hozza létre az AKS-fürtöt a ultralemezek.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ha ultralemez-támogatás nélkül szeretne fürtöt létrehozni, ezt az egyéni paraméter kihagyása után `--aks-custom-headers` használhatja.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Ultralemezek engedélyezése meglévő fürtön

Az ultralemezek meglévő fürtökön való engedélyezéséhez új csomópontkészletet kell hozzáadnia a fürthöz, amely támogatja az ultralemezeket. Konfigurálja az új csomópontkészletet ultralemezek használatára a `--aks-custom-headers` jelzővel.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ha az ultralemezek támogatása nélkül szeretne új csomópontkészleteket létrehozni, ezt az egyéni paraméter kihagyása után `--aks-custom-headers` használhatja.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Ultralemezek dinamikus használata tárolóosztályokkal

Ha ultralemezeket használ az üzemelő példányainkban vagy állapot-állapot-halmazainkban, használhat tárolóosztályt a [dinamikus üzembe helyezéshez.](azure-disks-dynamic-pv.md)

### <a name="create-the-storage-class"></a>A tárolási osztály létrehozása

A tárolóosztály annak meghatározására használható, hogy a rendszer hogyan hoz létre dinamikusan egy tárolóegységet egy állandó köteten. A Kubernetes-tárolóosztályokkal kapcsolatos további információkért lásd: [Kubernetes Storage-osztályok.][kubernetes-storage-classes]

Ebben az esetben egy olyan tárolóosztályt hozunk létre, amely ultralemezre hivatkozik. Hozzon létre egy nevű `azure-ultra-disk-sc.yaml` fájlt, és másolja be a következő jegyzékfájlt.

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

Hozza létre a storage osztályt a [kubectl apply paranccsal,][kubectl-apply] és adja meg *az azure-ultra-disk-sc.yaml fájlt:*

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímének létrehozása

Az állandó kötet jogcímek (PERSISTENT) segítségével a rendszer automatikusan kiépíti a tárolót egy tárolóosztály alapján. Ebben az esetben a PVC a korábban létrehozott tárolóosztály használatával hozhat létre egy ultralemezt.

Hozzon létre egy nevű `azure-ultra-disk-pvc.yaml` fájlt, és másolja be az alábbi jegyzékfájlt. A jogcím egy `ultra-disk` *1000 GB* méretű, *ReadWriteOnce* hozzáféréssel rendelkező lemezt igényel. Az *ultra-disk-sc tárolási* osztály van megadva tárolási osztályként.

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

Hozza létre az állandó kötet jogcímét a [kubectl apply paranccsal,][kubectl-apply] és adja meg az *azure-ultra-disk-pvc.yaml fájlt:*

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Az állandó kötet jogcímének létrehozása és a lemez sikeres üzembe állítása után egy pod is létre lehet hozva a lemezhez való hozzáféréssel. A következő jegyzékfájl egy alapszintű NGINX-podot hoz létre, amely az ultra-disk nevű állandó kötet jogcímet használja az *Azure-lemez* csatlakoztatáshoz a elérési `/mnt/azure` úton.

Hozzon létre egy nevű `nginx-ultra.yaml` fájlt, és másolja be az alábbi jegyzékfájlt.

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

Hozza létre a podot a [kubectl apply paranccsal][kubectl-apply] az alábbi példában látható módon:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Most már van egy futó podja, amely az Azure-lemezt csatlakoztatja a `/mnt/azure` könyvtárhoz. Ez a konfiguráció akkor látható, amikor a segítségével megvizsgálja a podot, ahogy az a következő rövid `kubectl describe pod nginx-ultra` példában látható:

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

- További információ az ultralemezekkel kapcsolatban: [Using Azure ultra disks (Azure ultralemezek használata).](../virtual-machines/disks-enable-ultra-ssd.md)
- További információ a tárolással kapcsolatos ajánlott eljárásokról: Ajánlott tárolási és biztonsági mentési eljárások Azure Kubernetes Service [(AKS)][operator-best-practices-storage]

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
