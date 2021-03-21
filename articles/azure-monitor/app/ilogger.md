---
title: A .NET nyomkövetési naplók megismerése a ILogger-Azure Application Insights
description: Minták az Azure Application Insights ILogger Provider ASP.NET Core és konzolos alkalmazásokkal való használatáról.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e7d0bd6f7d93eac944e0d53a8e898463bf9887f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592268"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider a Microsoft. Extension. naplózáshoz

Ez a cikk bemutatja, hogyan `ApplicationInsightsLoggerProvider` rögzíthet `ILogger` naplókat a konzolon és ASP.net Core alkalmazásokban.
További információ a naplózásról: [ASP.net Core naplózása](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Alkalmazások ASP.NET Core

`ApplicationInsightsLoggerProvider` Alapértelmezés szerint engedélyezve van ASP.NET Core alkalmazásokhoz, ha a ApplicationInsights [kód](./asp-net-core.md) vagy [kód nélküli](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) megközelítéssel van konfigurálva.

Alapértelmezés szerint csak a *Figyelmeztetési* és a fenti `ILogger` naplók (az összes [kategóriából](/aspnet/core/fundamentals/logging/#log-category)) lesznek elküldve a Application Insightsra. [Ezt a viselkedést azonban testre is szabhatja](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). További lépések szükségesek a ILogger-naplók programból való rögzítéséhez **. cs** vagy **Startup. cs**. (Lásd: [ILogger-naplók rögzítése a Startup. cs és a program. cs ASP.net Core alkalmazásokban](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Ha `ApplicationInsightsLoggerProvider` más Application Insights figyelés nélkül szeretné használni, kövesse az alábbi lépéseket.

1. Telepítse a NuGet csomagot:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Módosítás `Program.cs` az itt látható módon:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

A 2. lépésben szereplő kód konfigurálja `ApplicationInsightsLoggerProvider` . A következő kód egy példa vezérlő osztályt mutat be, amely `ILogger` a naplók küldésére használja. A naplókat a Application Insights rögzíti.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-naplók rögzítése a Startup. cs és a program. cs alkalmazásban ASP.NET Core alkalmazásokban

> [!NOTE]
> A ASP.NET Core 3,0-es és újabb verzióiban már nem lehet beszúrni `ILogger` a Startup. cs és a program. cs. https://github.com/aspnet/Announcements/issues/353További részletekért tekintse meg a következőt:.

`ApplicationInsightsLoggerProvider` a naplókat az alkalmazás indításának korai szakaszában rögzítheti. Bár a ApplicationInsightsLoggerProvider automatikusan engedélyezve van Application Insights (a 2.7.1-es verziótól kezdődően), nem rendelkezik a kialakítási kulccsal, amely a folyamat későbbi részében nem szerepel. Így csak a **vezérlő**/other osztályaiból származó naplók lesznek rögzítve. A **program. cs** és a **Startup. cs** -től kezdődő összes napló rögzítéséhez explicit módon engedélyeznie kell a ApplicationInsightsLoggerProvider a Instrumentation-kulcsot. Emellett a *TelemetryConfiguration* nincs teljesen beállítva a **program. cs** vagy a **Startup. cs** -ból való bejelentkezéskor. Így ezek a naplók a [InMemoryChannel](./telemetry-channels.md)-t használó minimális konfigurációval rendelkeznek, nem [mintavételezéssel](./sampling.md), és nem rendelkeznek szabványos [telemetria-inicializálással vagy processzorokkal](./api-filtering-sampling.md).

Az alábbi példák bemutatják ezt a képességet a **program. cs** és a **Startup. cs**-ban.

#### <a name="example-programcs"></a>Példa program. cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Példa: Startup. cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Áttelepítés a régi ApplicationInsightsLoggerProvider

A Microsoft. ApplicationInsights. AspNet SDK-verziók a 2.7.1-es verzióban már elavult naplózási szolgáltatót támogattak. Ezt a szolgáltatót engedélyezte a ILoggerFactory **AddApplicationInsights ()** bővítményi metódusa. Javasoljuk, hogy telepítse át az új szolgáltatóra, amely két lépést is magában foglal:

1. A kettős naplózás elkerüléséhez távolítsa el a *ILoggerFactory. AddApplicationInsights ()* metódust az **Startup.Configure ()** metódusból.
2. Alkalmazza újra a kód szűrési szabályait, mivel azokat az új szolgáltató nem veszi figyelembe. A *ILoggerFactory. AddApplicationInsights ()* túlterhelései minimális naplózási szint vagy szűrési funkciókat vettek igénybe. Az új szolgáltatónál a szűrés a naplózási keretrendszer részét képezi. Ezt a Application Insights szolgáltató nem végzi el. Így a *ILoggerFactory. AddApplicationInsights ()-* n keresztül biztosított szűrőket el kell távolítani. És a szűrési szabályokat a [vezérlés naplózási szintjének](#control-logging-level) útmutatása szerint kell megadni. Haappsettings.jshasználatával szűri *a* naplózást, az továbbra is együttműködik az új szolgáltatóval, mert mindkettő ugyanazt a szolgáltatói aliast használja, *ApplicationInsights*.

Továbbra is használhatja a régi szolgáltatót. (A rendszer csak a főverzió változását veszi át 3 értékre. *XX*.) de azt javasoljuk, hogy a következő okokból telepítse át az új szolgáltatóra:

- Az előző szolgáltató nem támogatja a [naplózási hatóköröket](/aspnet/core/fundamentals/logging#log-scopes). Az új szolgáltatóban a hatókör tulajdonságai automatikusan egyéni tulajdonságokként lesznek hozzáadva az összegyűjtött telemetria.
- A naplók mostantól jóval korábban is rögzíthetők az alkalmazás indítási folyamatában. A **programból** származó naplók és az **indítási** osztályok mostantól rögzíthetők.
- Az új szolgáltató esetében a szűrés a keretrendszer szintjén történik. A naplókat a Application Insights szolgáltatóhoz ugyanúgy szűrheti, mint a többi szolgáltatónál, például a beépített szolgáltatók, például a konzol, a hibakeresés és így tovább. Ugyanezeket a szűrőket is alkalmazhatja több szolgáltatóra is.
- ASP.NET Core (2,0-es és újabb verziók) esetében a  [naplózási szolgáltatók engedélyezésének](https://github.com/aspnet/Announcements/issues/255) ajánlott módja a **program. cs** ILoggingBuilder lévő bővítmény-metódusok használata.

> [!Note]
> Az új szolgáltató a NETSTANDARD 2.0-s vagy újabb verzióját használó alkalmazásokhoz érhető el. A [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) verziójának 2.14.0 kezdve új szolgáltató is elérhető a .NET-keretrendszer NET461 vagy újabb verzióját használó alkalmazásokhoz. Ha az alkalmazás a régebbi .NET Core-verziókat (például a .NET Core 1,1-as verziót) célozza meg, vagy ha a .NET-keretrendszert NET46-nál kisebbre célozza, folytassa a régi szolgáltató használatával.

## <a name="console-application"></a>Konzol alkalmazás

> [!NOTE]
> Létezik egy Application Insights új, [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nevű SDK, amely lehetővé teszi a Application Insights (ILogger és más Application Insights telemetria) használatát bármely konzolos alkalmazáshoz. Ezt a csomagot és a hozzá tartozó utasításokat ajánlott [használni.](./worker-service.md)

Ha a ApplicationInsightsLoggerProvider-et más Application Insights figyelés nélkül szeretné használni, kövesse az alábbi lépéseket.

Telepített csomagok:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Ez a példa az önálló csomagot használja `Microsoft.Extensions.Logging.ApplicationInsights` . Alapértelmezés szerint ez a konfiguráció a "csupasz minimális" TelemetryConfiguration használja az adatok Application Insights való küldéséhez. A csupasz minimális érték azt jelenti, hogy a InMemoryChannel a használt csatorna. Nincs mintavételezés és standard TelemetryInitializers. Ezt a viselkedést felülbírálhatja egy Console-alkalmazás esetében, az alábbi példában látható módon.

Telepítse ezt a kiegészítő csomagot:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan bírálhatja felül az alapértelmezett TelemetryConfiguration az **services.Configure \<TelemetryConfiguration> ()** metódus használatával. Ez a példa beállítja `ServerTelemetryChannel` és mintavételezést végez. Hozzáadja az egyéni ITelemetryInitializer a TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Vezérlés naplózási szintje

`ILogger` beépített mechanizmussal rendelkezik a [naplózási szűrés](/aspnet/core/fundamentals/logging#log-filtering)alkalmazásához. Így szabályozhatja az egyes regisztrált szolgáltatók számára eljuttatott naplókat, beleértve a Application Insights szolgáltatót is. A szűrés a konfigurációban végezhető el (általában egy *appsettings.js* fájl használatával) vagy kódban.

Az alábbi példák bemutatják, hogyan alkalmazhat szűrési szabályokat a következőre: `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Szűrési szabályok létrehozása a konfigurációban appsettings.jsbekapcsolva

A ApplicationInsightsLoggerProvider esetében a szolgáltató aliasa a következő: `ApplicationInsights` . A *appsettings.json* következő szakasza arra utasítja a naplózási szolgáltatókat, hogy általában a *riasztási szintű figyelmeztetésre* és a fentiekre jelentkezzenek be. Ezután felülbírálja a `ApplicationInsightsLoggerProvider` "Microsoft" kezdetű naplózási kategóriákat a következő szintű *hiba* esetén:.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Szűrési szabályok létrehozása a kódban

A következő kódrészlet az összes kategóriából származó *Figyelmeztetési* és ennél magasabb szintű naplókat *konfigurálja, valamint* a "Microsoft" kifejezéssel kezdődően a (z `ApplicationInsightsLoggerProvider` ) " Ez a konfiguráció ugyanaz, mint a *appsettings.json* előző szakaszában.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Naplózási hatókörök

`ApplicationInsightsLoggingProvider` a támogatja a [naplózási hatóköröket](/aspnet/core/fundamentals/logging#log-scopes) , és a hatókörök alapértelmezés szerint engedélyezve vannak.

Ha a hatókör típusa `IReadOnlyCollection<KeyValuePair<string,object>>` , akkor a gyűjtemény minden kulcs-érték párja egyéni tulajdonságokként lesz hozzáadva az Application telemetria. Az alábbi példában a naplók a `TraceTelemetry` (z) és a (z) ("Mykey", "MyValue") tulajdonságban lesznek rögzítve.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Ha bármely más típus hatókörként van használatban, akkor a "scope" tulajdonság alatt lesznek tárolva az Application telemetria. Az alábbi példában a a `TraceTelemetry` hatókört tartalmazó "scope" nevű tulajdonságot fogja tartalmazni.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Melyek a ApplicationInsightsLoggerProvider régi és új verziói?

A [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) tartalmaz egy beépített ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), amely a **ILoggerFactory** -bővítmény módszerein keresztül lett engedélyezve. Ezt a szolgáltatót a 2.7.1-es verzió elavultnak jelölte. A rendszer a következő főverzió változásakor teljesen eltávolítja. A [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomag maga nem elavult. A kérések, a függőségek és egyebek figyelésének engedélyezéséhez szükséges.

A javasolt alternatíva a [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)új önálló csomagja, amely továbbfejlesztett ApplicationInsightsLoggerProvider (Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) és bővítményi metódusokat tartalmaz a ILoggerBuilder-on való engedélyezéséhez.

A [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziója az új csomagtól függ, és lehetővé teszi a ILogger automatikus rögzítését.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Miért jelenik meg kétszer a Application Insights ILogger-naplója?

Az ismétlődés akkor fordulhat elő, ha a ApplicationInsightsLoggerProvider régebbi (már elavult) verziója engedélyezve van a hívással `AddApplicationInsights` `ILoggerFactory` . Ellenőrizze, hogy a **configure** metódus rendelkezik-e a következőkkel, majd távolítsa el:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Ha a Visual studióból történő hibakereséskor a kettős naplózást tapasztalja, a következő módon állítsa a `EnableDebugLogger`  *false (hamis) értéket* a Application Insightst engedélyező kódban. Ez az ismétlődés és javítás csak az alkalmazás hibakeresése esetén releváns.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Frissítettem a [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziójára, és a rendszer automatikusan rögzíti a ILogger naplóit. Hogyan teljesen kikapcsolja ezt a funkciót?

Tekintse meg a [vezérlők naplózási szintjének](#control-logging-level) című szakaszt, amelyből megtudhatja, hogyan szűrheti az általános naplókat. A ApplicationInsightsLoggerProvider kikapcsolásához használja a következőt `LogLevel.None` :

**A kódban:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**A konfigurációban:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Miért nem ugyanazokkal a tulajdonságokkal rendelkeznek a ILogger-naplók, mint mások?

Application Insights rögzíti és elküldi a ILogger-naplókat ugyanazzal a TelemetryConfiguration, amelyet minden más telemetria használ. Kivétel történt. Alapértelmezés szerint a TelemetryConfiguration nincs teljesen beállítva a **program. cs** vagy a **Startup. cs** naplófájlból való bejelentkezéskor. Ezen helyekről származó naplók nem rendelkeznek az alapértelmezett konfigurációval, így nem fognak futni az összes TelemetryInitializers és TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>A Microsoft. Extensions. Logging. ApplicationInsights önálló csomagot használom, és manuálisan szeretnék bejelentkezni néhány további egyéni telemetria. Hogyan tehetem ezt meg?

Ha az önálló csomagot használja, a `TelemetryClient` nem a di tárolóba van befecskendezve, ezért létre kell hoznia egy új példányt, `TelemetryClient` és ugyanazt a konfigurációt kell használnia, mint amelyet a naplózó szolgáltató használ, ahogy az alábbi kód mutatja. Ez biztosítja, hogy ugyanazt a konfigurációt használja az összes egyéni telemetria, valamint a ILogger telemetria.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Ha a Microsoft. ApplicationInsights. AspNetCore csomagot használja a Application Insights engedélyezéséhez, módosítsa ezt a kódot `TelemetryClient` közvetlenül a konstruktorba való beolvasáshoz. Példaként tekintse meg [ezt a gyakori kérdéseket](./asp-net-core.md#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Milyen Application Insights telemetria-típust hoz létre a ILogger-naplókból? Vagy Hol láthatom a ILogger-naplókat Application Insights?

A ApplicationInsightsLoggerProvider rögzíti a ILogger-naplókat, és létrehozza a TraceTelemetry. Ha a metódushoz egy kivétel objektumot ad át `Log` `ILogger` , a rendszer a TraceTelemetry helyett a *ExceptionTelemetry* hozza létre. Ezek a telemetria elemek ugyanazon a helyen találhatók, mint bármely más TraceTelemetry vagy ExceptionTelemetry, beleértve Application Insights a portált, az elemzést vagy a Visual Studio helyi hibakeresőjét is.

Ha szeretné mindig elküldeni a TraceTelemetry, használja a következő kódrészletet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nem telepítettem az SDK-t, és az Azure Web Apps bővítmény használatával Engedélyezem a Application Insights a ASP.NET Core alkalmazásokhoz. Hogyan használja az új szolgáltatót? 

Az Azure Web Apps Application Insights bővítménye az új szolgáltatót használja. A szűrési szabályokat módosíthatja az alkalmazás *appsettings.js* fájljában.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>A Microsoft. Extensions. Logging. ApplicationInsights önálló csomagja és a Application Insights Provider engedélyezése a **Builder hívásával. AddApplicationInsights ("rendszerállapotkulcsot")**. Van lehetőség rendszerállapot-kulcs beszerzésére a konfigurációból?


Módosítsa a program. cs és a appsettings.jsalkalmazást a következő módon:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Kapcsolódó szakasz `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ez a kód csak önálló naplózási szolgáltató használata esetén szükséges. A rendszeres Application Insights figyeléséhez a rendszer automatikusan betölti a kialakítási kulcsot a *ApplicationInsights: InstrumentationKey* konfigurációs elérési útról. A Appsettings.jsa következőhöz hasonlóan kell kinéznie:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Következő lépések

További információk:

* [Bejelentkezés ASP.NET Core](/aspnet/core/fundamentals/logging)
* [.NET-nyomkövetési naplók a Application Insights](./asp-net-trace-logs.md)

