---
title: Alkalmazás (AKS) Azure Kubernetes Service és leállítása
description: Megtudhatja, hogyan állíthat le vagy indítható Azure Kubernetes Service (AKS-) fürt.
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 2d3c946bc2f98b0c06fe33dcaaa77a5399f6d56b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782728"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS-) fürt leállítása és elindítani

Előfordulhat, hogy az AKS számítási feladatainak nem kell folyamatosan futniuk, például egy csak munkaidőben használt fejlesztési fürtnek. Ez ahhoz vezet, hogy Azure Kubernetes Service AKS-fürt tétlen lehet, és nem fut tovább, mint a rendszerösszetevők. Csökkentheti a fürt erőforrásigényét, ha az összes csomópontkészletet [ `System` ](use-system-pools.md) [ `User` 0-ra](scale-cluster.md#scale-user-node-pools-to-0)skálázja, de a készletre továbbra is szükség van a rendszerösszetevők futtatásához, amíg a fürt fut. A költségek ezen időszakokban való további optimalizálásához teljesen kikapcsolhatja (leállíthatja) a fürtöt. Ez a művelet teljesen leállítja a vezérlősíkot és az ügynökcsomópontokat, így pénzt takaríthat meg az összes számítási költségen, miközben az összes objektumot és fürtállapotot az újra elindítani képes állapotban tartja. Ezután a hétvégét követően közvetlenül a bal oldalon használhatja, vagy futtathatja a fürtöt csak a kötegelt feladatok futtatása közben.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

### <a name="limitations"></a>Korlátozások

A fürt indítási/leállítási szolgáltatásának használata esetén a következő korlátozások érvényesek:

- Ez a funkció csak a Virtual Machine Scale Sets fürtökön támogatott.
- A leállított AKS-fürt fürtállapota akár 12 hónapig is megmarad. Ha a fürt több mint 12 hónapig le van állítva, a fürt állapota nem állítható helyre. További információ: [AKS támogatási szabályzatok.](support-policies.md)
- Csak leállított AKS-fürtöt lehet elindítani vagy törölni. Ha bármilyen műveletet , például skáláz vagy frissít, először indítsa el a fürtöt.

## <a name="stop-an-aks-cluster"></a>AKS-fürt leállítása

Az paranccsal leállíthatja egy futó AKS-fürt csomópontját és `az aks stop` vezérlősíkját. Az alábbi példa leállít egy *myAKSCluster nevű fürtöt:*

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Az [az aks show][az-aks-show] paranccsal ellenőrizheti, hogy a fürt le van-e állítva, és ellenőrizheti, hogy az alábbi kimenetben látható `powerState` módon `Stopped` jelenik-e meg:

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

Ha az `provisioningState` ezt mutatja, az azt jelenti, hogy a fürt `Stopping` még nem állt le teljesen.

> [!IMPORTANT]
> Ha podkimaradási [költségvetéseket](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) használ, a leállítási művelet hosszabb ideig is tarthat, mivel a kiürítési folyamat több időt vehet igénybe.

## <a name="start-an-aks-cluster"></a>AKS-fürtöt indít el

A paranccsal elindíthatja egy leállított AKS-fürt csomópontját és `az aks start` vezérlősíkját. A fürt az előző vezérlősík-állapottal és az ügynökcsomópontok számával indul újra.  
Az alábbi példa elindít egy *myAKSCluster nevű fürtöt:*

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Az [az aks show][az-aks-show] paranccsal ellenőrizheti, hogy a fürt elindult-e, és az alábbi kimenetben látható módon ellenőrizheti `powerState` a `Running` kimenetet:

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

Ha az azt jelzi, hogy a fürt `provisioningState` még nem indult el `Starting` teljesen.

## <a name="next-steps"></a>Következő lépések

- A készletek 0-ra skálázható fel: Készletek méretezése `User` [ `User` 0-ra.](scale-cluster.md#scale-user-node-pools-to-0)
- Ha meg szeretne ismerkedni a kihasznál kihasznált példányokkal való költségtakarékos használattal, tekintse meg a kihasznált csomópontkészlet [AKS-hez való hozzáadását.](spot-node-pool.md)
- Az AKS támogatási szabályzatokkal kapcsolatos további információkért lásd: [AKS támogatási szabályzatok.](support-policies.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
