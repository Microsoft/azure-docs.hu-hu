---
title: A Container Storage Interface (CSI) illesztőprogramok engedélyezése Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan engedélyezheti a Container Storage Interface (CSI) illesztőprogramokat az Azure-lemezeken és Azure Files egy Azure Kubernetes Service (AKS-) fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c9edfdf1c9740ec1fdaaeeedbc6ba92793eb0b3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779956"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>A Container Storage Interface (CSI) illesztőprogramok engedélyezése Azure-lemezekhez és Azure Files on Azure Kubernetes Service (AKS) (előzetes verzió)

A Container Storage Interface (CSI) egy olyan szabvány, amely tetszőleges blokk- és fájltároló rendszereket tár fel a Kubernetes tárolóba ezett számítási feladatai számára. A CSI alkalmazásával és használatával az Azure Kubernetes Service (AKS) képes a beépülő modulok írását, üzembe helyezését és iterálását a Kubernetesben található új tárolórendszerek felfedése vagy javítása érdekében anélkül, hogy hozzá kellene érnie az alapvető Kubernetes-kódhoz, és meg kellene várnia a kiadási ciklusokat.

A CSI tárolóillesztő támogatása az AKS-hez lehetővé teszi a natív használatot:
- [*Azure-lemezek,*](azure-disk-csi.md)amelyek Kubernetes *DataDisk-erőforrások létrehozására* használhatók. A lemezek az Azure Premium Storage, nagy teljesítményű VAGY Azure Standard Storage, hagyományos HDD-k vagy Standard HDD-k használatával. A legtöbb éles és fejlesztési számítási feladathoz használja a Premium Storage. Az Azure-lemezek *ReadWriteOnce* ként vannak csatlakoztatva, így csak egyetlen pod számára érhetők el. Olyan tárolókötetek esetén, amelyekhez egyszerre több pod is hozzáfér, használja a Azure Files.
- [*Azure Files,*](azure-files-csi.md)amely egy Azure Storage-fiók által a podok számára egy SMB 3.0-megosztás csatlakoztatásra használható. A Azure Files több csomópont és pod között oszthat meg adatokat. Azure Files a Azure Standard Storage HDD-k vagy a nagy teljesítményű HDD-k által Premium Storage Azure-beli virtuális Premium Storage is használhatók.

> [!IMPORTANT]
> A Kubernetes 1.21-es verziójától kezdődően a Kubernetes csak CSI-illesztőprogramokat fog használni, alapértelmezés szerint. Ezek az illesztőprogramok a Kubernetes tárolási támogatásának jövőbeli jövője.
>
> *A fastruktúrában található illesztőprogramok* az alapvető Kubernetes-kód részét képezi, és az új CSI-illesztőprogramok, azaz a beépülő modulok.

## <a name="limitations"></a>Korlátozások

- Ez a funkció csak a fürt létrehozásakor lehet beállítani.
- A CSI-illesztőprogramokat támogató minimális Kubernetes-alverzió az 1.17-es verzió.
- Az előzetes verzióban az alapértelmezett tárolási osztály továbbra is ugyanaz a [fatárolóosztály lesz.](concepts-storage.md#storage-classes) Miután ez a szolgáltatás általánosan elérhetővé válik, az alapértelmezett tárolási osztály lesz a tárolási osztály, és a fa `managed-csi` tárolóosztályok el lesznek távolítva.
- Az első előzetes verzió fázisában csak az Azure CLI támogatott.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Az előzetes `EnableAzureDiskFileCSIDriver` verziójú funkció regisztrálása

Olyan AKS-fürt létrehozásához, amely képes CSI-illesztőprogramokat használni az Azure-lemezekhez és Azure Files, engedélyeznie kell a funkciójelölőt `EnableAzureDiskFileCSIDriver` az előfizetésén.

Regisztrálja `EnableAzureDiskFileCSIDriver` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

A CSI-tárolóillesztőket használni képes AKS-fürt vagy csomópontkészlet létrehozásához a legújabb *aks-preview Azure CLI-bővítmény* szükséges. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add paranccsal.][az-extension-add] Vagy telepítse az elérhető frissítéseket az [az extension update paranccsal.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>CSI-tárolóillesztők használatára képes új fürt létrehozása

Hozzon létre egy új fürtöt, amely KÉPES CSI-tárolóillesztőket használni az Azure-lemezekhez, Azure Files parancssori felület alábbi parancsai segítségével. A funkció `--aks-custom-headers` beállítását a `EnableAzureDiskFileCSIDriver` jelzővel használhatja.

Hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Hozza létre a CSI-tárolóillesztőket támogató AKS-fürtöt:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Ha CSI-tárolóillesztők helyett fatároló-illesztőprogramban szeretne fürtöket létrehozni, ezt az egyéni paraméter `--aks-custom-headers` kihagyása után használhatja.


A következő futtatásával ellenőrizze, hogy hány Lemezalapú Azure-kötet csatolhat ehhez a csomóponthoz:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Következő lépések

- A CSI-meghajtó Azure-lemezekhez való használatával lásd: [Azure-lemezek használata CSI-illesztőprogramokkal.](azure-disk-csi.md)
- Ha a CSI-meghajtót a Azure Files használni, tekintse meg [a Azure Files CSI-illesztőprogramokkal való használatát.](azure-files-csi.md)
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