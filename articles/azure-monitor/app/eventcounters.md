---
title: Az Application Insightsban lévő események számlálói | Microsoft Docs
description: A rendszer és az egyéni .NET/.NET Core EventCounters figyelése Application Insightsban.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579527"
---
# <a name="eventcounters-introduction"></a>EventCounters bemutatása

[`EventCounter`](/dotnet/core/diagnostics/event-counters) a .NET/.NET Core mechanizmusa számlálók vagy statisztikák közzétételére és felhasználására szolgál. A EventCounters minden operációsrendszer-platformon támogatott – Windows, Linux és macOS. Azt is megteheti, hogy a [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) platformfüggetlen, csak Windows rendszerekben támogatott.

Míg a felhasználók bármilyen egyéni személyt közzétehetnek az `EventCounters` igényeik kielégítése érdekében, a .net Core 3,0 és a magasabb szintű futtatókörnyezet alapértelmezés szerint közzéteszi a számlálók készletét. Ez a dokumentum végigvezeti az Azure Application Insightsban való összegyűjtéséhez és megtekintéséhez szükséges lépéseken `EventCounters` (rendszer által definiált vagy felhasználó által definiált).

## <a name="using-application-insights-to-collect-eventcounters"></a>A Application Insights használata a EventCounters gyűjtéséhez

Application Insights támogatja a begyűjtést a `EventCounters` alkalmazással `EventCounterCollectionModule` , amely a [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)újonnan kiadott NuGet-csomag részét képezi. `EventCounterCollectionModule` automatikusan engedélyezve van a [AspNetCore](asp-net-core.md) vagy a [WorkerService](worker-service.md)használatakor. `EventCounterCollectionModule` a nem konfigurálható adatgyűjtési gyakorisággal (60 másodperc) gyűjti a számlálókat. A EventCounters gyűjtéséhez nincs szükség különleges engedélyekre.

## <a name="default-counters-collected"></a>Összegyűjtött alapértelmezett számlálók

A [ASPNETCORE SDK](asp-net-core.md) vagy a [WorkerService SDK](worker-service.md)2.15.0 verziójától kezdve a rendszer alapértelmezés szerint nem gyűjt számlálókat. A modul maga is engedélyezve van, így a felhasználók egyszerűen hozzáadhatják a kívánt számlálókat a gyűjtéshez.

A .NET-futtatókörnyezet által közzétett ismert számlálók listájának lekéréséhez tekintse meg a [rendelkezésre álló számlálók](/dotnet/core/diagnostics/event-counters#available-counters) dokumentumát.

## <a name="customizing-counters-to-be-collected"></a>A gyűjteni kívánt számlálók testreszabása

Az alábbi példa bemutatja, hogyan adhat hozzá vagy távolíthat el számlálókat. Ezt a testreszabást az `ConfigureServices` alkalmazás metódusa fogja elvégezni, miután Application Insights telemetria-gyűjtemény engedélyezve lett a `AddApplicationInsightsTelemetry()` vagy a használatával `AddApplicationInsightsWorkerService()` . A következő példa egy ASP.NET Core alkalmazásból származó kódot mutat be. Más típusú alkalmazások esetében tekintse meg [ezt](worker-service.md#configuring-or-removing-default-telemetrymodules) a dokumentumot.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>A EventCounter-gyűjtési modul letiltása

`EventCounterCollectionModule` a használatával letiltható `ApplicationInsightsServiceOptions` . Alább látható egy példa ASP.NET Core SDK használatára.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

A WorkerService SDK-hoz hasonló módszer is használható, de a névteret az alábbi példában látható módon kell módosítani.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Az események számlálói a metrika Explorerben

Az EventCounter mérőszámok megjelenítéséhez a [metrika-kezelőben](../essentials/metrics-charts.md)válassza ki Application Insights erőforrást, majd a log-alapú metrikákat metrikai névtérként. Ezután az EventCounter mérőszámok az egyéni kategória alatt jelennek meg.

> [!div class="mx-imgBorder"]
> ![Application Insights metrika-kezelőben jelentett események számlálói](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Az elemzési események számlálói

Az **customMetrics** táblában is kereshet és megjeleníthet Event Counter-jelentéseket az [Analyticsben](../logs/log-query-overview.md).

Például a következő lekérdezés futtatásával tekintheti meg, hogy a rendszer milyen számlálókat gyűjt, és hogyan érhető el a lekérdezéshez:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analyticsben jelentett események számlálói](./media/event-counters/analytics-event-counters.png)

Egy adott számláló diagramjának lekéréséhez (például: `ThreadPool Completed Work Item Count` ) a legutóbbi időszak alatt futtassa a következő lekérdezést.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Csevegés egyetlen számlálóról Application Insights](./media/event-counters/analytics-completeditems-counters.png)

A többi telemetria hasonlóan a **customMetrics** is tartalmaz egy oszlopot, `cloud_RoleInstance` amely jelzi annak a gazdagép-példánynak az identitását, amelyen az alkalmazás fut. A fenti lekérdezés a számláló értékét jeleníti meg, és felhasználható a különböző kiszolgálói példányok teljesítményének összehasonlítására.

## <a name="alerts"></a>Riasztások
Más mérőszámokhoz hasonlóan [riasztást is beállíthat](../alerts/alerts-log.md) , amely figyelmezteti, ha egy esemény számlálója a megadott korláton kívül esik. Nyissa meg a riasztások ablaktáblát, és kattintson a riasztás hozzáadása lehetőségre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Láthatom a EventCounters az élő Mérőszámokban?

Az élő metrikák nem jelenítik meg a EventCounters a mai naptól. A telemetria megtekintéséhez használja a metrika Explorert vagy az elemzést.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Engedélyeztem Application Insights az Azure Web App Portalon. De nem látom a EventCounters.?

 ASP.NET Core [Application Insights bővítmény](./azure-web-apps.md) még nem támogatja ezt a funkciót. Ez a dokumentum akkor frissül, ha ez a funkció támogatott.

## <a name="next-steps"></a><a name="next"></a>Következő lépések

* [Függőségek nyomon követése](./asp-net-dependencies.md)

