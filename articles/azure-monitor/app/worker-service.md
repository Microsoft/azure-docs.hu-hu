---
title: Application Insights a Worker Service-alkalmazásokhoz (nem HTTP-alkalmazások)
description: A .NET Core/. NET Framework nem HTTP-alkalmazások figyelése Azure Monitor Application Insightsokkal.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: c1ca594626d4384c9dfb62990ee2017d2094fca4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371855"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights Worker Service-alkalmazásokhoz (nem HTTP-alkalmazások)

A [Application INSIGHTS SDK for Worker szolgáltatás](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) egy új SDK, amely a nem HTTP-alapú számítási feladatokhoz, például az üzenetküldéshez, a háttérbeli feladatokhoz, a konzol alkalmazásaihoz, Az ilyen típusú alkalmazások nem rendelkeznek olyan bejövő HTTP-kérések fogalmával, mint a hagyományos ASP.NET/ASP.NET Core-webalkalmazások, és így Application Insights csomagok használata [ASP.net](asp-net.md) vagy [ASP.net Core](asp-net-core.md) alkalmazásokhoz nem támogatott.

Az új SDK önmagában nem végez telemetria-gyűjteményt. Ehelyett más jól ismert Application Insights automatikus gyűjtők, például a [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), a [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), a [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) stb. Ez az SDK a `IServiceCollection` telemetria-gyűjtés engedélyezéséhez és konfigurálásához a bővítmények metódusait teszi elérhetővé.

## <a name="supported-scenarios"></a>Támogatott esetek

Az [Application INSIGHTS SDK for Worker szolgáltatás](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ideális a nem HTTP-alapú alkalmazásokhoz, függetlenül attól, hogy hol vagy hogyan futnak. Ha az alkalmazás fut, és hálózati kapcsolattal rendelkezik az Azure-hoz, a telemetria gyűjthet. A Application Insights figyelése mindenhol támogatott a .NET Core-ban. Ez a csomag az újonnan bevezetett [.net core 3,0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)-ben, a [ASP.net Core 2.1/2.2-ben](/aspnet/core/fundamentals/host/hosted-services), a konzolon futó alkalmazások (.net Core/.NET Framework) háttérbeli feladatai között használható.

## <a name="prerequisites"></a>Előfeltételek

Érvényes Application Insights kialakítási kulcs. Ez a kulcs szükséges ahhoz, hogy bármilyen telemetria küldjön a Application Insightsnak. Ha létre kell hoznia egy új Application Insights-erőforrást a kialakítási kulcs beszerzéséhez, tekintse meg a [Application Insights erőforrás létrehozása](./create-new-resource.md)című témakört.

## <a name="using-application-insights-sdk-for-worker-services"></a>Application Insights SDK használata a Worker-szolgáltatásokhoz

1. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.
   A következő kódrészlet azokat a módosításokat mutatja be, amelyeket hozzá kell adni a projekt `.csproj` fájljához.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. A `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` bővítmény metódusának meghívása a alkalmazásban `IServiceCollection` , amely a kialakítási kulcsot adja meg. Ezt a metódust az alkalmazás elején kell meghívni. A pontos hely az alkalmazás típusától függ.

1. Egy példány vagy egy példány beolvasása `ILogger` `TelemetryClient` a függőségi injektálási (di) tárolóból a `serviceProvider.GetRequiredService<TelemetryClient>();` konstruktor befecskendezésének meghívásával vagy használatával. Ez a lépés elindítja a és az `TelemetryConfiguration` automatikus gyűjtési modulok beállítását.

Az egyes alkalmazási típusok részletes ismertetését a következő szakaszokban találja.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 Worker szolgáltatásalkalmazás

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) van megosztva

1. A [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) letöltése és telepítése
2. Új feldolgozói szolgáltatási projekt létrehozása a Visual Studio új projekt sablonjának vagy parancssorának használatával `dotnet new worker`
3. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.

4. Adja hozzá `services.AddApplicationInsightsTelemetryWorkerService();` a `CreateHostBuilder()` metódust az `Program.cs` osztályban, ahogy az alábbi példában is látható:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Módosítsa az `Worker.cs` alább látható példaként.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Állítsa be a kialakítási kulcsot.

    Bár a kialakítási kulcsot argumentumként is megadhatja `AddApplicationInsightsTelemetryWorkerService` , javasoljuk, hogy adja meg a kialakítási kulcsot a konfigurációban. Az alábbi mintakód bemutatja, hogyan adható meg a kialakítási kulcs a alkalmazásban `appsettings.json` . Győződjön meg arról, hogy `appsettings.json` a közzététel során a rendszer átmásolja az alkalmazás gyökerének mappájába.

```json
    {
        "ApplicationInsights":
        {
            "InstrumentationKey": "putinstrumentationkeyhere"
        },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Azt is megteheti, hogy a kialakítási kulcsot a következő környezeti változók egyikében határozza meg.
`APPINSIGHTS_INSTRUMENTATIONKEY` vagy `ApplicationInsights:InstrumentationKey`

Például: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
VAGY `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Általában meghatározza a rendszerállapot- `APPINSIGHTS_INSTRUMENTATIONKEY` kulcsot a webes Feladatokhoz Web Apps központilag telepített alkalmazásokhoz.

> [!NOTE]
> A kód WINS-ben megadott rendszerállapot-kulcs a környezeti változón keresztül `APPINSIGHTS_INSTRUMENTATIONKEY` , amely más beállításokon keresztül nyerhető.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Az üzemeltetett szolgáltatásokkal ASP.NET Core háttérbeli feladatok

[Ez a](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio) dokumentum ismerteti, hogyan hozhat létre háttérbeli feladatokat ASP.net Core 2.1/2.2 alkalmazásban.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService) van megosztva

1. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.
2. Adja hozzá `services.AddApplicationInsightsTelemetryWorkerService();` a `ConfigureServices()` metódushoz az alábbi példában látható módon:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

A következő kód a `TimedHostedService` háttérben futó feladat logikájának helye.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Állítsa be a kialakítási kulcsot.
   A `appsettings.json` fenti példában szereplő .net Core 3,0 Worker Service-példa használatával.

## <a name="net-corenet-framework-console-application"></a>.NET Core/. NET Framework Console-alkalmazás

A cikk elején említettek szerint az új csomag lehetővé teszi, hogy a Application Insights Telemetria akár egy normál konzolos alkalmazásból is engedélyezzék. Ez a csomag célja [`NetStandard2.0`](/dotnet/standard/net-standard) , és így a .net Core 2,0-as vagy újabb verziójú konzolos alkalmazásokhoz, illetve a .NET-keretrendszer 4.7.2 vagy újabb verziójához is használható.

A teljes példa [itt](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) van megosztva

1. Telepítse a [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) csomagot az alkalmazásba.

2. Módosítsa a Program.cs az alábbi példában látható módon.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Ez a konzolszoftver ugyanazokat az alapértelmezett beállításokat használja `TelemetryConfiguration` , és a korábbi szakaszban található példákkal megegyező módon is testreszabható.

## <a name="run-your-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A fentiekben ismertetett feldolgozók a http-hívást másodpercenként bing.com, és néhány naplót is kibocsátanak a használatával `ILogger` . Ezek a sorok a hívásával vannak becsomagolva `StartOperation` `TelemetryClient` , amely egy művelet létrehozására szolgál (ebben a példában a `RequestTelemetry` "művelet"). A Application Insights összegyűjti ezeket a ILogger-naplókat (alapértelmezés szerint a figyelmeztetést) és a függőségeket, és a `RequestTelemetry` szülő-gyermek kapcsolattal együtt lesznek összekapcsolva. A korreláció a folyamat/hálózat határán is működik. Ha például a hívás egy másik figyelt összetevőre történt, akkor a rendszer ezt a szülővel is összefügg.

Ez az egyéni művelet `RequestTelemetry` azt is megteheti, hogy egy tipikus webalkalmazásban egy bejövő webes kérelem megfelelője. Habár a művelet nem szükséges, a legjobban megfelel a [Application Insights korrelációs adatmodellnek](./correlation.md) – a `RequestTelemetry` szülő műveletként való működéssel, és minden olyan telemetria, amely a munkavégző iteráción belül jön létre, és logikailag ugyanahhoz a művelethez tartozóként van kezelve. Ez a megközelítés azt is biztosítja, hogy az összes generált telemetria (automatikus és manuális) ugyanaz legyen `operation_id` . A mintavételezés alapján a `operation_id` mintavételezési algoritmus vagy az összes telemetria egyetlen iterációból tartja vagy eldobja.

Az alábbi listában a Application Insights által automatikusan összegyűjtött teljes telemetria szerepelnek.

### <a name="live-metrics"></a>Élő metrikák

Az [élő metrikák](./live-stream.md) segítségével gyorsan ellenőrizheti, hogy a Application Insights figyelése megfelelően van-e konfigurálva. Habár néhány percet is igénybe vehet, amíg a telemetria elindul a Portálon és az elemzésekben, az élő metrikák közel valós időben mutatják be a futó folyamat CPU-használatát. Más telemetria is megjeleníthet, például a kérelmeket, a függőségeket, a Nyomkövetéseket stb.

### <a name="ilogger-logs"></a>ILogger-naplók

`ILogger`A súlyosság vagy a nagyobb arány használatával kibocsátott naplók `Warning` automatikusan rögzítésre kerülnek. A [ILogger-dokumentumok](ilogger.md#control-logging-level) követésével testreszabhatja, hogy a Application Insights mely naplózási szinteket rögzíti.

### <a name="dependencies"></a>Függőségek

A függőségi gyűjtemény alapértelmezés szerint engedélyezve van. [Ez](asp-net-dependencies.md#automatically-tracked-dependencies) a cikk az automatikusan összegyűjtött függőségeket ismerteti, valamint a manuális nyomon követésre szolgáló lépéseket is tartalmaz.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` Alapértelmezés szerint engedélyezve van, és a rendszer a .NET Core 3,0-alkalmazásokból gyűjti össze a számlálók alapértelmezett készletét. A [EventCounter](eventcounters.md) -oktatóanyag a gyűjtött teljesítményszámlálók alapértelmezett készletét sorolja fel. Emellett a lista testreszabására vonatkozó utasításokat is tartalmaz.

### <a name="manually-tracking-additional-telemetry"></a>További telemetria manuális követése

Habár az SDK automatikusan gyűjti a telemetria a fentiekben leírtak szerint, a legtöbb esetben a felhasználónak további telemetria kell küldenie Application Insights szolgáltatásnak. A további telemetria követésének ajánlott módja a `TelemetryClient` függőségi befecskendezésből származó példány beszerzése, majd az egyik támogatott API-módszer meghívása `TrackXXX()` [](api-custom-events-metrics.md) . Egy másik tipikus használati eset a [műveletek egyéni nyomon követése](custom-operations-tracking.md). Ezt a megközelítést a fenti feldolgozói példákban mutatjuk be.

## <a name="configure-the-application-insights-sdk"></a>A Application Insights SDK konfigurálása

A `TelemetryConfiguration` Worker Service SDK alapértelmezés szerint a ASP.net-ben vagy ASP.net Core alkalmazásban használt automatikus konfigurációhoz hasonlít, mínusz a telemetria a-ból való dúsításához használt TelemetryInitializers `HttpContext` .

Testreszabhatja a Application Insights SDK for Worker szolgáltatást az alapértelmezett konfiguráció módosításához. Előfordulhat, hogy a Application Insights ASP.NET Core SDK felhasználói megismerik a konfiguráció módosítását ASP.NET Core beépített [függőségi befecskendezés](/aspnet/core/fundamentals/dependency-injection)használatával. A WorkerService SDK is hasonló alapelveken alapul. A szakaszban ismertetett megfelelő módszerek meghívásával szinte minden konfigurációs változást megtehet a `ConfigureServices()` szakaszon `IServiceCollection` .

> [!NOTE]
> Az SDK használatakor a konfiguráció módosításának módosítása nem `TelemetryConfiguration.Active` támogatott, és a módosítások nem fognak megjelenni.

### <a name="using-applicationinsightsserviceoptions"></a>A ApplicationInsightsServiceOptions használata

A következő példához hasonlóan módosíthat néhány gyakori beállítást `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService` , ahogy az alábbi példában is látható:

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

Vegye figyelembe, hogy ebben az SDK-ban a ASP.NET Core SDK-ban lévő `ApplicationInsightsServiceOptions` névtérben található `Microsoft.ApplicationInsights.WorkerService` `Microsoft.ApplicationInsights.AspNetCore.Extensions` .

Leggyakrabban használt beállítások a `ApplicationInsightsServiceOptions`

|Beállítás | Leírás | Alapértelmezett
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics funkció engedélyezése/letiltása | true
|EnableAdaptiveSampling | Adaptív mintavételezés engedélyezése/letiltása | true
|EnableHeartbeat | A szívverések funkció engedélyezése/letiltása, amely rendszeres időközönként (15 perces alapértelmezett) a "HeartBeatState" nevű egyéni metrikát küldi el a (z), például a .NET-es verzióval, az Azure-környezettel kapcsolatos információkkal, ha vannak ilyenek, stb. | true
|AddAutoCollectedMetricExtractor | Az AutoCollectedMetrics Extractor engedélyezése/letiltása, amely egy olyan TelemetryProcessor, amely előre összevont metrikákat küld a kérelmek/függőségek számára a mintavétel megkezdése előtt. | true
|EnableDiagnosticsTelemetryModule | Engedélyezés/letiltás `DiagnosticsTelemetryModule` . Ha letiltja ezt a beállítást, a rendszer figyelmen kívül hagyja a következő beállításokat: `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

A legnaprakészebb listához tekintse [meg `ApplicationInsightsServiceOptions` a konfigurálható beállításokat](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>Mintavételezés

Az Application Insights SDK for Worker szolgáltatás a rögzített sebességű és az adaptív mintavételezést is támogatja. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van. A mintavétel letiltható a `EnableAdaptiveSampling` [ApplicationInsightsServiceOptions](#using-applicationinsightsserviceoptions) kapcsolójának használatával

További mintavételi beállítások konfigurálásához a következő példa használható.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

További információ a [mintavételi](#sampling) dokumentumban található.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers hozzáadása

Használjon [telemetria-inicializálást](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , ha az összes telemetria ellátott tulajdonságokat szeretné megadni.

Vegyen fel minden újat `TelemetryInitializer` a `DependencyInjection` tárolóba, és az SDK automatikusan hozzáadja őket a-hoz `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers eltávolítása

A telemetria inicializálók alapértelmezés szerint jelennek meg. Az összes vagy adott telemetria-inicializáló eltávolításához használja a következő mintát a hívása *után* `AddApplicationInsightsTelemetryWorkerService()` .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Telemetria-processzorok hozzáadása

Egyéni telemetria-processzorokat a `TelemetryConfiguration` bővítmény metódusának használatával adhat hozzá `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . A telemetria processzorok [speciális szűrési forgatókönyvekben](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) való használata lehetővé teszi, hogy a Application Insights a szolgáltatásnak küldött, a telemetria által befoglalt vagy kizárni kívánt funkciók jobban átirányíthatók legyenek. Használja az alábbi példát.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Alapértelmezett TelemetryModules konfigurálása vagy eltávolítása

A Application Insights telemetria-modulokkal automatikusan gyűjti az adott számítási feladatokhoz tartozó telemetria a manuális nyomon követés nélkül.

A következő automatikus gyűjtési modulok alapértelmezés szerint engedélyezve vannak. Ezeknek a moduloknak a feladata a telemetria automatikus gyűjtése. Az alapértelmezett viselkedés megváltoztatásához letilthatja vagy konfigurálhatja őket.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` -(Jelenleg a telemetria modult érintő probléma merül fel. Ideiglenes áthidaló megoldásért lásd: [GitHub-probléma 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Az alapértelmezett beállítások konfigurálásához `TelemetryModule` használja a bővítmény metódusát a `ConfigureTelemetryModule<T>` `IServiceCollection` következő példában látható módon:.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Az alapértelmezett csatorna: `ServerTelemetryChannel` . A következő példában látható módon felülbírálhatja azt.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Telemetria dinamikus letiltása

Ha a telemetria feltételesen és dinamikusan szeretné letiltani, akkor előfordulhat, hogy `TelemetryConfiguration` a kódban bárhol feloldja a példányt ASP.net Core függőségi injektálási tárolóval, és beállítja `DisableTelemetry` a jelölőt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hogyan követhetem nyomon az automatikusan összegyűjtött telemetria?

Szerezzen be egy példányt a `TelemetryClient` konstruktor injekció használatával, és hívja meg a szükséges `TrackXXX()` metódust. Nem ajánlott új `TelemetryClient` példányokat létrehozni. Az egy példánya `TelemetryClient` már regisztrálva van a `DependencyInjection` tárolóban, amely a `TelemetryConfiguration` többi telemetria együtt osztozik. Az új `TelemetryClient` példányok létrehozása csak akkor javasolt, ha olyan konfigurációra van szükség, amely eltér a többi telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Használhatom a Visual Studio IDE-t a Application Insights bevezetésére a Worker Service-projektbe?

A Visual Studio IDE-előkészítés jelenleg csak a ASP.NET/ASP.NET Core-alkalmazások esetében támogatott. Ez a dokumentum akkor frissül, ha a Visual Studio-hajók támogatják a feldolgozó szolgáltatások alkalmazásainak bevezetését.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Engedélyezhető Application Insights-figyelés az olyan eszközökkel, mint a Állapotmonitor?

Nem. A [Állapotmonitor](./monitor-performance-live-website-now.md) és a [Állapotmonitor v2](./status-monitor-v2-overview.md) jelenleg csak a 4. x ASP.net támogatja.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Ha Linuxon futtatom az alkalmazást, az összes funkció támogatott?

Igen. Az SDK funkcióinak támogatása minden platformon azonos, a következő kivételekkel:

* A teljesítményszámlálók csak a Windows rendszerben támogatottak, az élő metrikák által megjelenített processzor/memória kivételével.
* Annak ellenére `ServerTelemetryChannel` , hogy alapértelmezés szerint engedélyezve van, ha az alkalmazás Linux vagy MacOS rendszeren fut, a csatorna nem hoz létre automatikusan helyi tárolási mappát, hogy a telemetria átmenetileg megmaradjanak, ha hálózati problémák vannak. Ennek a korlátozásnak a miatt a telemetria elvész, ha ideiglenes hálózati vagy kiszolgálói problémák merülnek fel. A probléma megkerüléséhez konfigurálja a csatorna helyi mappáját:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Mintaalkalmazások

[.Net Core Console-alkalmazás](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Használja ezt a mintát, ha a .NET Core (2,0 vagy újabb) vagy a .NET Framework (4.7.2 vagy újabb) nyelven írt konzol alkalmazást használ

[ASP .net Core háttérbeli feladatok a HostedServices](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService) Akkor használja ezt a mintát, ha a Asp.Net Core 2.1/2.2-es verzióban van, és a háttérben elvégzendő feladatokat az [alábbi](/aspnet/core/fundamentals/host/hosted-services) hivatalos útmutatás alapján hozza létre

[.Net Core 3,0 Worker szolgáltatás](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Akkor használja ezt a mintát, ha a .NET Core 3,0 Worker szolgáltatásalkalmazás az [alábbi](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio#worker-service-template) hivatalos útmutatás szerint van

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

* [Olvassa el és járuljon hozzá a kódhoz](https://github.com/microsoft/ApplicationInsights-dotnet).

A legújabb frissítések és hibajavítások [olvassa el a kibocsátási megjegyzéseket](./release-notes.md).

## <a name="next-steps"></a>Következő lépések

* [Az API használatával](./api-custom-events-metrics.md) saját eseményeket és mérőszámokat küldhet az alkalmazás teljesítményének és használatának részletes áttekintéséhez.
* A [további függőségek nyomon követése nem történik meg automatikusan](./auto-collect-dependencies.md).
* Az [automatikusan összegyűjtött telemetria gazdagítása vagy szűrése](./api-filtering-sampling.md).
* [Függőség injekció ASP.net Coreban](/aspnet/core/fundamentals/dependency-injection).
