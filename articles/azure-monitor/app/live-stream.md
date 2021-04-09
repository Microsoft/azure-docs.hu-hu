---
title: Diagnosztizálás Élő metrikastream – Azure Application Insights
description: Valós időben figyelheti a webalkalmazást egyéni metrikákkal, és diagnosztizálhatja a hibákat, nyomokat és eseményeket élő hírcsatornával.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679466"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Élő metrikastream: figyelje & diagnosztizálása 1 másodperces késéssel

Az éles környezetben futó webalkalmazások figyelése Élő metrikastream használatával (más néven QuickPulse) a [Application Insightsból](./app-insights-overview.md). A metrikák és teljesítményszámlálók kiválasztásával valós időben figyelheti a szolgáltatást, és nem zavarja a szolgáltatását. A sikertelen kérelmek és kivételek alapján ellenőrizze a verem nyomkövetéseit. A [Profiler](./profiler.md) és a [Snapshot Debugger](./snapshot-debugger.md)együttes használata esetén a élő metrikastream egy hatékony és nem invazív diagnosztikai eszközt biztosít az élő webhelyhez.

A Élő metrikastream a következőket teheti:

* A teljesítmény és a hibák számának figyelésével ellenőrizze, hogy megjelent-e a javítás.
* Tekintse meg a teszt betöltésének hatását, és az élő problémák diagnosztizálását.
* A megtekinteni kívánt metrikák kiválasztásával és szűrésével kiválaszthatja az egyes tesztelési munkameneteket, vagy kiszűrheti az ismert problémákat.
* Kivételek beolvasása, ahogy történnek.
* Próbálja ki a szűrőket a legfontosabb KPI-k megkereséséhez.
* Figyelje meg a Windows teljesítményszámláló élő működését.
* Könnyedén azonosíthatja a problémákat okozó kiszolgálót, és az összes KPI/Live-hírcsatornát szűrheti csak erre a kiszolgálóra.

![Élő metrikák lap](./media/live-stream/live-metric.png)

Az élő metrikák jelenleg a ASP.NET, a ASP.NET Core, a Azure Functions, a Java és a Node.js alkalmazások esetében támogatottak.

## <a name="get-started"></a>Bevezetés

1. Az élő metrikák engedélyezéséhez kövesse az adott nyelvre vonatkozó irányelveket.
   * [ASP.net](./asp-net.md) – az élő metrikák alapértelmezés szerint engedélyezve vannak.
   * [ASP.net Core](./asp-net-core.md)– az élő metrikák alapértelmezés szerint engedélyezve vannak.
   * [.Net/.net Core Console/Worker](./worker-service.md)– az élő metrikák alapértelmezés szerint engedélyezve vannak.
   * [.NET-alkalmazások – engedélyezheti a kód használatát](#enable-livemetrics-using-code-for-any-net-application).
    * A [Java](./java-in-process-agent.md) -Live metrikák alapértelmezés szerint engedélyezve vannak.
   * [Node.js](./nodejs.md#live-metrics)

2. A [Azure Portal](https://portal.azure.com)nyissa meg az alkalmazás Application Insights erőforrását, majd nyissa meg élő stream.

3. Ha kényes adatokat, például a szűrőket használó ügyfelek nevét használja, [gondoskodjon a vezérlési csatorna védelméről](#secure-the-control-channel) .

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>LiveMetrics engedélyezése bármely .NET-alkalmazás kódjának használatával

Annak ellenére, hogy a LiveMetrics alapértelmezés szerint engedélyezve van a .NET-alkalmazásokra vonatkozó ajánlott utasítások bevezetéséhez, az alábbiak azt mutatják be, hogyan lehet az élő metrikákat manuálisan beállítani.

1. Telepítse a [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet-csomagot
2. Az alábbi mintakód az élő metrikák beállítását mutatja be.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Míg a fenti minta egy Console-alkalmazáshoz készült, a rendszer ugyanazt a kódot használhatja bármely .NET-alkalmazásban. Ha bármely más TelemetryModules engedélyezve van, amely automatikusan gyűjti a telemetria-t, fontos, hogy ugyanazt a konfigurációt használja az élő metrika modulhoz is.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Miben különbözik az Élő metrikastream a Metrikaböngésző és az elemzéstől?

| |Élő stream | Metrikaböngésző és elemzés |
|---|---|---|
|**Késés**|Egy másodpercen belül megjelenített adatértékek|Percek alatt összesítve|
|**Nincs megőrzés**|Az adatmegőrzési idő a diagramon marad, és a rendszer elveti|[90 napig megőrzött adat](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Igény szerinti**|Az adatok csak az élő metrikák ablaktábla megnyitásakor áramlanak. |Ha az SDK telepítve és engedélyezve van, az adatküldés történik|
|**Ingyenes**|Élő stream-adatszolgáltatásért nem számítunk fel díjat|A [díjszabás](./pricing.md) hatálya alá tartozik
|**Mintavételezés**|Minden kiválasztott metrika és számláló továbbítva van. A hibák és a verem nyomkövetési mintája. |Az események [mintavétele](./api-filtering-sampling.md) megtörténhet|
|**Vezérlési csatorna**|A szűrő vezérlő jeleit a rendszer elküldi az SDK-nak. Javasoljuk, hogy gondoskodjon a csatorna biztonságáról.|A kommunikáció egyik módja a portálnak|

## <a name="select-and-filter-your-metrics"></a>Metrikák kiválasztása és szűrése

(ASP.NET, ASP.NET Core és Azure Functions (v2) érhető el.)

Megfigyelheti az egyéni KPI-ket, ha tetszőleges szűrőket alkalmaz bármilyen Application Insights telemetria a portálon. Kattintson arra a szűrő vezérlőelemre, amely akkor jelenik meg, ha az egérmutatót valamelyik diagramon átadja. A következő diagram egy egyéni kérések számának KPI-jét ábrázolja az URL-címek és az időtartam attribútumainak szűrésével. Érvényesítse a szűrőket a stream Preview szakaszával, amely a telemetria élő hírcsatornáit jeleníti meg, amelyek megfelelnek az adott időpontban megadott feltételeknek.

![Szűrési kérelmek gyakorisága](./media/live-stream/filter-request.png)

A darabszámtól eltérő értékeket is megfigyelheti. A beállítások a stream típusától függenek, amely bármilyen Application Insights telemetria lehet: kérelmek, függőségek, kivételek, Nyomkövetések, események vagy mérőszámok. A saját [Egyéni mérőszáma](./api-custom-events-metrics.md#properties)lehet:

![Lekérdezés-szerkesztő a kérelmek díjszabása egyéni metrikával](./media/live-stream/query-builder-request.png)

Application Insights telemetria kívül a Windows-teljesítményszámlálók figyelését is megfigyelheti, ha kijelöli az stream beállításai közül, és megadja a teljesítményszámláló nevét.

Az élő metrikák összesítése két ponton történik: helyileg az egyes kiszolgálókon, majd az összes kiszolgálón. Az alapértelmezett érték a megfelelő legördülő menüben található egyéb beállítások lehetőség kiválasztásával módosítható.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Példa telemetria: egyéni élő diagnosztikai események
Alapértelmezés szerint az események élő hírcsatornája a sikertelen kérelmek és függőségi hívások, kivételek, események és Nyomkövetések mintáit jeleníti meg. Kattintson a szűrő ikonra az alkalmazott feltételek bármely időpontban történő megtekintéséhez.

![Szűrő gomb](./media/live-stream/filter.png)

Mint a metrikák esetében, tetszőleges feltételek bármelyikét megadhatja a Application Insights telemetria-típusaihoz. Ebben a példában az adott kérelmekkel kapcsolatos hibák és események vannak kiválasztva.

![Lekérdezéskészítő](./media/live-stream/query-builder.png)

> [!NOTE]
> Jelenleg a kivételt jelző üzenetekre vonatkozó feltételek esetén használja a legkülső kivételként szolgáló üzenetet. Az előző példában a belső kivételű üzenettel való jóindulatú kivétel kiszűréséhez (a "< –" elválasztó karakterrel) "az ügyfél leválasztva". "hiba történt a kérelem tartalmának olvasása" feltételt nem tartalmazó üzenet használata.

Az élő hírcsatornában található elemek részleteinek megtekintéséhez kattintson rá. A hírcsatornát szüneteltetheti, ha a **szüneteltetés** gombra kattint, vagy egyszerűen lefelé görget, vagy rákattint egy elemre. Az élő hírcsatornák a felülre görgetés után is folytatódnak, vagy a felfüggesztéskor gyűjtött elemek számlálóra kattintanak.

![A képernyőképen a minta telemetria ablak látható, és a kivétel részletei az ablak alján jelennek meg.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Szűrés kiszolgálópéldány szerint

Ha egy adott kiszolgálói szerepkör-példányt szeretne figyelni, akkor a kiszolgáló alapján szűrhet. Ha szűrni szeretné a kiszolgáló nevét, válassza a *kiszolgálók* elemet.

![Mintául szolgáló élő hibák](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>A vezérlő csatorna biztonságossá tétele

> [!NOTE]
> Jelenleg csak kód alapú figyeléssel állíthat be hitelesített csatornát, és nem tudja hitelesíteni a kiszolgálókat a kód nélküli csatolás használatával.

Az élő metrikák portálon megadott egyéni szűrők feltételeit a rendszer visszaküldi a Application Insights SDK élő metrikák összetevőjének. A szűrők potenciálisan bizalmas adatokat is tartalmazhatnak, például customerIDs. A csatornát a kialakítási kulcs mellett titkos API-kulccsal is biztonságossá teheti.

### <a name="create-an-api-key"></a>API-kulcs létrehozása

![API-kulcs > API-kulcs ](./media/live-stream/api-key.png)
 ![ létrehozási API-kulcs létrehozása lap. Válassza az "SDK-vezérlési csatorna hitelesítése" lehetőséget, majd a "kulcs létrehozása" elemet.](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>API-kulcs hozzáadása a konfigurációhoz

### <a name="aspnet"></a>ASP.NET

A applicationinsights.config fájlban adja hozzá a AuthenticationApiKey a QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

[ASP.net Core](./asp-net-core.md) alkalmazások esetében kövesse az alábbi utasításokat.

`ConfigureServices`A Startup. cs fájl módosítása a következőképpen történik:

Adja hozzá a következő névteret.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ezután módosítsa `ConfigureServices` a metódust az alábbiak szerint.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

ASP.NET Core alkalmazások konfigurálásával kapcsolatos további információkért tekintse meg a [telemetria-modulok ASP.net Core-ben való konfigurálásának](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)útmutatóját.

### <a name="workerservice"></a>WorkerService

[WorkerService](./worker-service.md) -alkalmazások esetén kövesse az alábbi utasításokat.

Adja hozzá a következő névteret.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Ezután adja hozzá a következő sort a hívás előtt `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

A WorkerService-alkalmazások konfigurálásával kapcsolatos további információkért tekintse meg a [telemetria-modulok konfigurálása a WorkerServices-ben című](./worker-service.md#configuring-or-removing-default-telemetrymodules)témakörben található útmutatást.

### <a name="azure-function-apps"></a>Azure-függvényalkalmazások

Az Azure Function apps (v2) esetében a csatorna API-kulccsal való biztonságossá tétele környezeti változóval végezhető el.

Hozzon létre egy API-kulcsot a Application Insights erőforráson belül, és lépjen a **beállítások > a Függvényalkalmazás konfigurációjában** . Válassza az **új alkalmazás beállítása** lehetőséget, és adjon meg egy nevet `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` és egy értéket, amely megfelel az API-kulcsnak.

Ha azonban felismeri és megbízik az összes csatlakoztatott kiszolgálón, a hitelesített csatorna nélkül is kipróbálhatja az egyéni szűrőket. Ez a beállítás hat hónapig elérhető. Ezt a felülbírálást minden új munkamenet esetében meg kell adni, vagy ha egy új kiszolgáló online állapotba kerül.

![Élő metrikák hitelesítési beállításai](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Javasoljuk, hogy a hitelesített csatornát úgy állítsa be, hogy a szűrési feltételeknek megfelelő bizalmas információk (például a Vevőkód) megadása előtt.
>

## <a name="supported-features-table"></a>Támogatott szolgáltatások táblázata

| Nyelv                         | Alapszintű mérőszámok       | Teljesítmény-mérőszámok | Egyéni szűrés    | Minta telemetria    | PROCESSZOR bontása folyamat alapján |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET-keretrendszer                   | Támogatott (V 2.7.2 +) | Támogatott (V 2.7.2 +) | Támogatott (V 2.7.2 +) | Támogatott (V 2.7.2 +) | Támogatott (V 2.7.2 +)  |
| .NET Core (TARGET =. NET-keretrendszer)| Támogatott (V 2.4.1 +) | Támogatott (V 2.4.1 +) | Támogatott (V 2.4.1 +) | Támogatott (V 2.4.1 +) | Támogatott (V 2.4.1 +)  |
| .NET Core (TARGET =. NET mag)     | Támogatott (V 2.4.1 +) | Támogatott*          | Támogatott (V 2.4.1 +) | Támogatott (V 2.4.1 +) | **Nem támogatott**    |
| Azure Functions v2               | Támogatott           | Támogatott           | Támogatott           | Támogatott           | **Nem támogatott**    |
| Java                             | Támogatott (V 2.0.0 +) | Támogatott (V 2.0.0 +) | **Nem támogatott**   | **Nem támogatott**   | **Nem támogatott**    |
| Node.js                          | Támogatott (V 1.3.0 +) | Támogatott (V 1.3.0 +) | **Nem támogatott**   | Támogatott (V 1.3.0 +) | **Nem támogatott**    |

Az alapszintű mérőszámok közé tartozik a kérelem, a függőség és a kivételek aránya. A teljesítmény-metrikák (teljesítményszámlálók) tartalmazzák a memóriát és a PROCESSZORt. A minta telemetria a sikertelen kérések és függőségek, kivételek, események és Nyomkövetések részletes információit jeleníti meg.

 \* A PerfCounters támogatása némileg eltér a .NET-keretrendszer azon verziói között, amelyek nem célozzák meg a .NET-keretrendszert:

- A PerfCounters-metrikák a Windows Azure App Service operációs rendszerben való futtatása esetén támogatottak. (A AspNetCore SDK 2.4.1-es vagy újabb verziója)
- A PerfCounters akkor támogatottak, ha az alkalmazás bármely Windows-gépen (VM vagy Cloud Service vagy on-Prem stb.) fut. (A AspNetCore SDK 2.7.1-es vagy újabb verziója), de a .NET Core 2,0-es vagy újabb verzióját célzó alkalmazásokhoz.
- A PerfCounters akkor támogatottak, ha az alkalmazás bárhol fut (Linux, Windows, app Service for Linux, containers stb.) a legújabb verziókban (azaz a AspNetCore SDK-beli 2.8.0 vagy újabb verziójában), de csak a .NET Core 2,0-es vagy újabb verzióját célzó alkalmazásokhoz.

## <a name="troubleshooting"></a>Hibaelhárítás

Élő metrikastream eltérő IP-címeket használ, mint a többi Application Insights telemetria. Győződjön meg arról, hogy az [IP-címek](./ip-addresses.md) meg vannak nyitva a tűzfalon. Győződjön meg arról is, hogy a [kimenő portok élő metrikastream](./ip-addresses.md#outgoing-ports) a kiszolgálók tűzfalán vannak megnyitva.

## <a name="next-steps"></a>Következő lépések

* [Használat figyelése Application Insights](./usage-overview.md)
* [A diagnosztikai keresés használata](./diagnostic-search.md)
* [Profilkészítő](./profiler.md)
* [Pillanatkép-hibakereső](./snapshot-debugger.md)