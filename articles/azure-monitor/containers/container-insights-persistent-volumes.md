---
title: PV-figyelés konfigurálása a Azure Monitor for containers szolgáltatáshoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtöket állandó kötetekkel a Azure Monitor for containers használatával.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621178"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>PV-figyelés konfigurálása a Azure Monitor for containers szolgáltatáshoz

Az ügynök verziójának *ciprod10052020* kezdődően az Azure monitor for containers Integrated Agent mostantól támogatja a monitor PV (állandó kötet) használatát.

## <a name="pv-metrics"></a>PV mérőszámok

A tárolók Azure Monitor automatikusan elindítja az 60sec-intervallumok figyelését a következő metrikák összegyűjtésével és a **InsightMetrics** táblában való tárolásával.

|Metrika neve |Metrikus dimenzió (címkék) |Description |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Az adott Pod által használt jogcímet tartalmazó adott állandó kötethez felhasznált lemezterület bájtban megadva. `pvCapacityBytes` egy dimenzióként van behajtva a címkék mezőben az adatfeldolgozási díjak csökkentése és a lekérdezések egyszerűsítése érdekében.|

## <a name="monitor-persistent-volumes"></a>Állandó kötetek figyelése

A tárolók Azure Monitor tartalmaz előre konfigurált diagramokat ehhez a metrikához az összes fürthöz tartozó munkafüzetben. A diagramokat a **munkaterhelések részleteit** tartalmazó munkafüzet állandó kötet lapján találja közvetlenül egy AK-fürtről, ha a bal oldali ablaktáblán a munkafüzetek elemre kattint, és az elemzés **munkafüzetek megtekintése** legördülő listájában található. Emellett engedélyezheti az ajánlott riasztást a PV-használathoz, valamint lekérdezheti a metrikákat a Log Analytics.  

![Példa Azure Monitor PV munkaterhelés-munkafüzetre](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Következő lépések

- További információ az összegyűjtött PV-mérőszámokról [.](./container-insights-agent-config.md)
