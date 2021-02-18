---
title: Üzembe helyezés & HPA-metrikák Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy milyen üzembe helyezési & HPA (horizontális Pod autoscaleer) mérőszámok gyűjtése Azure Monitor for containers.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620088"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Üzembe helyezés & HPA-metrikák Azure Monitor tárolók számára

Az ügynök verziójának *ciprod08072020* kezdődően az Azure monitor for containers – az integrált ügynök mostantól az üzembe helyezések metrikáit gyűjti & HPAs.

## <a name="deployment-metrics"></a>Üzembe helyezési metrikák

A tárolók Azure Monitor automatikusan elindítja az üzembe helyezések figyelését, a következő metrikák 60 másodperces időközönkénti gyűjtésével és a **InsightMetrics** táblában való tárolásával:

|Metrika neve |Metrikus dimenzió (címkék) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, üzembe helyezés, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (Status. updatedReplicas) | A központi telepítés által megcélozható kész hüvelyek teljes száma (Status. readyReplicas). Az alábbiakban a metrika dimenziói láthatók. <ul> <li> központi telepítés – a központi telepítés neve </li> <li> k8sNamespace – Kubernetes névtér az üzemelő példányhoz </li> <li> deploymentStrategy – üzembe helyezési stratégia a hüvelyek újakkal való lecseréléséhez (spec. Strategy. Type)</li><li> creationTime – központi telepítés időbélyege </li> <li> spec_replicas – a kívánt hüvelyek (spec. replikák) száma </li> <li>status_replicas_available – az üzemelő példány által megcélozható rendelkezésre álló hüvelyek teljes száma (legalább minReadySeconds) (Status. availableReplicas)</li><li>status_replicas_updated – az üzemelő példány által megadott, nem lezárt hüvelyek teljes száma, amelyeken a kívánt sablon spec (Status. updatedReplicas) szerepel </li></ul>|

## <a name="hpa-metrics"></a>HPA-metrikák

A tárolók Azure Monitor automatikusan elindítja a figyelési HPAs, a következő metrikák 60 mp-es intervallumban történő gyűjtésével és a **InsightMetrics** táblában való tárolásával:

|Metrika neve |Metrikus dimenzió (címkék) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | Az ebben az autoskálázásban (Status. currentReplicas) kezelt hüvelyek replikáinak aktuális száma. Az alábbiakban a metrika dimenziói láthatók. <ul> <li> hPa – a HPA neve </li> <li> k8sNamespace – Kubernetes-névtér a HPA számára </li> <li> lastScaleTime – utolsó alkalommal, amikor a HPA a hüvelyek számát (Status. lastScaleTime) méretezi</li><li> creationTime – HPA létrehozás időbélyege </li> <li> spec_max_replicas – az autoskálázás által beállítható hüvelyek számának felső korlátja (spec. maxReplicas) </li> <li> spec_min_replicas – az automéretező által lekicsinyíthető replikák számának alsó határértéke (spec. minReplicas) </li><li>status_desired_replicas az ezen az autoskálázással kezelt hüvelyek replikáinak kívánt száma (Status. desiredReplicas)</li><li>targetKind – a HPA céljának típusa (spec. scaleTargetRef. Kind) </li><li>targetName – a HPA céljának neve (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Üzembe helyezés & HPA-diagramok 

A tárolók Azure Monitor tartalmaz előre konfigurált diagramokat a táblázatban korábban felsorolt mérőszámokhoz az összes fürthöz tartozó munkafüzetként. A központi telepítések & HPA-munkafüzetek üzembe helyezését **& hPa** -t közvetlenül egy AK-fürtről, a bal oldali ablaktáblán található **munkafüzetek** kiválasztásával, valamint az elemzés **munkafüzetek megtekintése** legördülő listából.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [az Kube mérőszámait a Kubernetes-ben](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) , és ismerkedjen meg a Kube mérőszámokkal.