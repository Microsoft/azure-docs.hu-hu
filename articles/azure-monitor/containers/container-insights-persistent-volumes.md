---
title: PV-figyelés konfigurálása a Container bepillantásokkal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az állandó kötetekkel rendelkező Kubernetes-fürtöket a tároló-felismerések használatával.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 578cfe128b7445f8b09771999d1e653e92c4befa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200699"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>PV-figyelés konfigurálása a Container bepillantást

Az ügynök verziójának *ciprod10052020* kezdődően az Azure monitor for containers Integrated Agent mostantól támogatja a monitor PV (állandó kötet) használatát. Az ügynök verziójának *ciprod01112021* az ügynök támogatja a PV-leltár figyelését, beleértve az állapotra, a tárolási osztályra, a típusra, a hozzáférési módokra és egyéb részletekre vonatkozó információkat.
## <a name="pv-metrics"></a>PV mérőszámok

A tároló-felismerések automatikusan elindítják az PV-használat monitorozását a következő metrikák 60-s és a **InsightMetrics** táblában való tárolásával.

| Metrika neve | Metrikus dimenzió (címkék) | Metrika leírása | | `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, clusterName | Az adott Pod által használt jogcímet tartalmazó adott állandó kötethez felhasznált lemezterület bájtban megadva. `capacityBytes` egy dimenzióként van behajtva a címkék mezőben az adatfeldolgozási díjak csökkentése és a lekérdezések egyszerűsítése érdekében. |

További információ az összegyűjtött PV-metrikák [konfigurálásáról.](https://aka.ms/ci/pvconfig)

## <a name="pv-inventory"></a>PV-leltár

A tárolók Azure Monitor automatikusan elindítja a figyelési PVs azáltal, hogy a következő adatokat gyűjti össze a 60-s intervallumokban, és a **KubePVInventory** táblában tárolja őket.

|Adatok |Adatforrás| Adattípus| Mezők|
|-----|-----------|----------|-------|
|Állandó kötetek leltára egy Kubernetes-fürtben |Kube API |`KubePVInventory` | PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Állandó kötetek figyelése

A tárolók Azure Monitor tartalmaz előre konfigurált diagramokat a használati metrika és a leltári információk számára az összes fürthöz tartozó munkafüzet-sablonokban. Engedélyezheti a PV használatának javasolt riasztását is, és lekérdezheti a metrikákat a Log Analytics.  

### <a name="workload-details-workbook"></a>Munkaterhelés részletei munkafüzet

Az adott számítási feladatokhoz tartozó használati diagramokat a **munkaterhelési részletek** munkafüzetének állandó kötet lapján, a bal oldali ablaktáblán található munkafüzetek kiválasztásával, a **megtekintő** ablaktáblán található munkafüzetek megtekintése legördülő listából, vagy az információk áttekintése panel **jelentések (előnézet) lapján** találja.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Példa Azure Monitor PV munkaterhelés-munkafüzetre":::

### <a name="persistent-volume-details-workbook"></a>Állandó kötet részletei munkafüzet

Tekintse át az állandó kötetek leltárát az **állandó kötet részletei** munkafüzetben, közvetlenül egy AK-fürtről, ha a bal oldali ablaktáblán a munkafüzetek elemre kattint, az információk **nézet munkafüzetek** legördülő listájában, illetve a **jelentések (előzetes verzió)** lapon, a betekintési panelen.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor PV részletei munkafüzet – példa":::

### <a name="persistent-volume-usage-recommended-alert"></a>Állandó mennyiségi használat javasolt riasztása
Engedélyezheti a javasolt riasztást, hogy riasztást küldjön, ha egy Pod átlagos PV-használata meghaladja a 80%-ot. További információ a riasztásokról [itt](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts) , valamint az alapértelmezett küszöbérték felülbírálása [itt](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Következő lépések

- További információ az összegyűjtött PV-mérőszámokról [.](./container-insights-agent-config.md)
