---
title: A GPU-figyelés konfigurálása a Container bepillantást tartalmazó szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtöket az NVIDIA és az AMD GPU-t használó csomópontok számára a tárolók bepillantást nyerhetve.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731867"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>A GPU-figyelés konfigurálása a Container bepillantást

Az ügynök verziójának *ciprod03022019* kezdődően a Container bepillantások integrált ügynöke mostantól támogatja a GPU-kompatibilis Kubernetes-fürt csomópontjain a monitoring GPU-t (grafikus feldolgozási egységek), valamint a GPU-erőforrásokat kérő és használó hüvelyek/tárolók figyelését.

## <a name="supported-gpu-vendors"></a>Támogatott GPU-szállítók

A Container bepillantást a következő GPU-szállítóktól származó GPU-fürtöket támogatja:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

A tároló-felismerések automatikusan elindítja a GPU-használat figyelését a csomópontokon, valamint a hüvelyeket és a munkaterheléseket kérő GPU-t a következő metrikák 60sec-intervallumokban való összegyűjtésével és a **InsightMetrics** táblában való tárolásával.

>[!NOTE]
>A fürt GPU-csomópontokkal való üzembe helyezését követően győződjön meg arról, hogy a GPU- [illesztő](../../aks/gpu-cluster.md) a GPU-munkaterhelések futtatásához szükséges az AK-ban. A tároló-információk GPU-metrikákat gyűjtenek a csomóponton futó GPU-illesztőn keresztül. 

|Metrika neve |Metrikus dimenzió (címkék) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Az elmúlt mintavételi időszakban (60 másodpercben) az idő százalékos aránya, amely alatt a GPU foglalt/aktívan feldolgozás alatt áll a tárolóban. A Duty ciklus 1 és 100 közötti szám. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Minden tároló megadhatja a határértékeket egy vagy több GPU-ként. A GPU töredékét nem lehet lekérni vagy korlátozni. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Az egyes tárolók egy vagy több GPU-t is igényelhetnek. A GPU töredékét nem lehet lekérni vagy korlátozni.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tároló számára elérhető, bájtban használható GPU-memória mennyisége. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Egy adott tároló által használt GPU memória mennyisége bájtban megadva. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Azon GPU-k száma egy csomóponton, amelyeket a Kubernetes használhat. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Egy csomóponton található GPU-k száma összesen |

## <a name="gpu-performance-charts"></a>GPU-teljesítményű diagramok 

A Container-alapú adatellenőrzések előre konfigurált diagramokat tartalmaznak a táblázatban korábban felsorolt mérőszámokhoz minden fürthöz tartozó GPU-munkafüzetként. A Container bepillantást nyerhető munkafüzetek leírását a [Container-](../insights/container-insights-reports.md) információkban található munkafüzetek című részben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

- Lásd: [a GPU használata nagy számítási igényű munkaterhelésekhez az Azure Kubernetes szolgáltatásban](../../aks/gpu-cluster.md) (ak), amelyből megtudhatja, hogyan helyezhet üzembe egy GPU-t támogató csomópontokat tartalmazó AK-fürtöt.

- További információ a [GPU-ra optimalizált VM SKU-ról Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Tekintse át a [GPU támogatását a Kubernetes-ben](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) , és ismerkedjen meg a Kubernetes kísérleti támogatásával a GPU-k egy vagy több csomóponton való kezeléséhez.
