---
title: Azure Service Fabric Event Analysis Azure Monitor-naplókkal
description: Ismerje meg, hogyan jelenítheti meg és elemezheti az eseményeket Azure Monitor naplók használatával az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 678e96c9b5611cd6d72abd7c4582691b55ffe479
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581552"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Események elemzése és vizualizáció Azure Monitor naplókkal
 Azure Monitor naplók gyűjti és elemzi a felhőben üzemeltetett alkalmazások és szolgáltatások telemetria, és elemzési eszközöket biztosít a rendelkezésre állás és a teljesítmény maximalizálása érdekében. Ez a cikk azt ismerteti, hogyan futtathat lekérdezéseket Azure Monitor-naplókban, hogy betekintést nyerjen, és hárítsa el, mi történik a fürtben. A következő gyakori kérdéseket tárgyaljuk:

* Hogyan az állapottal kapcsolatos eseményeket?
* Hogyan tudni, hogy egy csomópont leáll-e?
* Hogyan tudni, hogy az alkalmazás szolgáltatásai elindultak vagy leálltak?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>A Log Analytics munkaterület áttekintése

>[!NOTE] 
>Habár a diagnosztikai tár alapértelmezés szerint engedélyezve van a fürt létrehozási idején, a Log Analytics munkaterületet is be kell állítania a diagnosztikai tárolóból való olvasáshoz.

Azure Monitor a naplók adatokat gyűjtenek a felügyelt erőforrásokból, beleértve az Azure Storage-táblát vagy az ügynököt, és egy központi tárházban tárolják azokat. Az adatelemzéshez, riasztáshoz és vizualizációhoz, illetve további exportáláshoz is használhatók. Azure Monitor naplók támogatják az eseményeket, a teljesítményadatokat vagy bármely más egyéni adat használatát. Tekintse meg [a diagnosztikai bővítmény konfigurálásához szükséges lépéseket, hogy összegyűjtse az eseményeket](service-fabric-diagnostics-event-aggregation-wad.md) és [lépéseket a log Analytics munkaterület létrehozásához a Storage eseményeiből való olvasáshoz](service-fabric-diagnostics-oms-setup.md) , hogy az adatok Azure monitor naplókba legyenek átáramlva.

Azure Monitor naplókból származó adatok fogadása után az Azure számos olyan *figyelési megoldással* rendelkezik, amelyek előre csomagolt megoldásokkal vagy operatív irányítópultokkal figyelik a bejövő adatok figyelését, a különböző forgatókönyvek szerint testre szabva. Ezek közé tartozik a *Service Fabric Analytics* megoldás és a *tárolók* megoldása, amely a diagnosztika és a figyelés két legfontosabb tényezője Service Fabric fürtök használata esetén. Ez a cikk a munkaterülettel létrehozott Service Fabric Analytics megoldás használatát ismerteti.

## <a name="access-the-service-fabric-analytics-solution"></a>A Service Fabric Analytics-megoldás elérése

Az [Azure Portalon](https://portal.azure.com)nyissa meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást.

Válassza ki az erőforrás- **ServiceFabric \<nameOfOMSWorkspace\>**.

A-ben a `Summary` csempék egy gráf formájában jelennek meg az összes engedélyezett megoldáshoz, beleértve az egyiket a Service Fabric. A Service Fabric Analytics megoldás folytatásához kattintson a **Service Fabric** gráfra.

![Service Fabric megoldás](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Az alábbi képen a Service Fabric Analytics-megoldás kezdőlapja látható. Ez a Kezdőlap pillanatkép-nézetet jelenít meg a fürtben zajló eseményekről.

![A Service Fabric Analytics-megoldás kezdőlapját megjelenítő képernyőkép.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Ha a fürt létrehozásakor engedélyezte a diagnosztika szolgáltatást, a következő eseményeket tekintheti meg: 

* [Service Fabric-fürt eseményei](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programozási modell eseményei](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A Service Fabric események mellett a [diagnosztikai bővítmény konfigurációjának frissítésével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)részletesebb rendszereseményeket is gyűjthet.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric események megtekintése, beleértve a csomópontokon végrehajtott műveleteket is

A Service Fabric Analytics lapon kattintson a diagramra **Service Fabric eseményekhez**.

![Service Fabric megoldás működési csatornája](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

A **lista elemre kattintva** megtekintheti a listában szereplő eseményeket. Ha itt látható, az összes összegyűjtött rendszeresemény megjelenik. Ezek az Azure Storage-fiók **WADServiceFabricSystemEventsTable** származnak, és hasonlóképpen a megbízható szolgáltatások és Actors-események, amelyek a következőket látják, a megfelelő táblákból származnak.
    
![Működési csatorna lekérdezése](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Azt is megteheti, hogy a bal oldali nagyítóra kattint, és a Kusto lekérdezési nyelv segítségével megkeresi a keresett helyet. A fürt csomópontjain végrehajtott összes művelet megkereséséhez például a következő lekérdezést használhatja. Az alább használt eseményazonosító az [operatív csatorna eseményeinek referenciájában](service-fabric-diagnostics-event-generation-operational.md)található.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Több mezőből is lekérdezhető, például a rendszerszolgáltatás (feladatnév) adott csomópontjaira.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Megbízható szolgáltatás-és Actor-események megtekintése Service Fabric

A Service Fabric Analytics lapon kattintson a **Reliable Services** gráfra.

![Service Fabric megoldás Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

A **lista elemre kattintva** megtekintheti a listában szereplő eseményeket. Itt láthatja a megbízható szolgáltatások eseményeit. A szolgáltatás runasync indításakor és befejezésekor különböző eseményeket tekinthet meg, amelyek általában az üzembe helyezéseken és a frissítéseken történnek. 

![Lekérdezés Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

A megbízható színészi eseményeket hasonló módon lehet megtekinteni. A megbízható szereplők részletes eseményeinek konfigurálásához módosítania kell a `scheduledTransferKeywordFilter` konfigurációt a diagnosztikai bővítmény konfigurációjában (lásd alább). Ezeknek az értékeknek a részletei a [megbízható szereplőkkel kapcsolatos események hivatkozásában](service-fabric-reliable-actors-diagnostics.md#keywords)találhatók.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

A Kusto lekérdezési nyelve nagy teljesítményű. Egy másik értékes lekérdezés futtatásával megtudhatja, hogy mely csomópontok generálják a legtöbb eseményt. Az alábbi képernyőképen látható lekérdezés Service Fabric az adott szolgáltatással és csomóponttal összesített működési eseményeket mutatja.

![Események lekérdezése/csomópont](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Következő lépések

* Az infrastruktúra figyelésének engedélyezéséhez, azaz a teljesítményszámlálók létrehozásához lépjen [a log Analytics-ügynök hozzáadásához](service-fabric-diagnostics-oms-agent.md). Az ügynök gyűjti a teljesítményszámlálókat, és hozzáadja őket a meglévő munkaterülethez.
* Helyszíni fürtök esetén a Azure Monitor-naplók egy átjárót (HTTP-továbbítási proxyt) biztosítanak, amellyel az adatküldés Azure Monitor naplókba. További információ arról, hogy a [számítógépek internetkapcsolat nélküli csatlakoztatása Azure monitor naplókhoz a log Analytics átjáró használatával](../azure-monitor/agents/gateway.md).
* [Automatikus riasztások](../azure-monitor/alerts/alerts-overview.md) konfigurálása az észlelés és a diagnosztika támogatásához.
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/logs/log-query-overview.md) funkciókkal.
* Részletes áttekintést kaphat Azure Monitor naplókról, valamint arról, hogy mit kínál, olvassa el a [Mi az Azure monitor naplók?](../azure-monitor/overview.md)című témakört.
