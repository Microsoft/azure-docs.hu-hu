---
title: Azure Kubernetes szolgáltatás (ak) a rendelkezésre állási SLA-val
description: Ismerje meg az Azure Kubernetes Service (ak) API-kiszolgáló opcionális, rendelkezésre állási SLA-ajánlatát.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 69a4955f28bbd42cd7bf5651bd057412e15303de
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952915"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes szolgáltatás (ak) üzemidő SLA

A rendelkezésre állási SLA egy opcionális funkció, amely lehetővé teszi egy adott fürt pénzügyi támogatását, magasabb szintű SLA-t. A rendelkezésre állási SLA garantálja a Kubernetes API-kiszolgáló végpontjának 99,95%-os rendelkezésre állását olyan fürtök esetében, amelyek [Availability Zones][availability-zones] és 99,9%-ot használnak a nem a Availability Zones használó fürtök számára. Az AK fő csomópont-replikákat használ a frissítési és a tartalék tartományok között az SLA-követelmények teljesítésének biztosítása érdekében.

Azok az ügyfelek, akik SLA-t igényelnek a megfelelőségi követelmények kielégítése érdekében, vagy a végfelhasználók számára az SLA kibővítését igénylik a funkció engedélyezéséhez. A magasabb rendelkezésre állási SLA előnyeit kihasználó kritikus számítási feladatokkal rendelkező ügyfelek is hasznosak lehetnek. A Availability Zones rendelkezésre állási SLA funkciójának használata lehetővé teszi, hogy a Kubernetes API-kiszolgáló kihasználtsága nagyobb legyen.  

Az ügyfelek továbbra is létrehozhatnak korlátlan számú ingyenes fürtöt 99,5%-os szolgáltatási szintű célkitűzéssel (SLO), és igény szerint választhatják az előnyben részesített SLO-t vagy SLA-t.

> [!Important]
> A kilépési zárolást tartalmazó fürtök esetében lásd: a [kimenő forgalom korlátozása](limit-egress-traffic.md) a megfelelő portok megnyitására.

## <a name="region-availability"></a>Régiónkénti elérhetőség

* A rendelkezésre állási SLA nyilvános régiókban és Azure Government-régiókban érhető el, ahol az [AK támogatott](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* [A rendelkezésre][private-clusters] állási SLA minden olyan nyilvános régióban elérhető, ahol az AK támogatott.

## <a name="sla-terms-and-conditions"></a>SLA-feltételek és kikötések

A rendelkezésre állási SLA egy fizetős szolgáltatás, és fürtön engedélyezve van. A rendelkezésre állási SLA díjszabását a különálló fürtök száma határozza meg, nem az egyes fürtök méretével. További információért tekintse meg a [rendelkezésre állási SLA díjszabását](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="before-you-begin"></a>Előkészületek

* Az [Azure CLI](/cli/azure/install-azure-cli) 2.8.0 vagy újabb verziójának telepítése

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Új fürt létrehozása a rendelkezésre állási SLA-val

Ha a rendelkezésre állási SLA-val rendelkező új fürtöt szeretne létrehozni, használja az Azure CLI-t.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Az [`az aks create`][az-aks-create] parancs használatával hozzon létre egy AK-fürtöt. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet végrehajtása több percet is igénybe vehet:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza. A következő JSON-kódrészlet az SKU fizetős szintjét mutatja be, amely azt jelzi, hogy a fürt engedélyezve van a rendelkezésre állási SLA-val:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Meglévő fürt módosítása a rendelkezésre állási SLA használatára

A meglévő fürtöket igény szerint frissítheti a rendelkezésre állási SLA használatára.

Ha az előző lépésekkel létrehozott egy AK-fürtöt, törölje az erőforráscsoportot:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Hozzon létre egy új erőforráscsoportot:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy új fürtöt, és ne használja a rendelkezésre állási SLA-t:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

A [`az aks update`][az-aks-update] parancs használatával frissítse a meglévő fürtöt:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 A következő JSON-kódrészlet az SKU fizetős szintjét mutatja be, amely azt jelzi, hogy a fürt engedélyezve van a rendelkezésre állási SLA-val:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>A rendelkezésre állási SLA lemondása

A fürt frissítésével az ingyenes szintre válthat, és letilthatja a rendelkezésre állási SLA-t.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A díjak elkerüléséhez távolítsa el a létrehozott erőforrásokat. A fürt törléséhez használja a [`az group delete`][az-group-delete] parancsot az AK-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Következő lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

Konfigurálja a fürtöt a [kimenő forgalom korlátozása](limit-egress-traffic.md)érdekében.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
