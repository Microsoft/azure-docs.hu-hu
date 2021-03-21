---
title: Az Azure Service Fabric gyakori forgatókönyvek diagnosztizálására
description: Ismerje meg az Azure Service Fabric-alkalmazásokon belüli általános figyelési és diagnosztikai forgatókönyvek hibaelhárítását.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3083348948ce97ae9d99098896878d79081a15f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580488"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Gyakori forgatókönyvek diagnosztizálása Service Fabric

Ez a cikk bemutatja, hogyan fordul elő a felhasználók a monitorozás és a diagnosztika területén a Service Fabric. A bemutatott forgatókönyvek a Service Fabric 3 rétegét fedik le: alkalmazás, fürt és infrastruktúra. Mindegyik megoldás Application Insights és Azure Monitor naplókat, Azure monitoring-eszközöket használ az egyes forgatókönyvek teljesítéséhez. Az egyes megoldásokban szereplő lépések bemutatják, hogyan használhatók a Application Insights és az Azure Monitor naplók a Service Fabric környezetében.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Előfeltételek és javaslatok

A cikkben szereplő megoldások a következő eszközöket fogják használni. Javasoljuk, hogy a következőket állítsa be és konfigurálja:

* [Application Insights a Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Azure Diagnostics engedélyezése a fürtön](service-fabric-diagnostics-event-aggregation-wad.md)
* [Log Analytics munkaterület beállítása](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics ügynök a teljesítményszámlálók nyomon követéséhez](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hogyan tekinthetem meg a nem kezelt kivételeket az alkalmazásban?

1. Navigáljon a Application Insights-erőforráshoz, amelyhez az alkalmazás konfigurálva van.
2. Kattintson a bal felső sarokban található *Keresés* gombra. Ezután kattintson a szűrő elemre a következő panelen.

    ![AI – áttekintés](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Számos típusú eseményt láthat (nyomkövetés, kérés, egyéni esemény). Szűrőként válassza a "kivétel" lehetőséget.

    ![AI-szűrők listája](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    A listában szereplő kivételekre kattintva további részleteket is megtudhat, beleértve a szolgáltatási környezetet, ha a Service Fabric Application Insights SDK-t használja.

    ![AI-kivétel](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hogyan megtekintheti, hogy mely HTTP-hívásokat használják a saját szolgáltatásaim?

1. Ugyanebben a Application Insights erőforrásban a kivételek helyett a "kérések" lehetőségre szűrhet, és az összes kérelem megtekintése
2. Ha a Service Fabric Application Insights SDK-t használja, láthatja a szolgáltatásainak vizuális megjelenítését egymáshoz, valamint a sikeres és sikertelen kérések számát. A bal oldalon kattintson az "alkalmazás-hozzárendelés" elemre.

    ![AI app Map panel ](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![ AI-alkalmazás térképe](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Az alkalmazás-hozzárendeléssel kapcsolatos további információkért tekintse meg az [alkalmazás-Térkép dokumentációját](../azure-monitor/app/app-map.md) .

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hogyan riasztás létrehozása, ha egy csomópont leáll

1. A csomóponti eseményeket a Service Fabric-fürt nyomon követi. Navigáljon a Service Fabric Analytics ServiceFabric nevű megoldási erőforráshoz **(NameofResourceGroup).**
2. Kattintson az "összefoglalás" nevű panel alján található gráfra.

    ![Azure Monitor naplók megoldás](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Itt számos különböző mérőszámot megjelenítő gráf és csempe található. Kattintson az egyik gráfra, és a naplóbeli keresésre kerül. Itt lekérdezheti a fürt eseményeit vagy a teljesítményszámlálókat.
4. Adja meg a következő lekérdezést. Ezek az események azonosítói a [csomópont eseményeinek hivatkozásában](service-fabric-diagnostics-event-generation-operational.md#application-events) találhatók.

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Kattintson a felül az "új riasztási szabály" lehetőségre, és mostantól bármikor megérkezik egy esemény a lekérdezés alapján. a rendszer riasztást küld a választott kommunikációs módszerről.

    ![Azure Monitor új riasztást naplóz](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hogyan lehet riasztást kapni az alkalmazások frissítésének visszaállításáról?

1. Ugyanazon a naplóbeli keresés ablakban, mint a frissítés visszagörgetéséhez a következő lekérdezés megadása előtt. Ezek az eseményazonosító az [alkalmazás eseményeinek hivatkozása](service-fabric-diagnostics-event-generation-operational.md#application-events) alatt találhatók.

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Kattintson a felül az "új riasztási szabály" elemre, és most bármikor megérkezik egy esemény a lekérdezés alapján, riasztást fog kapni.

## <a name="how-do-i-see-container-metrics"></a>Hogyan lásd a tároló metrikáit?

Az összes gráftal azonos nézetben látni fogja a tárolók teljesítményéhez tartozó csempéket. A csempék feltöltéséhez szükség van a Log Analytics ügynökre és a [tároló-figyelési megoldásra](service-fabric-diagnostics-oms-containers.md) .

![Log Analytics tároló Metrikái](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Ahhoz, **hogy a telemetria** a tárolóból, hozzá kell adnia a [Application Insights nuget csomagot a](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)tárolók számára.

## <a name="how-can-i-monitor-performance-counters"></a>Hogyan figyelhetők a teljesítményszámlálók?

1. Miután hozzáadta a Log Analytics ügynököt a fürthöz, hozzá kell adnia a nyomon követni kívánt teljesítményszámlálókat. Navigáljon a Log Analytics munkaterület oldalára a portálon – a megoldás oldaláról a munkaterület lap bal oldali menüjében.

    ![Log Analytics munkaterület lap](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Miután megtörtént a munkaterület lapján, kattintson a "speciális beállítások" elemre a bal oldali menüben.

    ![Log Analytics speciális beállítások](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kattintson az adatok > Windows-teljesítményszámlálók (a Linux-alapú gépekhez tartozó adatok > Linux rendszerű számítógépek esetében) lehetőségre a csomópontok Log Analytics ügynökkel való összegyűjtésének megkezdéséhez. Példa a hozzáadandó számlálók formátumára

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     A rövid útmutatóban a VotingData és a VotingWeb a használt folyamat neve, így a számlálók nyomon követése a következőképpen néz ki:

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Teljesítményszámlálók Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Így megtekintheti, hogyan kezeli az infrastruktúra a számítási feladatokat, és hogyan állíthatja be a kapcsolódó riasztásokat az erőforrások kihasználtsága alapján. Előfordulhat például, hogy riasztást szeretne beállítani, ha a processzor teljes kihasználtsága 90% fölé vagy 5% alá esik. Az ehhez használt számláló neve: "% Processor Time". Ezt úgy teheti meg, hogy létrehoz egy riasztási szabályt a következő lekérdezéshez:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hogyan a Reliable Services és a szereplők teljesítményének nyomon követését?

Az alkalmazások Reliable Services vagy szereplőinek teljesítményének nyomon követéséhez a Service Fabric Actor, a Actors Method, a Service és a Service Method számlálókat is össze kell gyűjtenie. Íme néhány példa a megbízható szolgáltatásokra és a színészi teljesítményszámlálók gyűjtésére

>[!NOTE]
>Service Fabric teljesítményszámlálók jelenleg nem gyűjthetők össze az Log Analytics ügynökkel, de [más diagnosztikai megoldások](service-fabric-diagnostics-partners.md) is összegyűjthetők.

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

A megbízható [szolgáltatásokkal](service-fabric-reliable-serviceremoting-diagnostics.md) és [szereplőkkel](service-fabric-reliable-actors-diagnostics.md) kapcsolatos teljesítményszámlálók teljes listájáért olvassa el a következő hivatkozásokat:

## <a name="next-steps"></a>Következő lépések

* [Gyakori programkód-aktiválási hibák megkeresése](./service-fabric-diagnostics-code-package-errors.md)
* [Riasztások beállítása az AI-ben](../azure-monitor/alerts/alerts-log.md) a teljesítmény vagy a használat változásairól való értesítéshez
* Az [intelligens észlelés a Application Insights](../azure-monitor/app/proactive-diagnostics.md) az AI által küldött telemetria proaktív elemzését hajtja végre, hogy figyelmeztesse Önt a lehetséges teljesítménnyel kapcsolatos problémákra
* További információ Azure Monitor naplók [riasztásáról](../azure-monitor/alerts/alerts-overview.md) az észlelés és a diagnosztika támogatásához.
* Helyszíni fürtök esetén a Azure Monitor-naplók egy átjárót (HTTP-továbbítási proxyt) biztosítanak, amellyel az adatküldés Azure Monitor naplókba. További információ arról, hogy az [log Analytics átjáró használatával internet-hozzáférés nélküli számítógépek csatlakoztatása Azure monitor naplókhoz](../azure-monitor/agents/gateway.md)
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/logs/log-query-overview.md) funkciókkal
* Részletesebb áttekintést kaphat Azure Monitor naplókról és arról, hogy mit kínál, olvassa el a [Mi az Azure monitor-naplók?](../azure-monitor/overview.md)
