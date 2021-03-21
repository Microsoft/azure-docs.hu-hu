---
title: Azure Kubernetes Service- (AKS-) fürt méretezése
description: Megtudhatja, hogyan méretezheti a csomópontok számát egy Azure Kubernetes-szolgáltatásbeli (ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173766"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Csomópontszám skálázása egy Azure Kubernetes Service- (AKS-) fürtben

Ha az alkalmazások erőforrás-szükséglete megváltozik, manuálisan méretezheti egy AK-fürtöt úgy, hogy más számú csomópontot futtasson. A skálázás során a rendszer gondosan [kiüríti és kiüríti][kubernetes-drain] a csomópontokat az alkalmazások futtatásának minimalizálásához. Ha vertikális felskálázást végez, az AK megvárja, amíg a Kubernetes-fürt meg nem jelöli a csomópontokat `Ready` a hüvelyek ütemezése előtt.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először kérje le a Node-készlet *nevét* az az az [AK show][az-aks-show] paranccsal. A következő példa beolvassa a *myAKSCluster* nevű fürt Node-készletének nevét a *myResourceGroup* erőforráscsoporthoz:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A következő példa kimenete azt mutatja, hogy a *név* *nodepool1*:

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

A fürtcsomópontok méretezéséhez használja az az [AK Scale][az-aks-scale] parancsot. Az alábbi példa egy *myAKSCluster* nevű fürtöt egyetlen csomópontra méretezi. Adja meg a sajátját `--nodepool-name` az előző parancsból, például a *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A következő példa kimenete azt mutatja, hogy a fürt egy csomópontra való skálázása sikeresen megtörtént, ahogy az a *agentPoolProfiles* szakaszban is látható:

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


## <a name="scale-user-node-pools-to-0"></a>`User`Csomópont-készletek méretezése 0-ra

A csomópont `System` -készletektől eltérően, amelyekhez mindig futó csomópontok szükségesek, a `User` csomópont-készletek lehetővé teszik a méretezést 0-ra. A rendszer és a felhasználói csomópontok készletei közötti különbségekről további információt a [rendszer-és felhasználói csomópontok készletei](use-system-pools.md)című témakörben talál.

Ha a felhasználói készletet 0-ra szeretné méretezni, használhatja az az [AK nodepool skálázást][az-aks-nodepool-scale] a fenti `az aks scale` parancs alternatívájaként, és állítsa a 0 értéket a csomópontok számának megadásához.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

A `User` csomópont-készleteket 0 csomópontra is kibővítheti, ha a `--min-count` [fürt automéretező](cluster-autoscaler.md) paraméterét 0-ra állítja.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben manuálisan méretezhető egy AK-fürtöt a csomópontok számának növeléséhez vagy csökkentéséhez. A fürt automatikus [méretezésével][cluster-autoscaler] is automatikusan méretezheti a fürtöt.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale