---
title: Azure Kubernetes szolgáltatás indítása és leállítása (ak)
description: Ismerje meg, hogyan állíthat le vagy indíthat el egy Azure Kubernetes-szolgáltatási (ak-) fürtöt.
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 94edf35cc16d4967449af15797f6ecccba60be4b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181091"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (ak) fürt leállítása és elindítása (előzetes verzió)

Előfordulhat, hogy az AK-beli számítási feladatait nem kell folyamatosan futtatni, például egy fejlesztési fürtöt, amely csak munkaidőn belül használatos. Ez olyan időpontokat eredményez, amikor az Azure Kubernetes-szolgáltatás (ak) fürtje tétlen lehet, és nem fut tovább a rendszerösszetevőknél. Csökkentheti a fürt lábnyomát úgy, hogy az [összes `User` csomópont-készletet 0-ra](scale-cluster.md#scale-user-node-pools-to-0)méretezi, de a [ `System` készlet](use-system-pools.md) továbbra is szükséges a rendszerösszetevők futtatásához a fürt futása közben. A költségek ezen időszakok alatti optimalizálása érdekében teljes mértékben kikapcsolhatja (leállíthatja) a fürtöt. Ez a művelet teljes mértékben leállítja a vezérlő-és az ügynök-csomópontokat, így az összes számítási költséget mentheti, miközben az összes objektumot és a fürt állapotát az újraindításkor tárolja. Ezután felveheti a jogot, hogy egy hétvége után maradjon, vagy hogy a fürt csak a Batch-feladatok futtatásakor fusson.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].


### <a name="limitations"></a>Korlátozások

A fürt indítási/leállítási funkciójának használatakor a következő korlátozások érvényesek:

- Ez a funkció csak Virtual Machine Scale Sets-alapú fürtök esetén támogatott.
- A leállított AK-fürtök fürtjének állapota akár 12 hónapig is megmarad. Ha a fürt 12 hónapnál hosszabb ideig leáll, a fürt állapota nem állítható helyre. További információ: [AK-támogatási szabályzatok](support-policies.md).
- Az előzetes verzió ideje alatt le kell állítania a fürt automéretezőjét (CA), mielőtt megpróbálja leállítani a fürtöt.
- Csak leállított AK-fürtöket lehet elindítani vagy törölni. Az olyan műveletek elvégzéséhez, mint a skálázás vagy a frissítés, először indítsa el a fürtöt.

### <a name="install-the-aks-preview-azure-cli"></a>Az `aks-preview` Azure CLI telepítése 

Szüksége lesz az *AK-előnézeti* Azure CLI-bővítmény 0.4.64 vagy újabb verziójára is. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>Az `StartStopPreview` előzetes verzió funkciójának regisztrálása

A fürt indítása/leállítása funkció használatához engedélyeznie kell a `StartStopPreview` szolgáltatás jelölőjét az előfizetésén.

Regisztrálja a `StartStopPreview` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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
