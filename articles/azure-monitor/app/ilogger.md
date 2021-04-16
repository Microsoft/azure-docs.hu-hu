---
title: A .NET nyomkövetési naplók feltárása az ILogger használatával – Azure Application Insights
description: Példák az Azure Application Insights ILogger szolgáltató és ASP.NET Core- és konzolalkalmazások használatával.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568590"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider for Microsoft.Extension.Logging

Ez a cikk bemutatja, hogyan rögzítheti a naplókat a konzolon `ApplicationInsightsLoggerProvider` `ILogger` és ASP.NET Core-alkalmazásokban.
További információ a naplózásról: [Naplózás a ASP.NET Core-ban.](/aspnet/core/fundamentals/logging)

## <a name="aspnet-core-applications"></a>ASP.NET alapalkalmazások

`ApplicationInsightsLoggerProvider`alapértelmezés szerint engedélyezve van ASP.NET Core-alkalmazásokhoz, ha az [](./asp-net-core.md) ApplicationInsights code vagy [Code-less módszer használatával van konfigurálva.](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring)

Alapértelmezés *szerint csak* a Figyelmeztetés és a fenti naplók (az összes `ILogger` kategóriából) Application Insights a rendszer. [](/aspnet/core/fundamentals/logging/#log-category) Ezt a [viselkedést azonban testreszabhatja.](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection) Az ILogger-naplók **Program.cs** vagy **Startup.cs** fájlból való rögzítéséhez további lépések szükségesek. (Lásd: ILogger-naplók rögzítése a Startup.cs és a Program.cs fájlból [ASP.NET Core-alkalmazásokban.)](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)

Ha csak további monitorozási lehetőségek nélkül Application Insights `ApplicationInsightsLoggerProvider` használni, kövesse az alábbi lépéseket.

1. Telepítse a NuGet-csomagot:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Módosítsa `Program.cs` az itt látható módon:

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

A 2. lépésben megadott kód a következőt konfigurálja: `ApplicationInsightsLoggerProvider` . Az alábbi kód egy Controller osztályt mutat be, amely a használatával küld `ILogger` naplókat. A naplókat a rendszer Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ILogger-naplók rögzítése a Startup.cs és a Program.cs fájlból ASP.NET Core-alkalmazásokban

> [!NOTE]
> A ASP.NET Core 3.0-s és újabb rendszerében már nem lehet injektálni a `ILogger` Startup.cs és a Program.cs fájlba. További https://github.com/aspnet/Announcements/issues/353 részletekért lásd: .

`ApplicationInsightsLoggerProvider` A képes rögzíteni a naplókat az alkalmazás indításának korai szakaszában. Bár az ApplicationInsightsLoggerProvider automatikusan engedélyezve van az Application Insights-ban (a 2.7.1-es verziótól kezdve), a folyamat későbbi verziójáig nincs beállítva eszközkulcsa. Így a rendszer csak a **Controller**/other osztályból származó naplókat rögzíti. A **Program.cs** és **Startup.cs** fájltól kezdődő összes napló rögzítéséhez explicit módon engedélyeznie kell egy eszközkulcsot az ApplicationInsightsLoggerProvider számára. Emellett a *TelemetryConfiguration* nincs teljesen beállítva, amikor a **Program.cs** vagy a **Startup.cs fájlból jelentkezik** be. Így ezek a naplók olyan minimális konfigurációval fognak rendelkezik, amely [InMemoryChannelt](./telemetry-channels.md)használ, mintavételezés [nélkül,](./sampling.md)valamint standard [telemetriainicializálókat vagy processzorokat.](./api-filtering-sampling.md)

Az alábbi példák a **Program.cs és Startup.cs** fájl segítségével mutatják be ezt **a képességet.**

#### <a name="example-programcs"></a>Program.cs példa

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

#### <a name="example-startupcs"></a>Példa Startup.cs fájlra

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Áttelepítés a régi ApplicationInsightsLoggerProviderről

A Microsoft.ApplicationInsights.AspNet SDK 2.7.1 előtti verziói már elavult naplózási szolgáltatót támogattak. Ez a szolgáltató az **ILoggerFactory AddApplicationInsights() bővítmény** metódusával lett engedélyezve. Javasoljuk, hogy az új szolgáltatóra mináljon, amely két lépésből áll:

1. Távolítsa el az *ILoggerFactory.AddApplicationInsights()* hívást a **Startup.Configure()** metódusból a dupla naplózás elkerülése érdekében.
2. A szűrési szabályok újra alkalmazása a kódban, mert az új szolgáltató nem fogja őket tiszteletben tartani. Az *ILoggerFactory.AddApplicationInsights() túlterhelései* a minimális LogLevel- vagy szűrőfunkciókat vettük át. Az új szolgáltatóval a szűrés magának a naplózási keretrendszernek a része. Ezt a szolgáltató nem Application Insights. Ezért az *ILoggerFactory.AddApplicationInsights()* túlterhelései által biztosított szűrőket el kell távolítani. A szűrési szabályokat pedig a Naplózási szint [vezérlése utasításokkal kell biztosítani.](#control-logging-level) Ha a *appsettings.jshasználja* a naplózás szűréséhez, az továbbra is működni fog az új szolgáltatóval, mivel mindkettő ugyanazt az *ApplicationInsights* szolgáltatói aliast használja.

Továbbra is használhatja a régi szolgáltatót. (Csak a főverzió 3-ra való módosítása esetén lesz eltávolítva. *xx*.) Azt javasoljuk azonban, hogy az alábbi okokból áttért az új szolgáltatóra:

- A korábbi szolgáltató nem támogatja a [naplóhatókört.](/aspnet/core/fundamentals/logging#log-scopes) Az új szolgáltatóban a hatókörből származó tulajdonságok automatikusan egyéni tulajdonságokként vannak hozzáadva az összegyűjtött telemetriában.
- A naplók már sokkal korábban is rögzítettek az alkalmazásindítási folyamatban. A **program- és** indítási **osztályok** naplói mostantól rögzítettek.
- Az új szolgáltatóval a szűrés magára a keretrendszerre történik. A naplókat ugyanúgy szűrheti a Application Insights szolgáltatóra, mint más szolgáltatókra, beleértve a beépített szolgáltatókat, például a Konzolt, a Hibakeresést stb. Ugyanezeket a szűrőket több szolgáltatóra is alkalmazhatja.
- A ASP.NET Core (2.0 és újabb) esetében [](https://github.com/aspnet/Announcements/issues/255) a naplózási szolgáltatók engedélyezésének ajánlott módja az ILoggingBuilder bővítmény metódusának használata a **Program.cs fájlban.**

> [!Note]
> Az új szolgáltató a NETSTANDARD2.0-s vagy újabb alkalmazást célzó alkalmazásokhoz érhető el. A [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.14.0-s vagy újabb verziójától kezdve az új szolgáltató a NET461 vagy újabb .NET-keretrendszer alkalmazásokhoz is elérhető. Ha az alkalmazás régebbi .NET Core-verziókat (például .NET Core 1.1) vagy a NET46-ot nem .NET-keretrendszer, akkor továbbra is a régi szolgáltatót használja.

## <a name="console-application"></a>Konzolalkalmazás

> [!NOTE]
> Van egy új Application Insights SDK, a [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) amellyel bármely konzolalkalmazás számára engedélyezheti az Application Insights (ILogger és Application Insights telemetria) használatát. Javasoljuk, hogy ezt a csomagot és a kapcsolódó utasításokat innen [használja.](./worker-service.md)

Ha csak az ApplicationInsightsLoggerProvider alkalmazást szeretné használni más monitorozási Application Insights nélkül, kövesse az alábbi lépéseket.

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

Ez a példa a különálló csomagot `Microsoft.Extensions.Logging.ApplicationInsights` használja. Alapértelmezés szerint ez a konfiguráció az "üres minimális" TelemetryConfiguration konfigurációt használja az adatoknak a Application Insights. A minimális érték azt jelenti, hogy az InMemoryChannel a használt csatorna. Nincs mintavételezés, és nincsenek standard TelemetriaInitializerek. Ez a viselkedés felülbírálható egy konzolalkalmazásban, ahogy az alábbi példában látható.

Telepítse ezt a további csomagot:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A következő szakasz bemutatja, hogyan bírálhatja felül az alapértelmezett TelemetryConfiguration konfigurációt **aservices.Config\<TelemetryConfiguration> ()** metódussal. Ez a példa beállítja a és `ServerTelemetryChannel` a mintavételezést. Hozzáad egy egyéni ITelemetryInitializert a TelemetryConfiguration konfigurációhoz.

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

## <a name="control-logging-level"></a>Naplózási szint szabályozása

`ILogger`beépített mechanizmussal rendelkezik a [naplószűrés alkalmazására.](/aspnet/core/fundamentals/logging#log-filtering) Ez lehetővé teszi az egyes regisztrált szolgáltatóknak küldött naplók szabályozását, beleértve a Application Insights is. A szűrés konfigurációban (általában fájlonappsettings.js) vagy kódban is használhatja. 

Az alábbi példák bemutatják, hogyan alkalmazhat szűrési szabályokat a `ApplicationInsightsLoggerProvider` következőre: .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Szűrőszabályok létrehozása olyan konfigurációban, amely appsettings.jsbe

Az ApplicationInsightsLoggerProvider szolgáltatói aliasa `ApplicationInsights` a következő: . A következő szakasza *appsettings.jsarra* utasítja a naplózási szolgáltatókat, hogy a Figyelmeztetés vagy magasabb szinten *jelentkezzenek* be. Ezután felülbírálja a naplózási kategóriákat, amelyek a Hiba vagy magasabb szinten `ApplicationInsightsLoggerProvider` a "Microsoft" szóval kezdődnek. 

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

### <a name="create-filter-rules-in-code"></a>Szűrőszabályok létrehozása a kódban

Az alábbi kódrészlet az összes kategóriában a Warning (Figyelmeztetés) és a *(hibák)* és a (hibák) és a (fent) kategóriákban konfigurálja a naplókat, amelyek a "Microsoft" kezdettől a következővel kezdődnek:  `ApplicationInsightsLoggerProvider` . Ez a konfiguráció ugyanaz, mint a fájl előző *szakaszábanappsettings.jsa következőn:*.

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

`ApplicationInsightsLoggingProvider` A [támogatja a naplóhatókört,](/aspnet/core/fundamentals/logging#log-scopes) és a hatókörök alapértelmezés szerint engedélyezve vannak.

Ha a hatókör típusú, akkor a gyűjtemény minden kulcs-érték párja egyéni tulajdonságokként lesz hozzáadva az `IReadOnlyCollection<KeyValuePair<string,object>>` Application Insights telemetriához. Az alábbi példában a naplók a következő módon lesznek rögzítik: `TraceTelemetry` és a tulajdonságokban a ("MyKey", "MyValue") lesz.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Ha bármely más típus hatókörként van használva, akkor az Application Insights telemetriában a "Hatókör" tulajdonság alatt lesznek tárolva. Az alábbi példában a egy "Hatókör" nevű tulajdonságot fog `TraceTelemetry` tartalmazni, amely a hatókört tartalmazza.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Melyek az ApplicationInsightsLoggerProvider régi és új verziói?

[A Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) tartalmazott egy beépített ApplicationInsightsLoggerProvidert (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), amelyet az **ILoggerFactory** bővítmény metódusai engedélyeztek. Ez a szolgáltató elavultként van megjelölve a 2.7.1-es verziótól. A következő főverzió-módosításban teljesen el lesz távolítva. Maga [a Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomag nem elavult. Engedélyeznie kell a kérések, függőségek stb. monitorozását.

A javasolt alternatíva az új, különálló [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)csomag, amely továbbfejlesztett ApplicationInsightsLoggerProvidert (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) és az engedélyezéshez az ILoggerBuilder bővítmény metódusát tartalmazza.

[A Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziója függőséget vesz fel az új csomagtól, és engedélyezi az ILogger automatikus rögzítését.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Miért jelennek meg néhány ILogger-napló kétszer a Application Insights?

Duplikálás akkor fordulhat elő, ha az ApplicationInsightsLoggerProvider régebbi (már elavult) verzióját engedélyezi a `AddApplicationInsights` `ILoggerFactory` hívásával. Ellenőrizze, hogy a **Configure** metódus rendelkezik-e a következővel, és távolítsa el:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Ha kettős naplózást tapasztal, amikor hibakeresést Visual Studio, állítsa false (hamis) értékre a kódban, amely engedélyezi a Application Insights `EnableDebugLogger` az alábbiak szerint.  Ez a duplikálás és javítás csak az alkalmazás hibakeresése esetén releváns.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Frissítettem a [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1-es verziójára, és a rendszer automatikusan rögzíti az ILogger naplóit. Hogyan teljesen kikapcsolni ezt a funkciót?

A [naplók szűrésének](#control-logging-level) általános megtekintéséhez tekintse meg a Naplózási szint szabályozása című szakaszt. Az ApplicationInsightsLoggerProvider kikapcsolához használja a `LogLevel.None` következőt:

**Kódban:**

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Miért nem rendelkeznek egyes ILogger-naplók ugyanazokkal a tulajdonságokkal, mint a többi?

Application Insights rögzíti és elküldi az ILogger-naplókat ugyanazokkal a TelemetryConfiguration konfigurációval, amelyet minden más telemetriához használ. Van azonban egy kivétel. Alapértelmezés szerint a TelemetryConfiguration nincs teljesen beállítva, amikor a **Program.cs vagy Startup.cs** fájlból **jelentkezik be.** Az ezekről a helyekről származó naplók nem az alapértelmezett konfigurációval fognak érkezni, ezért nem futtatják az összes TelemetryInitializer és TelemetryProcessors elemet.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>A Microsoft.Extensions.Logging.ApplicationInsights különálló csomagot használom, és manuálisan szeretnék naplózni néhány további egyéni telemetriát. Hogyan kell ezt tennem?

Ha az önálló csomagot használja, a nem kerül be a DI-tárolóba, ezért létre kell hoznia egy új példányát, és ugyanazt a konfigurációt kell használnia, mint a naplózó szolgáltató, ahogyan az alábbi `TelemetryClient` `TelemetryClient` kód mutatja. Ez biztosítja, hogy a rendszer ugyanazt a konfigurációt használja az összes egyéni telemetriában, valamint az ILoggerből származó telemetriában is.

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
> Ha a Microsoft.ApplicationInsights.AspNetCore csomaggal engedélyezi a Application Insights, módosítsa ezt a kódot úgy, hogy közvetlenül a `TelemetryClient` konstruktorba jut. Példaként tekintse meg ezt [a gyakori kérdéseket.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Milyen Application Insights típusú telemetria készül az ILogger-naplókból? Vagy hol látom az ILogger-naplókat a Application Insights?

Az ApplicationInsightsLoggerProvider rögzíti az ILogger-naplókat, és traceTelemetry adatokat hoz létre bennük. Ha a metódusnak egy Exception objektumot ad át, a TraceTelemetry helyett az `Log` `ILogger` *ExceptionTelemetry* lesz létrehozva. Ezek a telemetriai elemek ugyanazokon a helyeken találhatók, mint a Application Insights bármely más TraceTelemetry vagy ExceptionTelemetry eleme, beleértve a portált, az elemzést vagy Visual Studio helyi hibakeresőt.

Ha mindig a TraceTelemetry elemetry üzenetet szeretné elküldeni, használja ezt a kódrészletet: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nincs telepítve az SDK, és az Azure Web Apps bővítmény használatával engedélyezem a Application Insights a ASP.NET Core-alkalmazásokhoz. Hogyan az új szolgáltatót? 

Az Application Insights Bővítmény az Azure Web Apps az új szolgáltatót használja. Az alkalmazás fájljában aappsettings.js *módosíthatja* a szűrési szabályokat.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>A Microsoft.Extensions.Logging.ApplicationInsights különálló csomagot használom, és engedélyezem a Application Insights builder **hívásával. AddApplicationInsights("ikey")**. Van lehetőség a rendszerkonfigurációs kulcs konfigurációból való lekértre?


Módosítsa a Program.cs appsettings.jsa következőképpen:

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

   Kapcsolódó szakasz `appsettings.json` innen:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Erre a kódra csak akkor van szükség, ha önálló naplózási szolgáltatót használ. A rendszeres Application Insights a rendszerkonfigurációs kulcsot a rendszer automatikusan betölti az *ApplicationInsights: InstrumentationKey konfigurációs útvonalról.* Appsettings.jsa következőnek kell lennie:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Az új **Azure-régiókhoz** kapcsolati sztringek használata szükséges a rendszerkulcsok helyett. [A kapcsolati](./sdk-connection-string.md?tabs=net) sztring azonosítja azt az erőforrást, amellyel társítani szeretné a telemetriai adatokat. Azt is lehetővé teszi, hogy módosítsa azokat a végpontokat, amelyekre az erőforrás a telemetria célhelyeként fog használni. Ki kell másolnia a kapcsolati sztringet, és hozzá kell adni az alkalmazás kódjához vagy egy környezeti változóhoz.

## <a name="next-steps"></a>Következő lépések

További információk:

* [Bejelentkezés a ASP.NET Core-ban](/aspnet/core/fundamentals/logging)
* [.NET nyomkövetési naplók a Application Insights](./asp-net-trace-logs.md)

