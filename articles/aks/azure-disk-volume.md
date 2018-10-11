---
title: Podok statikus kötetet létrehozni az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan hozhat létre manuálisan egy kötet a podot Azure Kubernetes Service (AKS) segítségével Azure-lemezek
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 9c5879474568885d9a705e7bfd16e2a4e2304b96
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068184"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Manuális létrehozásához és a egy kötet használata Azure-lemezek az Azure Kubernetes Service (AKS)

Tárolóalapú alkalmazások gyakran kell eléréséhez, és a egy külső adatmennyiség adatok megőrzése. Ha egy podot kell a hozzáférést a tárolóhoz, az alkalmazások által használható natív kötet jelenleg Azure lemez is használható. Ez a cikk bemutatja, hogyan manuálisan hozzon létre egy Azure-lemez, és mellékelje egy pod az aks-ben.

> [!NOTE]
> Egy Azure-lemez csak csatlakoztathatók a egy podot egyszerre. Ha a tartós kötet megosztása több podok van szüksége, használja a [Azure Files][azure-files-volume].

A Kubernetes-köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.46 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-disk"></a>Hozzon létre egy Azure-lemez

Ha egy Azure-lemez használatra hoz létre az aks-sel, hozhat létre a lemezerőforrást a a **csomópont** erőforráscsoportot. Ez a megközelítés lehetővé teszi, hogy az AKS-fürt eléréséhez és kezeléséhez a lemezerőforrást. Hozzon létre a lemezt egy külön erőforráscsoportban, ha a fürt számára kell biztosítania az Azure Kubernetes Service (AKS) egyszerű szolgáltatás a `Contributor` szerepkör a lemez erőforrás-csoportba.

Ebben a cikkben a lemez létrehozása a csomópont erőforráscsoportban. Először kérje le az erőforráscsoport neve az a [az aks show] [ az-aks-show] parancsot, majd adja hozzá a `--query nodeResourceGroup` lekérdezési paraméter. Az alábbi példa lekéri az AKS-fürt nevét a csomópont erőforráscsoport *myAKSCluster* az erőforráscsoport nevét a *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy lemez a [az lemez létrehozása] [ az-disk-create] parancsot. Adja meg az előző parancs, és a lemez-erőforrás neve például előállított csomópont erőforráscsoportnevet *myAKSDisk*. A következő példában létrehozunk egy *20*GiB lemez, és ezután a lemez Azonosítóját kimenetére:

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Az Azure disks számlázása Termékváltozat által egy adott méretet. Ezen SKU-k és közé eső 32GiB S4 vagy P4 szintű lemezek 8TiB S60 vagy P60 lemezek. Az átviteli sebesség és IOPS-teljesítmény egy prémium szintű felügyelt lemez a Termékváltozat és a példány mérete az AKS-fürt csomópontjainak függ. Lásd: [díjszabás és a felügyelt lemezek teljesítményének][managed-disk-pricing-performance].

A lemez erőforrás-azonosító jelenik meg a parancs sikeres befejezését követően az alábbi példa kimenetében látható módon. A Lemezazonosító segítségével csatlakoztassa a lemezt, a következő lépésben.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Lemez csatolása kötetként

Az Azure-lemez csatlakoztatása a pod be, konfigurálja a kötetet a tároló specifikációja. Hozzon létre egy új fájlt `azure-disk-pod.yaml` a következő tartalommal. Frissítés `diskName` a lemezt az előző lépésben létrehozott nevével és `diskURI` a lemez Azonosítóját, a lemez kimenetben látható a create paranccsal. Ha szükséges, frissítse a `mountPath`, azaz az elérési utat, amelyen az Azure-lemez található a pod csatlakoztatva van.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
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

Használja a `kubectl` paranccsal hozza létre a pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Egy Azure-lemez csatlakoztatásának helye a most már rendelkezik egy futó pod `/mnt/azure`. Használhat `kubectl describe pod mypod` ellenőrizze a lemez sikeresen csatlakoztatva van. A következő sűrített példához kimenetet jeleníti meg a kötet csatlakoztatva van a tárolóban:

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

## <a name="next-steps"></a>További lépések

További információ az AKS fürtök kezelése az Azure-lemezek, lásd: a [Kubernetes beépülő modul az Azure Disks][kubernetes-disks].

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