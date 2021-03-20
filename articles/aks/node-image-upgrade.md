---
title: Az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképeinek frissítése
description: Ismerje meg, hogyan frissítheti a lemezképeket az AK-fürtcsomópontok és-csomópontok csomópontjain.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96531479"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképének frissítése

Az AK támogatja a lemezképek frissítését egy csomóponton, hogy naprakész legyen a legújabb operációsrendszer-és futtatókörnyezet-frissítésekkel. Az AK egy új rendszerképet biztosít hetente a legújabb frissítésekkel, így hasznos lehet a csomópontok képeit rendszeresen frissíteni a legújabb funkciókhoz, beleértve a Linux vagy a Windows rendszerű javításokat is. Ez a cikk bemutatja, hogyan frissítheti az AK-fürtcsomópontok lemezképeit, és hogyan frissítheti a csomópont-készlet lemezképeit a Kubernetes verziójának frissítése nélkül.

Az AK által biztosított legújabb rendszerképekkel kapcsolatos további információkért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases).

További információ a fürt Kubernetes-verziójának frissítéséről: [AK-fürt frissítése][upgrade-cluster].

> [!NOTE]
> Az AK-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Ellenőrizze, hogy a csomópont-készlet a legújabb csomópont-lemezképen van-e

Az alábbi paranccsal megtekintheti, hogy mi a csomópont-rendszerkép a Node-készlethez elérhető legfrissebb verziója: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

A kimenetben a következő példában láthatók `latestNodeImageVersion` :

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Így `nodepool1` a legújabb csomópont-lemezkép elérhető `AKSUbuntu-1604-2020.10.28` . Most összehasonlíthatja azt a Node-készlet aktuális, a csomópont által használt verziójával, a futtatásával:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Példa:

```output
"AKSUbuntu-1604-2020.10.08"
```

Így ebben a példában az aktuális `AKSUbuntu-1604-2020.10.08` lemezkép-verzióról a legújabb verzióra frissíthet `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Az összes csomópont összes csomópontjának frissítése

A csomópont rendszerképének frissítése a-vel történik `az aks upgrade` . A csomópont rendszerképének frissítéséhez használja a következő parancsot:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, használja a következőt: `az aks show` a Node-készlet frissített adatainak beolvasása. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Adott csomópont-készlet frissítése

Egy csomópont-készlet rendszerképének frissítése hasonló a fürtön lévő rendszerkép frissítéséhez.

Ha a Kubernetes frissítése nélkül szeretné frissíteni a csomópont operációsrendszer-rendszerképét, használja a `--node-image-only` következő példában szereplő beállítást:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, használja a következőt: `az aks nodepool show` a Node-készlet frissített adatainak beolvasása. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Csomópont-lemezképek frissítése a Node túlfeszültséggel

A csomópont-rendszerkép frissítési folyamatának felgyorsításához a Node-lemezképeket testreszabható csomópont-túllépési érték használatával frissítheti. Alapértelmezés szerint az AK egy további csomópontot használ a frissítések konfigurálásához.

Ha szeretné bővíteni a frissítések sebességét, használja a értéket a `--max-surge` frissítésekhez használni kívánt csomópontok számának konfigurálásához. Ha többet szeretne megtudni a különböző beállításokkal kapcsolatos kompromisszumokról `--max-surge` , tekintse meg a csomópontok túlterhelésének [frissítését][max-surge]ismertető témakört.

A következő parancs a csomópont-rendszerkép frissítésének maximális túllépési értékét állítja be:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`A csomópont-készlet frissített adatainak beszerzéséhez használja a következőt:. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Következő lépések

- A legújabb csomópont-lemezképekkel kapcsolatos információkért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .
- Ismerje meg, hogyan frissítheti a Kubernetes-verziót [egy AK-fürt frissítésével][upgrade-cluster].
- [Fürt-és csomópont-készlet frissítéseinek automatikus alkalmazása GitHub-műveletekkel][github-schedule]
- További tudnivalók a több csomópontos készletekről, valamint a csomópont-készletek [több csomópontos készletek létrehozásával és kezelésével][use-multiple-node-pools]történő frissítéséről.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
