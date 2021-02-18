---
title: Függőségek nyomon követése az Azure Application Insightsban | Microsoft Docs
description: A helyszíni vagy Microsoft Azure webalkalmazástól származó függőségi hívások figyelése Application Insightsokkal.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 05b6c29b121cbf42cf0ebe12b2879e50735db7ea
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652003"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Függőségek nyomon követése az Azure Application Insights 

A *függőség* az alkalmazás által meghívott összetevő. Általában egy HTTP-vel hívott szolgáltatások, adatbázisok vagy fájlrendszerek. [Application Insights](./app-insights-overview.md) méri a függőségi hívások időtartamát, függetlenül attól, hogy a meghibásodása vagy sem, valamint további információk, például a függőség neve és így tovább. Megvizsgálhat bizonyos függőségi hívásokat, és összekapcsolhatja őket a kérelmekkel és a kivételekkel.

## <a name="automatically-tracked-dependencies"></a>Automatikusan követett függőségek

Application Insights SDK-kat .NET-és .NET Core-hajókhoz `DependencyTrackingTelemetryModule` , amely a telemetria modul, amely automatikusan gyűjti a függőségeket. Ez a függőségi gyűjtemény automatikusan engedélyezve van a [ASP.net](./asp-net.md) és a [ASP.net Core](./asp-net-core.md) alkalmazásokhoz, ha a társított hivatalos dokumentumokhoz van konfigurálva. `DependencyTrackingTelemetryModule` [ezt](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) a NuGet-csomagként szállítja, és automatikusan a NuGet-csomagok vagy a használatával `Microsoft.ApplicationInsights.Web` történik `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` a jelenleg automatikusan nyomon követi a következő függőségeket:

|Függőségek |Részletek|
|---------------|-------|
|HTTP/HTTPS | Helyi vagy távoli HTTP/HTTPS-hívások |
|WCF-hívások| Csak automatikusan nyomon követhető, ha HTTP-alapú kötések vannak használatban.|
|SQL | A-vel végzett hívások `SqlClient` . [Tekintse meg az SQL](#advanced-sql-tracking-to-get-full-sql-query) -lekérdezés rögzítését ismertető témakört.  |
|[Azure Storage (blob, tábla, üzenetsor)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Az Azure Storage-ügyféllel kezdeményezett hívások. |
|[EventHub ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | A 1.1.0 vagy újabb verzió. |
|[ServiceBus ügyfél-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3.0.0 vagy újabb verzió. |
|Azure Cosmos DB | Csak a HTTP/HTTPS használata esetén automatikusan nyomon követhető. A Application Insights nem rögzíti a TCP-módot. |

Ha hiányzik egy függőség, vagy egy másik SDK-t használ, győződjön meg róla, hogy az [automatikusan összegyűjtött függőségek](./auto-collect-dependencies.md)listáján szerepel. Ha a függőség nincs automatikusan begyűjtve, nyomon követheti manuálisan a [függőségi hívással](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Automatikus függőség-követés beállítása a konzolos alkalmazásokban

A .NET-konzol alkalmazásaitól származó függőségek automatikus nyomon követéséhez telepítse a NuGet csomagot `Microsoft.ApplicationInsights.DependencyCollector` , és inicializálja a `DependencyTrackingTelemetryModule` következőt:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

A .NET Core Console-alkalmazások TelemetryConfiguration. Active elavult. A [Worker Service dokumentációjában](./worker-service.md) és a [ASP.net Core monitoring dokumentációjában](./asp-net-core.md) talál útmutatást

### <a name="how-automatic-dependency-monitoring-works"></a>Hogyan működik az automatikus függőség-figyelés?

A függőségeket a rendszer a következő módszerek egyikének használatával automatikusan gyűjti:

* Byte Code Instrumentation használata a Select metódusok köré. (InstrumentationEngine a StatusMonitor vagy az Azure Web App Extension-ből)
* EventSource visszahívások
* DiagnosticSource-visszahívások (a legújabb .NET/.NET Core SDK-k)

## <a name="manually-tracking-dependencies"></a>Függőségek manuális követése

Az alábbiakban néhány példát láthat a függőségekről, amelyeket a rendszer nem gyűjt automatikusan, és így manuális követést igényel.

* A Azure Cosmos DB automatikusan nyomon követhető, ha a rendszer [HTTP/HTTPS protokollt](../../cosmos-db/performance-tips.md#networking) használ. A Application Insights nem rögzíti a TCP-módot.
* Redis

Az SDK által automatikusan összegyűjtött függőségek esetében manuálisan nyomon követheti azokat a [TRACKDEPENDENCY API](api-custom-events-metrics.md#trackdependency) -val, amelyet a standard automatikus gyűjtemény moduljai használnak.

Ha például a kódot egy olyan szerelvény alapján hozza létre, amelyet nem írt magával, akkor az összes hívást megtudhatja, hogy megtudja, milyen mértékben járul hozzá a válaszadási időpontokhoz. Ha meg szeretné jeleníteni ezeket az adataikat a Application Insights függőségi diagramokban, küldje el a következővel: `TrackDependency` .

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Azt is megteheti, `TelemetryClient` hogy bővítményi metódusokat biztosít, `StartOperation` `StopOperation` amelyek használatával manuálisan követheti a függőségeket, ahogy az [itt](custom-operations-tracking.md#outgoing-dependencies-tracking) látható.

Ha ki szeretné kapcsolni a szabványos függőség-követési modult, távolítsa el a ASP.NET-alkalmazások DependencyTrackingTelemetryModule mutató hivatkozást a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) alkalmazásban. ASP.NET Core alkalmazásokhoz kövesse az alábbi [utasításokat.](asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="tracking-ajax-calls-from-web-pages"></a>AJAX-hívások követése weblapokról

Weblapok esetében Application Insights JavaScript SDK automatikusan függőségként gyűjti az AJAX-hívásokat.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Részletes SQL-követés a teljes SQL-lekérdezés beszerzéséhez

SQL-hívások esetén a rendszer a kiszolgáló és az adatbázis nevét gyűjti össze és tárolja az összegyűjtött név szerint `DependencyTelemetry` . Van egy "adat" nevű további mező, amely a teljes SQL-lekérdezési szöveget tartalmazhatja.

ASP.NET Core alkalmazások esetében most meg kell adnia az SQL-szöveg gyűjtését a következő használatával:
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

A ASP.NET-alkalmazások esetében a teljes SQL-lekérdezési szöveget a rendszer a System. adat. SqlClient könyvtár helyett a [Microsoft. adat. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet-csomag használatával gyűjti. Az SQL-lekérdezések teljes gyűjtésének engedélyezéséhez a platformra vonatkozó konkrét lépéseket lásd alább:

| Platform | A teljes SQL-lekérdezés beolvasásához szükséges lépés (ek) |
| --- | --- |
| Azure-webalkalmazás |A webalkalmazás-Vezérlőpulton [nyissa meg a Application Insights](../../azure-monitor/app/azure-web-apps.md) panelt, és engedélyezze az SQL-parancsokat a .net alatt. |
| IIS-kiszolgáló (Azure-beli virtuális gép, helyszíni stb.) | Használja a [Microsoft. Information. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet-csomagot, vagy használja a Állapotmonitor PowerShell-modult [a Instrumentation-motor telepítéséhez](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) , és indítsa újra az IIS-t. |
| Azure-felhőszolgáltatás | [Indítási feladat hozzáadása a StatusMonitor telepítéséhez](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Az alkalmazást a NuGet-csomagok [ASP.net](./asp-net.md) vagy [ASP.net Core alkalmazások](./asp-net-core.md) számára történő telepítésével kell előkészíteni a ApplicationInsights SDK-ra. |
| IIS Express | Használja a [Microsoft. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet-csomagot.
| Azure web Jobs-feladatok | Használja a [Microsoft. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet-csomagot.

A fenti platform-specifikus lépések mellett **explicit módon be kell jelentkeznie az SQL-parancsok gyűjtésének engedélyezéséhez** a applicationInsights.config fájl módosításával a következőkkel:

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

A fenti esetekben a rendszerállapot-kezelő motor megfelelő ellenőrzésének helyes módszere az, hogy ellenőrzi, hogy a gyűjtött SDK-verzió `DependencyTelemetry` "rddp". a "rdddsd" vagy a "rddf" érték azt jelzi, hogy a függőségek gyűjtése DiagnosticSource vagy EventSource visszahívásokon keresztül történik, így a teljes SQL-lekérdezés nem lesz rögzítve.

## <a name="where-to-find-dependency-data"></a>A függőségi adatkeresés helye

* Az [alkalmazás-Térkép](app-map.md) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségeket.
* A [tranzakciós diagnosztika](transaction-diagnostics.md) az egyesített, korrelált kiszolgáló adatait jeleníti meg.
* A [böngészők lapon](javascript.md) Ajax-hívásokat láthat a felhasználói böngészőkből.
* A függőségi hívások vizsgálatához kattintson a lassú vagy sikertelen kérelmekre.
* Az [elemzési szolgáltatás](#logs-analytics) a függőségi adatlekérdezéshez használható.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Lassú kérelmek diagnosztizálása

Minden kérési esemény társítva van a függőségi hívások, a kivételek és az alkalmazás által a kérelem feldolgozásakor követett egyéb eseményekhez. Ha azonban bizonyos kérések helytelenek, megtudhatja, hogy a függőség miatt lassú válasz-e.

### <a name="tracing-from-requests-to-dependencies"></a>Nyomkövetés a kérelmektől a függőségek felé

Nyissa meg a **teljesítmény** lapot, és navigáljon a műveletek lap tetején található **függőségek** lapra.

Kattintson a **függőség nevére** a teljes területen. Miután kiválasztotta a függőség eloszlásának diagramját, a jobb oldalon jelennek meg.

![A teljesítmény lapon kattintson a felső függőség fülre, majd a diagramon a függőség neve elemre.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kattintson a jobb alsó sarokban található kék **minták** gombra, majd egy mintán a végpontok közötti tranzakció részleteinek megjelenítéséhez.

![Kattintson a mintára a végpontok közötti tranzakció részleteinek megtekintéséhez](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Az élő webhely profilja

Nincs ötlete, hogy mikor megy? A [Application Insights PROFILER](../../azure-monitor/app/profiler.md) http-hívásokat végez az élő webhelyhez, és megjeleníti a kód azon funkcióit, amelyek a leghosszabb időt vettek igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek

A sikertelen kérések a függőségekhez tartozó sikertelen hívásokkal is társíthatók.

Lépjen a **hibák** lapra a bal oldalon, majd kattintson a felül található **függőségek** fülre.

![Kattintson a sikertelen kérelmek diagramra](./media/asp-net-dependencies/4-fail.png)

Itt láthatja a sikertelen függőségek darabszámát. Ha további részleteket szeretne megtudni egy sikertelen előfordulásról, az alsó táblázatban található függőségi névre kattintva próbálkozhat. Kattintson a jobb alsó sarokban található kék **függőségek** gombra a végpontok közötti tranzakció részleteinek beszerzéséhez.

## <a name="logs-analytics"></a>Naplók (Analitika)

A függőségeket a [Kusto lekérdezési nyelvén](/azure/kusto/query/)követheti nyomon. Íme néhány példa.

* A sikertelen függőségi hívások keresése:

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX-hívások keresése:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* A kérelmekhez társított függőségi hívások keresése:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Az oldal nézeteihez kapcsolódó AJAX-hívások keresése:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hogyan nem sikerült az automatikus függőségi gyűjtő jelentése a függőségek meghívására?*

* Sikertelen függőségi hívások esetén a "sikeres" mező értéke false (hamis) lesz. `DependencyTrackingTelemetryModule` nem jelent jelentést `ExceptionTelemetry` . A függőség teljes adatmodelljéről [itt](data-model-dependency-telemetry.md)olvashat.

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Hogyan kiszámítja a betöltési késést a függőségi telemetria?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Hogyan meghatározza a függőségi hívás elindításának időpontját?*

A Log Analytics lekérdezés nézetben `timestamp` a TrackDependency () hívás indításának pillanatát jelenti, amely közvetlenül a függőségi hívás válaszának kézhezvétele után következik be. A függőségi hívás elkezdésének időpontjának kiszámításához el kell végeznie a `timestamp` függőségi hívás rögzítését, és ki kell vonnia azt `duration` .

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK
Mint minden Application Insights SDK, a függőség-gyűjtési modul is nyílt forráskódú. Olvassa el és járuljon hozzá a kóddal, vagy jelentse [a hibákat a hivatalos GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server)-tárházban.

## <a name="next-steps"></a>Következő lépések

* [Kivételek](./asp-net-exceptions.md)
* [Felhasználói & lap adatvédelme](./javascript.md)
* [Rendelkezésre állás](./monitor-web-app-availability.md)

