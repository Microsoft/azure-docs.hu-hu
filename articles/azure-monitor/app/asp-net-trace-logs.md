---
title: A .NET-nyomkövetési naplók megismerése Application Insights
description: A nyomkövetés, a NLog vagy a Log4Net által létrehozott naplók keresése.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937561"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>A .NET/.NET Core és a Python nyomkövetési naplók megismerése Application Insights

Diagnosztikai nyomkövetési naplók küldése a ASP.NET/ASP.NET Core-alkalmazáshoz a ILogger, a NLog, a log4Net vagy a System. Diagnostics. Traceből az [Azure Application Insightsba][start]. Python-alkalmazások esetén a diagnosztikai nyomkövetési naplók küldéséhez használja a AzureLogHandler a OpenCensus Pythonban Azure Monitor. Ezután megtekintheti és megkeresheti őket. Ezek a naplók az alkalmazás más naplófájljaiba vannak egyesítve, így azonosíthatók az egyes felhasználói kérésekhez társított nyomkövetési adatok, és más eseményekkel és kivételekkel kapcsolatos jelentések is összekapcsolhatók.

> [!NOTE]
> Szüksége van a log-Capture modulra? Ez egy hasznos adapter a külső gyártótól származó adatgyűjtők számára. Ha azonban még nem használja a NLog, a log4Net vagy a System. Diagnostics. Tracet, vegye figyelembe, hogy közvetlenül a [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) metódust hívja meg.
>
>
## <a name="install-logging-on-your-app"></a>A naplózás telepítése az alkalmazásban
Telepítse a kiválasztott naplózási keretrendszert a projektben, aminek app.config vagy web.config bejegyzést kell eredményeznie.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Application Insights konfigurálása naplók gyűjtéséhez
Ha még nem tette meg, [adja hozzá Application Insights a projekthez](./asp-net.md) . Megjelenik egy lehetőség, amely tartalmazza a napló gyűjtőjét.

Vagy kattintson a jobb gombbal a projektre Megoldáskezelő a **Application Insights konfigurálásához**. Válassza a **nyomkövetési gyűjtemény konfigurálása** lehetőséget.

> [!NOTE]
> Nincs Application Insights menü vagy a log Collector lehetőség? Próbálja ki a [hibaelhárítást](#troubleshooting).

## <a name="manual-installation"></a>Manuális telepítés
Ezt a módszert akkor használja, ha a projekt típusát nem támogatja a Application Insights telepítője (például egy Windows asztali projekt).

1. Ha a log4Net vagy a NLog használatát tervezi, telepítse azt a projektbe.
2. Megoldáskezelő kattintson a jobb gombbal a projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
3. Keressen rá a "Application Insights" kifejezésre.
4. Válasszon egyet az alábbi csomagok közül:

   - ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - A System. Diagnostics esetében: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System. Diagnostics banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ NuGet diagnosztikai forrásának figyelője](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ NuGet ETW-gyűjtő szalagcíme](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ NuGet eseményforrás-figyelő szalagcíme](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

A NuGet csomag telepíti a szükséges szerelvényeket, és módosítja web.config vagy app.config, ha alkalmazható.

## <a name="ilogger"></a>ILogger

Példák a Application Insights ILogger megvalósítására a konzolos alkalmazásokkal és a ASP.NET Coreekkel kapcsolatban: a [.net Core ILogger-naplók ApplicationInsightsLoggerProvider](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Diagnosztikai naplók beszúrása
Ha a System. Diagnostics. Tracet használja, egy tipikus hívás a következő:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Ha a log4net vagy az NLog-t részesíti előnyben, használja a következőket:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>EventSource-események használata
Konfigurálhatja a [System. Diagnostics. Trace. EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) eseményeket, amelyeket a rendszer a nyomkövetési Application Insights küldendő. Először telepítse a `Microsoft.ApplicationInsights.EventSourceListener` NuGet csomagot. Ezután szerkessze a `TelemetryModules` [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) fájl szakaszát.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes forrásokhoz a következő paramétereket állíthatja be:
 * A **név** megadja a gyűjteni kívánt EventSource nevét.
 * A **szint** meghatározza a gyűjteni kívánt naplózási szintet: *kritikus*, *hiba*, *tájékoztató*, *LogAlways*, *részletes* vagy *Figyelmeztetés*.
 * **Kulcsszavak** (nem kötelező) Itt adhatja meg a használni kívánt kulcsszó-kombinációk egész értékét.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource-események használata
Konfigurálhatja a [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eseményeket, amelyeket a rendszer nyomkövetési Application Insightsként fog elküldeni. Először telepítse a [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet csomagot. Ezután szerkessze a [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) fájl "TelemetryModules" szakaszát.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Minden nyomon követni kívánt DiagnosticSource vegyen fel egy bejegyzést a **Name** attribútummal, amely a DiagnosticSource nevére van beállítva.

## <a name="use-etw-events"></a>ETW-események használata
Windows esemény-nyomkövetés (ETW) eseményeket úgy is konfigurálhatja, hogy a rendszer nyomkövetésként Application Insights küldjön. Először telepítse a `Microsoft.ApplicationInsights.EtwCollector` NuGet csomagot. Ezután szerkessze a [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) fájl "TelemetryModules" szakaszát.

> [!NOTE] 
> A ETW eseményeket csak akkor lehet összegyűjteni, ha az SDK-t futtató folyamat olyan identitás alatt fut, amely a Teljesítménynapló felhasználói vagy rendszergazdáinak tagja.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Az egyes forrásokhoz a következő paramétereket állíthatja be:
 * A **ProviderName** a gyűjteni kívánt ETW-szolgáltató neve.
 * A **ProviderGuid** meghatározza a gyűjteni kívánt ETW-szolgáltató GUID azonosítóját. A használata helyett használhatja `ProviderName` .
 * A **szint** beállítja a begyűjteni kívánt naplózási szintet. Ez lehet *kritikus*, *hiba*, *tájékoztató*, *LogAlways*, *részletes* vagy *Figyelmeztetés*.
 * **Kulcsszavak** (nem kötelező) a kulcsszó-kombinációk egész értékének megadására használható.

## <a name="use-the-trace-api-directly"></a>A nyomkövetési API közvetlen használata
Közvetlenül is meghívhatja a Application Insights nyomkövetési API-t. A naplózási adapterek ezt az API-t használják.

Például:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

A TrackTrace előnye, hogy viszonylag hosszú adatmennyiséget helyezhet el az üzenetben. Például elvégezheti az adatposták küldését.

Az üzenethez súlyossági szintet is hozzáadhat. A többi telemetria hasonlóan a különböző nyomkövetési csoportok szűréséhez és kereséséhez is hozzáadhat tulajdonságértékeket. Például:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Ez lehetővé [teszi, hogy][diagnostic] egyszerűen kiszűrje az adott adatbázishoz kapcsolódó adott súlyossági szint összes üzenetét.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler a OpenCensus Pythonhoz
A Azure Monitor log Handler lehetővé teszi a Python-naplók exportálását Azure Monitorba.

Az alkalmazást a [OpenCensus PYTHON SDK](./opencensus-python.md) for Azure monitor eszközzel alakíthatja ki.

Ez a példa bemutatja, hogyan küldhet figyelmeztetési szintű naplót Azure Monitorba.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>A naplók megismerése
Futtassa az alkalmazást hibakeresési módban, vagy telepítse élőben.

Az alkalmazás áttekintés paneljén, [a Application Insights portálon][portal]válassza a [Keresés][diagnostic]lehetőséget.

Például a következőket teheti:

* A naplózási Nyomkövetések vagy a megadott tulajdonságokkal rendelkező elemek szűrése.
* Egy adott tétel részletes vizsgálata.
* Az ugyanahhoz a felhasználói kérelemhez kapcsolódó más rendszernapló-adattípusok keresése (azonos OperationId rendelkezik).
* Egy oldal konfigurációjának mentése kedvencként.

> [!NOTE]
>Ha az alkalmazás sok adatmennyiséget küld, és a ASP.NET 2.0.0-beta3 vagy újabb verziójához készült Application Insights SDK-t használja, akkor az *adaptív mintavételi* funkció működhet, és csak a telemetria egy részét küldheti el. [További tudnivalók a mintavételezésről.](./sampling.md)
>

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="how-do-i-do-this-for-java"></a>Hogyan ezt a javát?
A Java-kódok nélküli kialakításban (ajánlott) a naplókat a rendszer a [java 3,0-ügynök](./java-in-process-agent.md)használatával gyűjti be.

Ha a Java SDK-t használja, használja a [Java log-adaptereket](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nincs Application Insights lehetőség a projekt helyi menüjében
* Győződjön meg arról, hogy a fejlesztői elemzési eszközök telepítve vannak a fejlesztői gépen. A Visual Studio **Tools**  >  **Extensions és Updates** szolgáltatásban keresse meg a **fejlesztői elemzési eszközöket**. Ha nincs a **telepített** lapon, nyissa meg az **online** lapot, és telepítse.
* Lehet, hogy a fejlesztői elemzési eszközök nem támogatják a projekt típusát. [Manuális telepítés](#manual-installation)használata.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>A konfigurációs eszközben nincs naplózási adapter beállítás
* Először telepítse a naplózási keretrendszert.
* Ha System. Diagnostics. Tracet használ, győződjön meg arról, hogy [a *web.configban* van konfigurálva](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Győződjön meg arról, hogy rendelkezik a Application Insights legújabb verziójával. A Visual Studióban válassza az **eszközök**  >  **bővítmények és frissítések** menüpontot, és nyissa meg a **frissítések** lapot. Ha a **fejlesztői Analitika eszközei** vannak, válassza ki azt a frissítéshez.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>A "rendszerállapot-kulcs nem lehet üres" hibaüzenet jelenik meg
Valószínűleg telepítette a naplózási adapter NuGet-csomagját Application Insights telepítése nélkül. A Megoldáskezelő kattintson a jobb gombbal a *ApplicationInsights.config* elemre, majd válassza az **Application Insights frissítése** elemet. A rendszer felszólítja, hogy jelentkezzen be az Azure-ba, és hozzon létre egy Application Insights-erőforrást, vagy egy meglévőt újra. Ezt a problémát ki kell javítani.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Nyomon követhetem a nyomkövetéseket, de nem más eseményeket a diagnosztikai keresésben
Eltarthat egy ideig, amíg az összes esemény és kérelem át nem kerül a folyamaton.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Mennyi adat van megőrzött?
Számos tényező befolyásolja a megőrzött adatok mennyiségét. További információ: az ügyfél-esemény metrikái oldal [korlátozások](./api-custom-events-metrics.md#limits) szakasza.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nem látok néhány naplóbeli bejegyzést, amelyet vártam
Ha az alkalmazás terjedelmes mennyiségű adatokat küld, és a ASP.NET 2.0.0-beta3 vagy újabb verziójához használja a Application Insights SDK-t, akkor az adaptív mintavételi funkció működhet, és csak a telemetria egy részét küldheti el. [További tudnivalók a mintavételezésről.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Következő lépések

* [Hibák és kivételek diagnosztizálása a ASP.NET-ben][exceptions]
* [További információ a keresésről][diagnostic]
* [Rendelkezésre állási és válaszadási tesztek beállítása][availability]
* [Hibaelhárítás][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

