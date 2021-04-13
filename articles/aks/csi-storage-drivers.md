---
title: A Container Storage Interface (CSI) illesztőprogramjainak engedélyezése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan engedélyezheti az Azure-lemezek és-Azure Files számára készült Container Storage Interface (CSI) illesztőprogramokat az Azure Kubernetes Service (ak) fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: ec8aeb07e54c6ec49647e7bb65284b6cb7343555
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305667"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>A Container Storage Interface (CSI) illesztőprogramjainak engedélyezése Azure-lemezekhez és Azure Files az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)

A Container Storage Interface (CSI) egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszerek számára teszi elérhetővé a Kubernetes tárolt számítási feladatokat. A CSI segítségével az Azure Kubernetes Service (ak) segítségével írhat, telepíthet és megismételheti a beépülő modulokat, hogy új vagy javítsa a meglévő tárolási rendszereket a Kubernetes anélkül, hogy meg kellene érintenie az alapvető Kubernetes-kódot, és várnia kell a kiadási ciklusokra.

A CSI Storage-illesztőprogram támogatja az AK-t, így natív módon használható:
- [*Azure-lemezek*](azure-disk-csi.md), amelyek Kubernetes *adatlemez* -erőforrások létrehozására használhatók. A lemezek a nagy teljesítményű SSD-k, illetve az Azure standard Storage által támogatott Azure Premium Storage-t is használhatják, a normál HDD-k vagy a standard SSD-k használatával. A legtöbb éles és fejlesztési számítási feladathoz használja a Premium Storage. Az Azure-lemezek *ReadWriteOnce*-ként vannak csatlakoztatva, így csak egyetlen Pod számára érhető el. A több hüvelyrel egyidejűleg elérhető tárolási kötetek esetében használja a Azure Files.
- [*Azure Files*](azure-files-csi.md), amely egy Azure Storage-fiók által a hüvelyek számára támogatott SMB 3,0-megosztás csatlakoztatására használható. A Azure Files használatával több csomóponton és hüvelyen is megoszthatja az adategységeket. A Azure Files a standard HDD-k vagy az Azure-Premium Storage által támogatott, nagy teljesítményű SSD-ket támogató Azure standard Storage-t is használhatja.

> [!IMPORTANT]
> A Kubernetes 1,21-es verziójától kezdődően a Kubernetes csak a CSI-illesztőprogramokat és alapértelmezés szerint fogja használni. Ezek az illesztőprogramok a tárolók támogatásának jövője a Kubernetes-ben.
>
> A *fában lévő illesztőprogramok* az alapszintű Kubernetes-kód részét képező, az új CSI-illesztőprogramok, amelyek beépülő modulok.

## <a name="limitations"></a>Korlátozások

- Ez a funkció csak fürt létrehozási idején állítható be.
- A CSI-illesztőprogramokat támogató minimális Kubernetes-alverzió v 1.17.
- Az előzetes verzió ideje alatt az alapértelmezett tárolási osztály továbbra is [ugyanaz lesz a fán tárolt tárolási osztályban](concepts-storage.md#storage-classes). Miután ez a funkció általánosan elérhetővé válik, az alapértelmezett tárolási osztály lesz `managed-csi` eltávolítva.
- Az első előzetes fázisban csak az Azure CLI támogatott.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Az `EnableAzureDiskFileCSIDriver` előzetes verzió funkciójának regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely a CSI-illesztőprogramokat használja az Azure-lemezekhez és a Azure Fileshoz, engedélyeznie kell a `EnableAzureDiskFileCSIDriver` szolgáltatás jelölőjét az előfizetésében.

Regisztrálja a `EnableAzureDiskFileCSIDriver` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Ha egy AK-alapú fürtöt vagy egy olyan csomópont-készletet szeretne létrehozni, amely a CSI Storage-illesztőprogramokat használja, szüksége lesz a legújabb *AK – előzetes* verzió Azure CLI-bővítményre. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>A CSI Storage-illesztőprogramokat használó új fürt létrehozása

Hozzon létre egy új fürtöt, amely a következő CLI-parancsokkal használhatja a CSI Storage-illesztőprogramokat az Azure-lemezekhez és a Azure Fileshoz. A `--aks-custom-headers` szolgáltatás beállításához használja a jelzőt `EnableAzureDiskFileCSIDriver` .

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Az AK-fürt létrehozása a CSI Storage-illesztőprogramok támogatásával:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Ha a CSI Storage-illesztőprogramok helyett fatároló-illesztőprogramokban szeretne fürtöket létrehozni, ezt az egyéni paraméter kihagyása mellett teheti meg `--aks-custom-headers` .


Győződjön meg arról, hogy hány Azure Disk-alapú kötetet tud csatlakoztatni ehhez a csomóponthoz a következő futtatásával:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Következő lépések

- Az Azure-lemezek CSI-meghajtójának használatához lásd: az [Azure-lemezek használata a CSI-illesztőprogramokkal](azure-disk-csi.md).
- A Azure Files CSI-meghajtójának használatához tekintse meg a [Azure Files használata a CSI-illesztőprogramokkal](azure-files-csi.md)című témakört.
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban][operator-best-practices-storage].

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