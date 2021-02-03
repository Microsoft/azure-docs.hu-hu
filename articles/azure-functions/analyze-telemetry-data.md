---
title: Azure Functions telemetria elemzése Application Insights
description: Megtudhatja, hogyan tekintheti meg és kérdezheti le Azure Functions telemetria az Azure Application Insights által gyűjtött és tárolt adatokat.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2a991157962b0588e3d49510e8a82a9abcfb9aed
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493770"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Azure Functions telemetria elemzése Application Insights 

A Azure Functions együttműködik a Application Insightsekkel, hogy jobban megfigyelje a Function apps-alkalmazásokat. Application Insights gyűjti a telemetria által generált adatokat, beleértve az alkalmazás által a naplókba írt adatokat is. A Application Insights integráció általában engedélyezve van a Function alkalmazás létrehozásakor. Ha a Function alkalmazás nem rendelkezik a kialakítási kulccsal, először [engedélyeznie kell Application Insights integrációt](configure-monitoring.md#enable-application-insights-integration). 

Alapértelmezés szerint a függvény alkalmazásból gyűjtött adatokat Application Insights tárolja a rendszer. A [Azure Portal](https://portal.azure.com)Application Insights a telemetria-adatvizualizációk széles körét biztosítja. A hibák naplózása és a lekérdezési események és mérőszámok részletezése is megtehető. Ez a cikk az összegyűjtött adatok megtekintésének és lekérdezésének alapvető példáit mutatja be. Ha többet szeretne megtudni a Function alkalmazás adatainak Application Insights való megismeréséről, tekintse meg a [Mi az a Application Insights?](../azure-monitor/app/app-insights-overview.md)című témakört. 

Az adatmegőrzéssel és a lehetséges tárolási költségekkel kapcsolatos további információkért lásd: adatgyűjtés [, megőrzés és tárolás Application Insightsban](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>A telemetria megtekintése a figyelés lapon

Ha [engedélyezve van Application Insights integráció](configure-monitoring.md#enable-application-insights-integration), megtekintheti a telemetria a **figyelés** lapon.

1. A Function app (függvény alkalmazása) lapon válasszon ki egy olyan függvényt, amely legalább egyszer fut Application Insights konfigurálása után. Ezután válassza a **figyelő** lehetőséget a bal oldali ablaktáblán. Válassza a rendszeres **frissítés** lehetőséget, amíg meg nem jelenik a függvény meghívások listája.

   ![Meghívások listája](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Akár öt percet is igénybe vehet, amíg a lista megjelenik, miközben a telemetria-ügyfél a kiszolgálónak továbbított adatforgalmat. A késleltetés nem vonatkozik a [élő metrikastreamra](../azure-monitor/app/live-stream.md). A szolgáltatás a lap betöltésekor csatlakozik a functions-gazdagéphez, így a naplók közvetlenül az oldalra lesznek továbbítva.

1. Egy adott függvény naplóinak megtekintéséhez válassza ki az adott hívás **dátum (UTC)** oszlopának hivatkozását. Az adott hívás naplózási kimenete új lapon jelenik meg.

   ![Meghívás részletei](media/functions-monitoring/invocation-details-ai.png)

1. A Azure Monitor lekérdezés forrásának megtekintéséhez válassza a **Futtatás a Application Insightsban** lehetőséget. Ha első alkalommal használja az Azure-Log Analytics az előfizetésében, a rendszer kéri, hogy engedélyezze.

1. A Log Analytics engedélyezése után a következő lekérdezés jelenik meg. Láthatja, hogy a lekérdezés eredményei az elmúlt 30 napra korlátozódnak ( `where timestamp > ago(30d)` ), és az eredmények legfeljebb 20 sort mutatnak ( `take 20` ). Ezzel szemben a függvény Meghívási részleteinek listája az elmúlt 30 napra, korlátozás nélkül.

   ![Application Insights Analytics-meghívások listája](media/functions-monitoring/ai-analytics-invocation-list.png)

További információ: telemetria- [adatok lekérdezése](#query-telemetry-data) a cikk későbbi részében.

## <a name="view-telemetry-in-application-insights"></a>Telemetria megtekintése Application Insights

Application Insights megnyitása a [Azure Portalban](https://portal.azure.com)lévő Function alkalmazásban:

1. Tallózással keresse meg a Function alkalmazást a portálon.

1. A bal oldalon található **Beállítások** területen válassza a **Application Insights** lehetőséget. 

1. Ha első alkalommal használja Application Insights az előfizetését, a rendszer felszólítja, hogy engedélyezze. Ehhez jelölje be a **Application Insights bekapcsolása** lehetőséget, majd válassza az **alkalmaz** lehetőséget a következő oldalon.

![Application Insights megnyitása a Function app – áttekintés oldalon](media/functions-monitoring/ai-link.png)

A Application Insights használatáról az [Application Insights dokumentációjában](/azure/application-insights/)olvashat bővebben. Ez a szakasz néhány példát mutat be a Application Insights lévő adatmegjelenítésre. Ha már ismeri a Application Insightst, közvetlenül [a telemetria-adatainak konfigurálásával és testreszabásával foglalkozó fejezetekre](configure-monitoring.md#configure-log-levels)léphet.

![Application Insights Áttekintés lap](media/functions-monitoring/metrics-explorer.png)

Az Application Insights következő területei hasznosak lehetnek a függvények viselkedésének, teljesítményének és hibáinak kiértékelése során:

| Vizsgálat | Leírás |
| ---- | ----------- |
| **[Hibák](../azure-monitor/app/asp-net-exceptions.md)** |  Diagramok és riasztások létrehozása a függvények hibái és a kiszolgálói kivételek alapján. A **művelet** neve a függvény neve. A függőségek meghibásodása csak akkor jelenik meg, ha egyéni telemetria valósít meg a függőségekhez. |
| **[Teljesítmény](../azure-monitor/app/performance-counters.md)** | A teljesítménnyel kapcsolatos problémák elemzéséhez tekintse meg az erőforrás-kihasználtságot és az átviteli sebességet a **felhőalapú szerepkör példányain**. Ez a teljesítményadatok hasznosak lehetnek olyan forgatókönyvek hibakereséséhez, amelyekben a függvények lekérik az alapul szolgáló erőforrásokat. |
| **[Mérőszámok](../azure-monitor/platform/metrics-charts.md)** | Metrikák alapján létrehozhat diagramokat és riasztásokat. A metrikák közé tartozik a Function meghívások száma, a végrehajtási idő és a sikerességi arány. |
| **[Élő metrikák](../azure-monitor/app/live-stream.md)** | A metrikák adatait a közel valós időben létrehozva tekintheti meg. |

## <a name="query-telemetry-data"></a>Telemetria-adatbázis lekérdezése

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) hozzáférést biztosít minden telemetria-adatbázishoz egy adatbázisban lévő táblák formájában. Az elemzés lekérdezési nyelvet biztosít az adatok kinyeréséhez, módosításához és megjelenítéséhez. 

Válassza a **naplók** lehetőséget a naplózott események felderítéséhez vagy lekérdezéséhez.

![Elemzési példa](media/functions-monitoring/analytics-traces.png)

Az alábbi példa egy lekérdezési példát mutat be, amely az elmúlt 30 percben megjeleníti az egyes feldolgozó kérelmek eloszlását.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

A rendelkezésre álló táblák a bal oldali **séma** lapon jelennek meg. Az alábbi táblázatokban megtalálhatja a függvények által generált adathívásokat:

| Táblázat | Leírás |
| ----- | ----------- |
| **nyomok** | A futásidejű, a skálázási vezérlő és a nyomkövetési napló által létrehozott naplók a függvény kódjából. |
| **kérések** | Egy kérelem az egyes függvények meghívásához. |
| **kivételek** | A futtatókörnyezet által kiváltott kivételek. |
| **customMetrics** | A sikeres és sikertelen meghívások száma, a sikerességi arány és az időtartam. |
| **customEvents** | A futtatókörnyezet által követett események, például: a függvényt kiváltó HTTP-kérelmek. |
| **performanceCounters** | Információk azon kiszolgálók teljesítményéről, amelyeken a függvények futnak. |

A többi tábla a rendelkezésre állási tesztekhez, valamint az ügyfél és a böngésző telemetria. Egyéni telemetria is létrehozhat, amelyekkel adathozzáadást adhat hozzájuk.

A függvények egyes adatai egy adott `customDimensions` mezőben találhatók.  A következő lekérdezés például lekéri az összes naplózási szintű nyomkövetést `Error` .

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

A futtatókörnyezet biztosítja a `customDimensions.LogLevel` és a `customDimensions.Category` mezőket. További mezőket is megadhat a függvény kódjába írt naplókban. A C# nyelvben például lásd: [strukturált naplózás](functions-dotnet-class-library.md#structured-logging) a .net-osztály könyvtárának fejlesztői útmutatójában.

## <a name="query-scale-controller-logs"></a>A méretezési vezérlő naplófájljainak lekérdezése

_Ez a funkció előzetes verzióban érhető el._

A [méretezési vezérlő naplózásának](configure-monitoring.md#configure-scale-controller-logs) és a [Application Insights integrációjának](configure-monitoring.md#enable-application-insights-integration)engedélyezése után a Application Insights naplóbeli kereséssel kérdezheti le a kibocsátott skálázási vezérlő naplóit. A skálázási vezérlő naplóit a `traces` gyűjtemény a **ScaleControllerLogs** kategóriában tárolja.

A következő lekérdezéssel megkeresheti az aktuális Function alkalmazáshoz tartozó összes méretezési vezérlő naplóját a megadott időtartamon belül:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

A következő lekérdezés kibontja az előző lekérdezést, hogy bemutassa, hogyan lehet csak a skála változását jelző naplókat beolvasni:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>Használati terv – specifikus mérőszámok

Ha egy használati [tervben](consumption-plan.md)fut, az egyfunkciós végrehajtás végrehajtási *költségeit* *GB-másodpercben* mérjük. A végrehajtási költségeket a memóriahasználat és a végrehajtási idő kombinálásával kell kiszámítani. További információ: a [felhasználási terv költségeinek becslése](functions-consumption-costs.md).

A következő telemetria-lekérdezések olyan mérőszámokra vonatkoznak, amelyek befolyásolják a használati tervben szereplő függvények futtatásának költségeit.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions figyeléséről:

+ [Az Azure Functions monitorozása](functions-monitoring.md)
+ [A Azure Functions figyelésének konfigurálása](configure-monitoring.md)

