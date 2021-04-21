---
title: Statikus kötet létrehozása podok számára a Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan hozhat létre manuálisan Azure-lemezeket a podokkal való használatra a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776212"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Tartós kötet manuális létrehozása és használata Azure-lemezekkel az Azure Kubernetes Service-ben (AKS)

A tárolóalapú alkalmazásoknak gyakran kell hozzáférniük az adatokhoz, és egy külső adatkötetben meg kellőrzeni az adatokat. Ha egyetlen podnak hozzá kell férni a tárterülethez, azure-lemezekkel egy natív kötetet is bemutathat az alkalmazáshasználathoz. Ez a cikk bemutatja, hogyan hozhat létre manuálisan Azure-lemezt, és csatolhatja azt egy podhoz az AKS-ban.

> [!NOTE]
> Az Azure-lemezek egyszerre csak egy podhoz csatlakoztathatóak. Ha egy állandó kötetet kell megosztania több pod között, használja a [Azure Files.][azure-files-volume]

További információ a Kubernetes-kötetekkel kapcsolatban: Tárolási lehetőségek alkalmazásokhoz az [AKS-ban.][concepts-storage]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-disk"></a>Azure-lemez létrehozása

Amikor létrehoz egy Azure-lemezt az AKS-sel való használatra, a lemezerőforrást a csomópont **erőforráscsoportban** hozhatja létre. Ez a megközelítés lehetővé teszi az AKS-fürt számára a lemezerőforráshoz való hozzáférést és annak kezelését. Ha ehelyett egy külön erőforráscsoportban hozza létre a lemezt, akkor a fürt Azure Kubernetes Service (AKS) által felügyelt identitása számára szerepkört kell biztosít a lemez `Contributor` erőforráscsoportja számára.

Ebben a cikkben a csomópont erőforráscsoportban hozza létre a lemezt. Először szerezze be az erőforráscsoport nevét az [az aks show paranccsal,][az-aks-show] és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. Az alábbi példa a myResourceGroup nevű erőforráscsoportban lekért *myAKSCluster AKS-fürt* nevének *csomópont-erőforráscsoportját:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy lemezt [az az disk create paranccsal.][az-disk-create] Adja meg az előző parancsban lekért csomópont-erőforráscsoport nevét, majd a lemezerőforrás nevét( *például myAKSDisk).* Az alábbi példa egy *20* GiB-os lemezt hoz létre, és a létrehozást követően ki is adja a lemez azonosítóját. Ha Windows Server-tárolókhoz szükséges lemezt kell létrehoznia, adja hozzá a paramétert a lemez `--os-type windows` megfelelő formázása érdekében.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Az Azure-lemezek termékváltozat alapján vannak kiszámlázva egy adott méretért. Ezek az S4- vagy P4-lemezek 32GiB és S80 vagy P80 lemezek esetén 32TiB-osak (előzetes verzióban). A prémium szintű felügyelt lemezek átviteli sebességének és IOPS-teljesítményének mértéke az AKS-fürtben lévő csomópontok termékváltozatától és példányméretétől is függ. Lásd [a Managed Disks díjszabását és Managed Disks.][managed-disk-pricing-performance]

A lemezerőforrás-azonosító a parancs sikeres befejezése után jelenik meg, ahogyan az alábbi kimenetben látható. Ezzel a lemezazonosítóval csatlakoztatja a lemezt a következő lépésben.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Lemez csatlakoztatása kötetként

Az Azure-lemezt a podhoz való csatlakoztatáshoz konfigurálja a kötetet a tároló specifikációi között. Hozzon létre egy nevű új `azure-disk-pod.yaml` fájlt az alábbi tartalommal. Frissítse az adatokat az előző lépésben létrehozott lemez nevére, valamint a lemezazonosítóra, amely a `diskName` `diskURI` disk create parancs kimenetében látható. Ha szükséges, frissítse a et, amely az az elérési út, ahová az `mountPath` Azure-lemez csatlakoztatva van a podhoz. Windows Server-tárolók esetén adjon meg egy *mountPath útvonalat* a Windows elérésiút-konvenciójának (például *"D:" ) használatával.*

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

A pod `kubectl` létrehozásához használja az parancsot.

```console
kubectl apply -f azure-disk-pod.yaml
```

Most már van egy futó podja, amely egy azure-lemezzel van csatlakoztatva `/mnt/azure` a-ban. A használatával `kubectl describe pod mypod` ellenőrizheti, hogy a lemez sikeresen csatlakoztatva van-e. Az alábbi rövid példakimenet a tárolóhoz csatlakoztatott kötetet mutatja:

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

A kapcsolódó ajánlott eljárásokért lásd: Ajánlott eljárások az AKS-sel való tároláshoz és biztonsági [mentéshez.][operator-best-practices-storage]

Az Azure-lemezekkel kommunikáló AKS-fürtökről az [Azure Diskshez elérhető Kubernetes beépülő modulban található további információ.][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
