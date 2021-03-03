---
title: PV-figyelés konfigurálása a Container bepillantásokkal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az állandó kötetekkel rendelkező Kubernetes-fürtöket a tároló-felismerések használatával.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713728"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>PV-figyelés konfigurálása a Container bepillantást

Az ügynök verziójának *ciprod10052020* kezdődően a Container bepillantást biztosító integrált ügynök mostantól támogatja a (állandó kötet) figyelését.

## <a name="pv-metrics"></a>PV mérőszámok

A tároló-felismerések automatikusan elindítja az 60sec-intervallumok figyelését a következő metrikák összegyűjtésével és a **InsightMetrics** táblában való tárolásával.

|Metrika neve |Metrikus dimenzió (címkék) |Leírás |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Az adott Pod által használt jogcímet tartalmazó adott állandó kötethez felhasznált lemezterület bájtban megadva. `pvCapacityBytes` egy dimenzióként van behajtva a címkék mezőben az adatfeldolgozási díjak csökkentése és a lekérdezések egyszerűsítése érdekében.|

## <a name="monitor-persistent-volumes"></a>Állandó kötetek figyelése

A tároló-bepillantást a metrikához tartozó előre konfigurált diagramokat tartalmaz egy munkafüzetben minden fürthöz. A diagramokat a **munkaterhelések részleteit** tartalmazó munkafüzet állandó kötet lapján találja közvetlenül egy AK-fürtről, ha a bal oldali ablaktáblán a munkafüzetek elemre kattint, és az elemzés **munkafüzetek megtekintése** legördülő listájában található. Emellett engedélyezheti az ajánlott riasztást a PV-használathoz, valamint lekérdezheti a metrikákat a Log Analytics.  

![Példa Azure Monitor PV munkaterhelés-munkafüzetre](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Következő lépések

- További információ az összegyűjtött PV-mérőszámokról [.](./container-insights-agent-config.md)
