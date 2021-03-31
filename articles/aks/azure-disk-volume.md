---
title: Statikus kötet létrehozása hüvelyekhez az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan hozhat létre manuálisan Azure-lemezekkel rendelkező köteteket az Azure Kubernetes Service (ak) szolgáltatásban található Pod használatával
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7d8a038926fc6bf3234b43a82c0259ba633df11e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506650"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure-lemezekkel rendelkező kötet manuális létrehozása és használata az Azure Kubernetes szolgáltatásban (ak)

A tároló-alapú alkalmazásoknak gyakran kell elérniük és megőrizniük az adatmennyiséget egy külső adatköteten. Ha egyetlen Pod-hozzáférésre van szükség a tárolóhoz, az Azure-lemezek használatával natív kötetet adhat az alkalmazás használatához. Ebből a cikkből megtudhatja, hogyan hozhat létre manuálisan Azure-lemezeket, és hogyan csatlakoztathatja egy Pod-hez az AK-ban.

> [!NOTE]
> Az Azure-lemezek egyszerre csak egyetlen Pod csatlakoztatására használhatók. Ha egy állandó kötetet több hüvelyben kell megosztania, használja a [Azure Files][azure-files-volume].

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-disk"></a>Azure-lemez létrehozása

Ha az AK-val való használatra létrehoz egy Azure-lemezt, akkor a **csomópont** -erőforráscsoport létrehozhatja a lemez erőforrását. Ez a megközelítés lehetővé teszi az AK-fürt számára a lemez erőforrásának elérését és kezelését. Ha ehelyett külön erőforráscsoporthoz hozza létre a lemezt, meg kell adnia az Azure Kubernetes szolgáltatás (ak) felügyelt identitását a fürt számára a `Contributor` szerepkört a lemez erőforráscsoporthoz.

Ehhez a cikkhez hozza létre a lemezt a csomópont-erőforráscsoporthoz. Először kérje le az erőforráscsoport nevét az az [az AK show][az-aks-show] paranccsal, és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa lekéri a csomópont-erőforráscsoportot az AK-fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy lemezt az az [Disk Create][az-disk-create] paranccsal. Adja meg az előző parancsban beszerzett csomópont-erőforráscsoport nevét, majd a lemez erőforrásának nevét, például *myAKSDisk*. A következő példa egy *20* GIB-lemezt hoz létre, és a létrehozás után kiírja a lemez azonosítóját. Ha Windows Server-tárolókkal használandó lemezt kell létrehoznia, adja hozzá a `--os-type windows` paramétert a lemez megfelelő formázásához.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Az Azure-lemezek számlázása az SKU alapján történik egy adott méretre. Ezek az SKU-32GiB az S4 vagy a P4 lemezeken a S80-vagy P80-lemezek 32TiB (előzetes verzió). A prémium szintű felügyelt lemez átviteli sebessége és IOPS teljesítménye az AK-fürtben található csomópontok SKU-jának és példányának méretétől függ. Lásd: [Managed Disks díjszabása és teljesítménye][managed-disk-pricing-performance].

A lemez erőforrás-azonosítója a parancs sikeres befejeződése után jelenik meg, ahogy az a következő példában látható. Ez a lemez-azonosító a lemez a következő lépésben való csatlakoztatására szolgál.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Lemez csatlakoztatása kötetként

Az Azure-lemez Pod-ba való csatlakoztatásához konfigurálja a kötetet a tároló specifikációjában. Hozzon létre egy nevű új fájlt `azure-disk-pod.yaml` a következő tartalommal. Frissítse az `diskName` előző lépésben létrehozott lemez nevét és `diskURI` a lemez létrehozása parancs kimenetében látható lemez azonosítóját. Ha szükséges, frissítse a `mountPath` -t, amely az az elérési út, ahol az Azure-lemez csatlakoztatva van a pod-hoz. Windows Server-tárolók esetén a Windows PATH Convention (például *'d:*) használatával válasszon egy *mountPath* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

A `kubectl` parancs használatával hozza létre a pod-t.

```console
kubectl apply -f azure-disk-pod.yaml
```

Most már rendelkezik egy futó Pod lemezzel, amely a következőhöz van csatlakoztatva: `/mnt/azure` . `kubectl describe pod mypod`A használatával ellenőrizheti, hogy a lemez csatlakoztatása sikeres volt-e. A következő összefoglalt példa kimenet a tárolóban csatlakoztatott kötetet mutatja:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

További információ az AK-fürtökről az Azure-lemezekkel kapcsolatban: [Kubernetes beépülő modul Azure-lemezekhez][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
