---
title: Gazdagépalapú titkosítás engedélyezése Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan konfigurálhatja a gazdagépalapú titkosítást egy Azure Kubernetes Service -fürtben
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772918"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Gazdagépalapú titkosítás Azure Kubernetes Service (előzetes verzió)

Gazdagépalapú titkosítással az AKS-ügynökcsomópontok virtuális gépének gazdagépe által tárolt adatok titkosítva vannak, és a folyamatok titkosítva vannak a Storage szolgáltatás felé. Ez azt jelenti, hogy az ideiglenes lemezek titkosítása platform által felügyelt kulcsokkal történik. Az operációs rendszer és az adatlemezek gyorsítótára az adott lemezeken beállított titkosítási típustól függően platform által felügyelt vagy felhasználó által kezelt kulcsokkal van titkosítva. Alapértelmezés szerint az AKS használata esetén az operációs rendszer és az adatlemezek titkosítása platform által felügyelt kulcsokkal történik, ami azt jelenti, hogy az ilyen lemezek gyorsítótárai alapértelmezés szerint platform által felügyelt kulcsokkal vannak titkosítva az operációs rendszer és az adatlemezek között.  A Saját kulcsok használata [(BYOK)](azure-disk-customer-managed-keys.md)az Azure-lemezekkel a következő lépésekben adhatja meg a saját felügyelt kulcsait a Azure Kubernetes Service. A lemezek gyorsítótára is titkosítva lesz az ebben a lépésben megadott kulccsal.


## <a name="before-you-begin"></a>Előkészületek

Ez a szolgáltatás csak a fürt vagy a csomópontkészlet létrehozásakor lehet beállítani.

> [!NOTE]
> A gazdagépalapú titkosítás olyan [Azure-régiókban][supported-regions] érhető el, amelyek támogatják az Azure-beli felügyelt lemezek kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép-méretekkel. [][supported-sizes]

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy telepítve van a `aks-preview` CLI-bővítmény 0.4.73-as vagy újabb verziója.
- Győződjön meg arról, hogy `EnableEncryptionAtHostPreview` a szolgáltatás jelzője engedélyezve `Microsoft.ContainerService` van a alatt.

A szolgáltatást engedélyeznie kell az előfizetéséhez, mielőtt az EncryptionAtHost tulajdonságot Azure Kubernetes Service fürthöz. A funkció előfizetéshez való engedélyezéséhez kövesse az alábbi lépéseket:

1. A funkció előfizetéshez való regisztrálásához hajtsa végre a következő parancsot

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. A szolgáltatás kipróbálása előtt ellenőrizze, hogy a regisztrációs állapot Regisztrálva (eltarthat néhány percig) az alábbi paranccsal.

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Gazdagépalapú titkosítást szolgáltató AKS-fürt létrehozásához a legújabb *aks-preview CLI-bővítményre* van szükség. Telepítse az *aks-preview* Azure CLI-bővítményt az [az extension add][az-extension-add] paranccsal, vagy az az [extension update][az-extension-update] paranccsal ellenőrizze az elérhető frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

- Csak új csomópontkészletek esetén engedélyezhető.
- Csak olyan [Azure-régiókban engedélyezhető,][supported-regions] amelyek támogatják az Azure-beli felügyelt lemezek kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép-méretekkel. [][supported-sizes]
- A virtuálisgép-készlet típusaként Virtual Machine Scale Sets AKS-fürtöt és csomópontkészletet *igényel.*

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Gazdagépalapú titkosítás használata új fürtökön (előzetes verzió)

Konfigurálja a fürt ügynökcsomópontokat gazdagépalapú titkosítás használatára a fürt létrehozásakor. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Ha gazdagépalapú titkosítás nélkül szeretne fürtöt létrehozni, ezt a paraméter kihagyása után `--enable-encryption-at-host` használhatja.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Gazdagépalapú titkosítás használata meglévő fürtökön (előzetes verzió)

A meglévő fürtökön a gazdagépalapú titkosítás engedélyezéséhez új csomópontkészletet kell hozzáadnia a fürthöz. Konfigurálja az új csomópontkészletet gazdagépalapú titkosítás használatára a paraméter `--enable-encryption-at-host` használatával.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Ha gazdagépalapú titkosítási funkció nélkül szeretne új csomópontkészleteket létrehozni, ezt a paraméter kihagyása után `--enable-encryption-at-host` használhatja.

## <a name="next-steps"></a>Következő lépések

Tekintse [át az AKS-fürtök biztonságának ajánlott eljárásait][best-practices-security] További információ a gazdagépalapú [titkosításról.](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
