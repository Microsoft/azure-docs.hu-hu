---
title: Azure Kubernetes Service- (AKS-) fürt méretezése
description: Megtudhatja, hogyan skálázható a csomópontok száma egy Azure Kubernetes Service (AKS-) fürtben.
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782886"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Csomópontszám skálázása egy Azure Kubernetes Service- (AKS-) fürtben

Ha az alkalmazások erőforrás-igényei megváltoznak, manuálisan skálázhat egy AKS-fürtöt különböző számú csomópont futtatásához. A leskálás során [][kubernetes-drain] a csomópontok gondosan el vannak korétálva és ki vannak ürítve, hogy minimálisra csökkenje a futó alkalmazások üzemkimaradása. A felskáláskor az AKS megvárja, amíg a kubernetes-fürt meg nem jelöli a csomópontokat, mielőtt a `Ready` podok ütemezve vannak.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először szerezze *be a* csomópontkészlet nevét az az [aks show paranccsal.][az-aks-show] Az alábbi példa lekérte a *myResourceGroup* erőforráscsoportban található *myAKSCluster* nevű fürt csomópontkészletének nevét:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Az alábbi példakimeneten látható, hogy *a név* *nodepool1:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

A [fürtcsomópontok méretezéséhez][az-aks-scale] használja az az aks scale parancsot. Az alábbi példa egy *myAKSCluster* nevű fürtöt skáláz egyetlen csomópontra. Adja meg a `--nodepool-name` sajátját az előző parancsból, például *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Az alábbi példakimeneten látható, hogy a fürt sikeresen skálázva lett egy csomópontra, ahogyan az *agentPoolProfiles* szakaszban látható:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>`User`Csomópontkészletek méretezése 0-ra

A mindig futó csomópontokat igénylő csomópontkészletekkel ellentétben a csomópontkészletek lehetővé teszik `System` `User` a 0-ra való méretezést. A rendszer- és felhasználói csomópontkészletek közötti különbségekről további információt a Rendszer- és felhasználói csomópontkészletek oldalon [olvashat.](use-system-pools.md)

Ha 0-ra skáláz egy felhasználói készletet, használhatja az [az aks nodepool scale][az-aks-nodepool-scale] parancsot a fenti parancs alternatívájaként, és `az aks scale` beállíthatja a 0-t csomópontszámként.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

A csomópontkészleteket 0 csomópontra is automatikusan skálázhatja, ha az automatikus fürtméretozó paraméterét `User` `--min-count` 0-ra stb. [](cluster-autoscaler.md)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben manuálisan skálázta az AKS-fürtöt a csomópontok számának növelése vagy csökkentése érdekében. Az automatikus [fürtméretozóval automatikusan][cluster-autoscaler] is skálázhatja a fürtöt.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale