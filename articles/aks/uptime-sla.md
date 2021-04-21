---
title: Azure Kubernetes Service (AKS) üzemidőre sla-val
description: Ismerje meg az opcionális üzemidőre vonatkozó SLA-ajánlatot a Azure Kubernetes Service (AKS) API-kiszolgálóhoz.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779560"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service (AKS) üzemidejű SLA

Az üzemidőre vonatkozó SLA nem kötelező funkció, amely lehetővé teszi a pénzügyi támogatású, magasabb SLA-t a fürtök számára. Az üzemidőre vonatkozó SLA a Kubernetes API kiszolgálóvégpontjának 99,95%-os rendelkezésre állását garantálja az olyan fürtök számára, amelyek [Availability Zones-t][availability-zones] és a rendelkezésre állás 99,9%-át használják a nem Availability Zones. Az AKS frissítési és tartalék tartományok főcsomópont-replikáit használja az SLA követelményeinek való megfelelés biztosítása érdekében.

Az ügyfeleknek, akik SLA-val teljesítik a megfelelőségi követelményeket, vagy sla-t kell kiterjeszteniük a végfelhasználókra, engedélyezniük kell ezt a funkciót. Azok az ügyfelek, akik kritikus számítási feladatokat futtatnak, magasabb üzemidőre való SLA-t fognak kihozni, szintén előnyösek lehetnek. Az üzemidőRE vonatkozó SLA szolgáltatás Availability Zones a Kubernetes API-kiszolgáló üzemideje számára magasabb rendelkezésre állást tesz lehetővé.  

Az ügyfelek így is létrehozhatnak 99,5%-os szolgáltatásiszint-célkitűzéssel (SLO) korlátlan számú ingyenes fürtöt, és szükség szerint választhatják az előnyben részesített SLO- vagy SLA-üzemidőt.

> [!Important]
> A ki- és visszacsatolással zárolt fürtök esetén lásd: A bejövő [forgalom](limit-egress-traffic.md) korlátozása a megfelelő portok megnyitásához.

## <a name="region-availability"></a>Régiónkénti elérhetőség

* Az üzemidőre szolgáltatásiszint-szerződés nyilvános régiókban és Azure Government, ahol az [AKS támogatott.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Az üzemidőre vonatkozó SLA minden nyilvános régióban elérhető a privát [AKS-fürtökhöz,][private-clusters] ahol az AKS támogatott.

## <a name="sla-terms-and-conditions"></a>SLA-feltételek és -feltételek

Az üzemidőre való SLA egy fizetős szolgáltatás, amely fürtönként engedélyezett. Az üzemidőRE vonatkozó SLA díjszabását a különálló fürtök száma határozza meg, nem az egyes fürtök mérete. További információért tekintse meg [az üzemidőRE vonatkozó SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) díjszabását.

## <a name="before-you-begin"></a>Előkészületek

* Az [Azure CLI](/cli/azure/install-azure-cli) 2.8.0-s vagy újabb verziójának telepítése

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Új fürt létrehozása üzemidőre sla-val

Az Üzemidő SLA-val létrehozott új fürt létrehozásához használja az Azure CLI-t.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Az [`az aks create`][az-aks-create] paranccsal hozzon létre egy AKS-fürtöt. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet több percig is eltarthat:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Néhány perc múlva befejeződik a parancs, és visszaadja a fürttel kapcsolatos JSON-formátumú információkat. Az alábbi JSON-kódrészlet a termékváltozat fizetős rétegét mutatja be, jelezve, hogy a fürtön engedélyezve van az üzemidőRE vonatkozó SLA:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Meglévő fürt módosítása üzemidőre vonatkozó SLA használatára

Igény szerint frissítheti a meglévő fürtökön az Üzemidő SLA-t.

Ha az előző lépésekkel létrehozott egy AKS-fürtöt, törölje az erőforráscsoportot:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Hozzon létre egy új erőforráscsoportot:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy új fürtöt, és ne használja az Üzemidő SLA-t:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

A [`az aks update`][az-aks-update] paranccsal frissítse a meglévő fürtöt:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Az alábbi JSON-kódrészlet a termékváltozat fizetős rétegét mutatja be, jelezve, hogy a fürtön engedélyezve van az üzemidőRE vonatkozó SLA:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Az üzemidőre és az üzemidőre (SLA) való lemondás

Frissítheti a fürtöt úgy, hogy az ingyenes szintre váltsa, és levüntetheti az üzemidő sla-ját.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A díjak elkerülése érdekében az összes létrehozott erőforrást el kell takarítani. A fürt törléséhez használja az parancsot az [`az group delete`][az-group-delete] AKS-erőforráscsoport törléséhez:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Következő lépések

A [Availability Zones][availability-zones] növelheti a magas rendelkezésre állást az AKS-fürt számítási feladataival.

Konfigurálja a fürtöt [a bejövő forgalom korlátozására.](limit-egress-traffic.md)

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
