---
title: Csomópont Azure Kubernetes Service (AKS)-csomópontok rendszerképének frissítése
description: Ismerje meg, hogyan frissítheti az AKS-fürtcsomópontok és csomópontkészletek rendszerképét.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767528"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS) csomópont rendszerképének frissítése

Az AKS támogatja a csomóponton található rendszerképek frissítését, hogy naprakész legyen az operációs rendszer és a futásidő legújabb frissítéseit. Az AKS hetente egy új rendszerképet biztosít a legújabb frissítésekkel, ezért hasznos, ha rendszeresen frissíti a csomópont lemezképeit a legújabb szolgáltatások, például a Linux vagy a Windows javításai számára. Ez a cikk bemutatja, hogyan frissítheti az AKS-fürtcsomópont-rendszerképeket, és hogyan frissítheti a csomópontkészlet-rendszerképeket a Kubernetes verziójának frissítése nélkül.

Az AKS által biztosított legújabb rendszerképekkel kapcsolatos további információkért tekintse meg az [AKS kibocsátási megjegyzéseit.](https://github.com/Azure/AKS/releases)

A fürt Kubernetes-verziójának frissítéséhez lásd: [AKS-fürt frissítése.][upgrade-cluster]

> [!NOTE]
> Az AKS-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Ellenőrizze, hogy a csomópontkészlet a legújabb csomópont-rendszerképen található-e

A következő paranccsal láthatja a csomópontkészlethez elérhető legújabb csomópont-rendszerképverziót: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

A kimenetben az alábbi példához `latestNodeImageVersion` hasonlót láthat:

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

A legújabb `nodepool1` elérhető csomópont-rendszerkép tehát `AKSUbuntu-1604-2020.10.28` a következő: . Most már összehasonlíthatja a csomópontkészlet által használt aktuális csomópont-rendszerképverzióval a következő futtatásával:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Példa a kimenetre:

```output
"AKSUbuntu-1604-2020.10.08"
```

Ebben a példában tehát frissíthet az aktuális rendszerképverzióról a `AKSUbuntu-1604-2020.10.08` legújabb `AKSUbuntu-1604-2020.10.28` verzióra. 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Az összes csomópontkészlet összes csomópontjának frissítése

A csomópont rendszerképének frissítése a következővel `az aks upgrade` történik: . A csomópont rendszerképének frissítésére használja a következő parancsot:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

A frissítés során a következő paranccsal ellenőrizze a csomópont-rendszerképek állapotát a címkék lekért és a csomópont aktuális rendszerkép-információinak `kubectl` kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, a használatával `az aks show` lekérte a frissített csomópontkészlet adatait. Az aktuális csomópont rendszerképe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Adott csomópontkészlet frissítése

A csomópontkészlet lemezképének frissítése hasonló a fürtön található lemezkép frissítéséhez.

A csomópontkészlet operációsrendszer-lemezképének Kubernetes-fürtfrissítés nélküli frissítéséhez használja az alábbi `--node-image-only` példában található kapcsolót:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

A frissítés során ellenőrizze a csomópont-rendszerképek állapotát a következő paranccsal, hogy lekérte a címkéket, és kiszűrje az aktuális `kubectl` csomópont rendszerkép-információit:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, a használatával `az aks nodepool show` lekérte a frissített csomópontkészlet adatait. Az aktuális csomópont rendszerképe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Csomópont-rendszerképek frissítése csomópont-túlcsomópont-túlcsomóponttal

A csomópont rendszerkép-frissítési folyamatának felgyorsítása érdekében testre szabható csomópont-túlcsomópont-értékkel frissítheti a csomópont-rendszerképeket. Alapértelmezés szerint az AKS egy további csomópontot használ a frissítések konfigurálhoz.

Ha növelni szeretné a frissítések sebességét, az értékkel konfigurálhatja a frissítéshez használt csomópontok számát, hogy azok `--max-surge` gyorsabban befejeződnek. A különböző beállításokra vonatkozó további `--max-surge` információkért lásd: [Csomópontok túlcsomópont-frissítésének testreszabása.][max-surge]

A következő parancs beállítja a csomópont-rendszerkép frissítésének maximális túlcsomóponti értékét:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

A frissítés során ellenőrizze a csomópont-rendszerképek állapotát a következő paranccsal, hogy lekérte a címkéket, és kiszűrje az aktuális `kubectl` csomópont rendszerkép-információit:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Az `az aks nodepool show` használatával lekérte a frissített csomópontkészlet adatait. Az aktuális csomópont rendszerképe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Következő lépések

- A legújabb [csomópont-rendszerképekkel kapcsolatos](https://github.com/Azure/AKS/releases) információkért tekintse meg az AKS kibocsátási megjegyzéseit.
- Megtudhatja, hogyan frissítheti a Kubernetes verzióját [az AKS-fürtök frissítéséhez.][upgrade-cluster]
- [Fürt- és csomópontkészlet-frissítések automatikus alkalmazása a GitHub Actions][github-schedule]
- További információ a több csomópontkészletről és a csomópontkészletek frissítésével kapcsolatban a Több csomópontkészlet [létrehozása és kezelése segítségével.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
