---
title: Gazdagép-alapú titkosítás engedélyezése az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan konfigurálhat gazdagép-alapú titkosítást egy Azure Kubernetes-szolgáltatási (ak-) fürtben
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 66e71dfd6a76fb4e6b464eb5c44dcc809fb9be38
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039733"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Gazdagép-alapú titkosítás az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)

A gazdagép-alapú titkosítással az AK-ügynök csomópontjainak virtuálisgép-gazdagépén tárolt adatok titkosítva maradnak, és a tárolási szolgáltatásba titkosított folyamatok is titkosítottak. Ez azt jelenti, hogy a temp lemezeket a rendszer a platform által felügyelt kulcsokkal titkosítja. Az operációs rendszer és az adatlemezek gyorsítótára inaktív állapotban van, vagy a platform által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal a lemezeken beállított titkosítási típustól függően. Alapértelmezés szerint az AK-ban az operációs rendszer és az adatlemezek titkosítva vannak a platform által felügyelt kulcsokkal, ami azt jelenti, hogy ezeknek a lemezeknek a gyorsítótárai is alapértelmezés szerint titkosítva vannak a platform által felügyelt kulcsokkal.  Megadhatja saját felügyelt kulcsait a [saját kulcsok (BYOK) Azure-lemezekkel való használatával az Azure Kubernetes szolgáltatásban](azure-disk-customer-managed-keys.md). A lemezek gyorsítótára ezután az ebben a lépésben megadott kulccsal is titkosítva lesz.


## <a name="before-you-begin"></a>Előkészületek

Ez a funkció csak a fürt létrehozásakor vagy a csomópont-készlet létrehozási idején állítható be.

> [!NOTE]
> A gazdagép-alapú titkosítás olyan [Azure-régiókban][supported-regions] érhető el, amelyek támogatják az Azure Managed Disks kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép- [méretekkel][supported-sizes]rendelkeznek.

### <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy telepítve van a `aks-preview` CLI bővítmény v 0.4.73 vagy újabb verziója.
- Győződjön meg arról, hogy engedélyezve van a `EnableEncryptionAtHostPreview` funkció jelzője `Microsoft.ContainerService` .

Ahhoz, hogy a virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz titkosítást lehessen használni a gazdagépen, be kell szereznie a funkciót az előfizetésében. Küldje el az **encryptionAtHost@microsoft.com** előfizetési azonosítókat az előfizetések funkciójának beszerzéséhez. 

> [!IMPORTANT]
> **encryptionAtHost@microsoft.com** Ahhoz, hogy a szolgáltatás engedélyezve legyen a számítási erőforrások számára, e-mailt kell kapnia az előfizetési azonosítókkal. Számítási erőforrások esetében nem engedélyezheti saját magát.


### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

A gazdagép-alapú titkosítással rendelkező AK-fürtök létrehozásához a legújabb *AK-előnézet CLI-* bővítményre van szükség. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal, vagy keresse meg a rendelkezésre álló frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

- Csak új csomópont-készleteken vagy új fürtökön engedélyezhető.
- Csak olyan [Azure-régiókban][supported-regions] engedélyezhető, amelyek támogatják az Azure Managed Disks kiszolgálóoldali titkosítását, és csak bizonyos támogatott virtuálisgép- [méretekkel][supported-sizes]rendelkeznek.
- A (z) Virtual Machine Scale Sets (VMSS) alapján, virtuálisgép- *készlet típusú* fürt és csomópont-készlet szükséges.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Gazdagép-alapú titkosítás használata új fürtökön (előzetes verzió)

Konfigurálja a Fürtfelügyelő csomópontjait a gazdagép-alapú titkosítás használatára a fürt létrehozásakor. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Ha gazdagép-alapú titkosítás nélkül szeretné létrehozni a fürtöket, ezt a paraméter kihagyása mellett teheti meg `--enable-encryption-at-host` .

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Gazdagép-alapú titkosítás használata meglévő fürtökön (előzetes verzió)

A meglévő fürtökön a gazdagép-alapú titkosítást úgy engedélyezheti, ha új csomópont-készletet ad hozzá a fürthöz. Konfigurálja az új csomópont-készletet a gazdagép-alapú titkosítás használatára a `--enable-encryption-at-host` paraméter használatával.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Ha a gazdagép-alapú titkosítási funkció nélkül szeretne új csomópont-készleteket létrehozni, ezt a paraméter kihagyása mellett teheti meg `--enable-encryption-at-host` .

## <a name="next-steps"></a>Következő lépések

Tekintse át a [gazdagép-alapú titkosításról](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)szóló [ajánlott eljárásokat az AK-fürtök biztonsága][best-practices-security] című cikkből.


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
