---
title: Azure Kubernetes szolgáltatás indítása és leállítása (ak)
description: Ismerje meg, hogyan állíthat le vagy indíthat el egy Azure Kubernetes-szolgáltatási (ak-) fürtöt.
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201002"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (ak) fürt leállítása és elindítása

Előfordulhat, hogy az AK-beli számítási feladatait nem kell folyamatosan futtatni, például egy fejlesztési fürtöt, amely csak munkaidőn belül használatos. Ez olyan időpontokat eredményez, amikor az Azure Kubernetes-szolgáltatás (ak) fürtje tétlen lehet, és nem fut tovább a rendszerösszetevőknél. Csökkentheti a fürt lábnyomát úgy, hogy az [összes `User` csomópont-készletet 0-ra](scale-cluster.md#scale-user-node-pools-to-0)méretezi, de a [ `System` készlet](use-system-pools.md) továbbra is szükséges a rendszerösszetevők futtatásához a fürt futása közben. A költségek ezen időszakok alatti optimalizálása érdekében teljes mértékben kikapcsolhatja (leállíthatja) a fürtöt. Ez a művelet teljes mértékben leállítja a vezérlő-és az ügynök-csomópontokat, így az összes számítási költséget mentheti, miközben az összes objektumot és a fürt állapotát az újraindításkor tárolja. Ezután felveheti a jogot, hogy egy hétvége után maradjon, vagy hogy a fürt csak a Batch-feladatok futtatásakor fusson.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

### <a name="limitations"></a>Korlátozások

A fürt indítási/leállítási funkciójának használatakor a következő korlátozások érvényesek:

- Ez a funkció csak Virtual Machine Scale Sets-alapú fürtök esetén támogatott.
- A leállított AK-fürtök fürtjének állapota akár 12 hónapig is megmarad. Ha a fürt 12 hónapnál hosszabb ideig leáll, a fürt állapota nem állítható helyre. További információ: [AK-támogatási szabályzatok](support-policies.md).
- Csak leállított AK-fürtöket lehet elindítani vagy törölni. Az olyan műveletek elvégzéséhez, mint a skálázás vagy a frissítés, először indítsa el a fürtöt.

## <a name="stop-an-aks-cluster"></a>AK-fürt leállítása

A `az aks stop` parancs segítségével leállíthatja egy futó AK-fürt csomópontjait és a vezérlési síkot. A következő példa egy *myAKSCluster* nevű fürtöt állít le:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

A fürt leállításához használja az az [AK show][az-aks-show] parancsot, és erősítse `powerState` meg az `Stopped` alábbi kimenetet:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Ha az `provisioningState` azt `Stopping` jelenti, hogy a fürt még nem lett teljesen leállítva.

> [!IMPORTANT]
> Ha [Pod-megszakadási költségvetést](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) használ, a leállítási művelet hosszabb időt vehet igénybe, mert a kiürítési folyamat több időt vesz igénybe.

## <a name="start-an-aks-cluster"></a>AK-fürt indítása

A `az aks start` paranccsal elindíthatja egy leállított AK-fürt csomópontjait és a vezérlési síkot. A fürt újra lett indítva az előző vezérlő síkja állapottal és az ügynök csomópontjainak számával.  
A következő példa egy *myAKSCluster* nevű fürtöt indít el:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Ellenőrizze, hogy a fürt elindult-e az az [AK show][az-aks-show] paranccsal, és erősítse `powerState` meg az `Running` alábbi kimenetet:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Ha az `provisioningState` azt `Starting` jelenti, hogy a fürt még nem lett teljesen elindítva.

## <a name="next-steps"></a>Következő lépések

- A `User` készletek 0-ra méretezésének megismeréséhez lásd [: `User` készletek méretezése 0-ra](scale-cluster.md#scale-user-node-pools-to-0).
- Ha szeretné megtudni, hogyan mentheti a költségeket a helyszíni példányok használatával, tekintse meg [a helyszíni csomópont-készlet hozzáadása az AK-hoz](spot-node-pool.md)lehetőséget.
- Ha többet szeretne megtudni az AK-támogatási szabályzatokról, tekintse meg az [AK támogatási szabályzatait](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
