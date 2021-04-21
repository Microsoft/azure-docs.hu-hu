---
title: Telemetriai mintavételezés az Azure Application Insights | Microsoft Docs
description: A telemetria mennyiségének szabályozása.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834749"
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel

A mintavételezés az [Azure Application Insights.](./app-insights-overview.md) Ez az ajánlott módszer a telemetriai forgalom, az adatköltségek és a tárolási költségek csökkentésére, miközben megőrzi az alkalmazásadatok statisztikailag helyes elemzését. A mintavételezés segít elkerülni a Application Insights a telemetria szabályozását. A mintavételezési szűrő kiválasztja a kapcsolódó elemeket, így a diagnosztikai vizsgálatok során navigálhat az elemek között.

Amikor a metrikák száma jelennek meg a portálon, a rendszer újranormalizálja őket, hogy figyelembe vegye a mintavételezést. Ez minimálisra csökkenti a statisztikákra gyakorolt hatást.

## <a name="brief-summary"></a>Rövid összefoglalás

* A mintavételezésnek három különböző típusa létezik: adaptív mintavételezés, rögzített sebességetű mintavételezés és mintavételezés.
* Az adaptív mintavételezés alapértelmezés szerint engedélyezve van a Application Insights ASP.NET és ASP.NET Core Software Development Kits (SDK-k) legújabb verzióiban. A következő által is [Azure Functions.](../../azure-functions/functions-overview.md)
* A rögzített sebességet használó mintavételezés a Application Insights SDK-k legújabb verzióiban érhető el a ASP.NET, ASP.NET Core, Java (az ügynök és az SDK) és a Python esetében.
* Az ingestion sampling a Application Insights szolgáltatásvégponton működik. Csak akkor érvényes, ha nincs más mintavételezés. Ha az SDK mintákat ad a telemetriához, a mintavételezés le van tiltva.
* Webalkalmazások esetén, ha egyéni eseményeket naplóz, és biztosítania kell, hogy az események egy halmazát együtt őrizze meg vagy vesse el, az eseményeknek ugyanazokkal az értékkel kell `OperationId` bírni.
* Ha Analytics-lekérdezéseket ír, vegye figyelembe a [mintavételezést.](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations) A rekordok egyszerű megszámlálása helyett használja a következőt: `summarize sum(itemCount)` .
* Bizonyos telemetriatípusok, így a teljesítménymetrikák és az egyéni metrikák is mindig meg vannak megőrizve, függetlenül attól, hogy a mintavételezés engedélyezve van-e.

Az alábbi táblázat összefoglalja az egyes SDK-khoz és alkalmazástípusokhoz elérhető mintavételezési típusokat:

| Application Insights SDK | Adaptív mintavételezés támogatása | Rögzített sebességet támogató mintavételezés | Támogatott bebevételi mintavételezés |
|-|-|-|-|
| ASP.NET | [Igen (alapértelmezés szerint be van kapcsolva)](#configuring-adaptive-sampling-for-aspnet-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-applications) | Csak akkor, ha nincs más mintavételezés érvényben |
| ASP.NET Core | [Igen (alapértelmezés szerint be van kapcsolva)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Csak akkor, ha nincs más mintavételezés érvényben |
| Azure Functions | [Igen (alapértelmezés szerint be van kapcsolva)](#configuring-adaptive-sampling-for-azure-functions) | No | Csak akkor, ha nincs más mintavételezés érvényben |
| Java | No | [Igen](#configuring-fixed-rate-sampling-for-java-applications) | Csak akkor, ha nincs más mintavételezés érvényben |
| Node.JS | No | [Igen](./nodejs.md#sampling) | Csak akkor, ha nincs más mintavételezés érvényben
| Python | No | [Igen](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Csak akkor, ha nincs más mintavételezés érvényben |
| Minden más | Nem | Nem | [Igen](#ingestion-sampling) |

> [!NOTE]
> A lap nagy része az Application Insights aktuális verzióira vonatkozik. Az SDK-k régebbi verzióival kapcsolatos [információkért lásd az alábbi szakaszt.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Mintavételezési típusok

Három különböző mintavételezési módszer létezik:

* **Az adaptív mintavételezés** automatikusan módosítja az SDK-ból a ASP.NET/ASP.NET Core-alkalmazásban és a Azure Functions. Ez az alapértelmezett mintavételezés a Core SDK ASP.NET ASP.NET használatakor. Az adaptív mintavételezés jelenleg csak ASP.NET kiszolgálóoldali telemetriákhoz és Azure Functions.

* **A rögzített sebességet** használó mintavételezés csökkenti a ASP.NET Core ASP.NET Vagy Java-kiszolgálóról, valamint a felhasználók böngészőiről küldött telemetria mennyiségét. Ön adja meg a sebességet. Az ügyfél és a kiszolgáló szinkronizálja a mintavételezést, így a Keresésben navigálhat a kapcsolódó lapnézetek és kérések között.

* **A mintavételezés a** szolgáltatásvégpont Application Insights történik. A beállított mintavételi sebességgel elveti az alkalmazásból érkező egyes telemetriai adatokat. Nem csökkenti az alkalmazásból küldött telemetriai forgalmat, de segít a havi kvótán belül tartani. A mintavételezés fő előnye, hogy az alkalmazás ismételt üzembeása nélkül állíthatja be a mintavételezési sebességet. A mintavételezés egységesen működik az összes kiszolgálón és ügyfélen, de nem alkalmazható más típusú mintavételezések esetén.

> [!IMPORTANT]
> Ha az adaptív vagy rögzített sebességet tartalmazó mintavételezési módszerek engedélyezve vannak egy telemetriatípushoz, akkor az adott telemetria esetén a mintavételezés le van tiltva. Az SDK szintjén történő mintavételezésből kizárt telemetriatípusok azonban továbbra is mintavételezést fognak végezni a portálon beállított sebességgel.

## <a name="adaptive-sampling"></a>Adaptív mintavételezés

Az adaptív mintavételezés hatással van a webkiszolgáló-alkalmazásból a Application Insights végpontra küldött telemetria mennyiségére.

> [!TIP]
> Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az ASP.NET SDK vagy a ASP.NET Core SDK használatakor, és alapértelmezés szerint engedélyezve van a Azure Functions.

A kötetet a rendszer automatikusan úgy állítja be, hogy a megadott maximális forgalmon belül maradjon, és a beállítással `MaxTelemetryItemsPerSecond` vezérelhető. Ha az alkalmazás alacsony telemetriát állít elő, például hibakereséskor vagy alacsony kihasználtság miatt, a mintavételező feldolgozó nem dob elemeket, amíg a kötet a alatt `MaxTelemetryItemsPerSecond` van. A telemetria mennyiségének növekedésével a mintavételezési sebességet úgy állítja be a rendszer, hogy elérje a célkötetet. A rendszer rendszeres időközönként újraszámja a módosítást, és a kimenő átviteli sebesség mozgó átlagán alapul.

A célkötet eléréséhez a létrehozott telemetria egy része el lesz vetve. Más mintavételezési típusokhoz azonban az algoritmus is megőrzi a kapcsolódó telemetriai elemeket. Ha például a telemetriát vizsgálja a Keresésben, megkeresheti az adott kivételhez kapcsolódó kérést.

A rendszer úgy módosítja a metrikák számát, mint a kérelmek és a kivételek száma, hogy kompenzálja a mintavételezési sebességet, hogy a Metrikák explorerben körülbelül megfelelő értékeket mutassanak.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET alkalmazásokhoz

> [!NOTE]
> Ez a szakasz a ASP.NET alkalmazásokra vonatkozik, nem ASP.NET Core-alkalmazásokra. [A dokumentum későbbi, Alapalkalmazások ASP.NET adaptív mintavételezésének konfigurálásával kapcsolatos tudnivalók.](#configuring-adaptive-sampling-for-aspnet-core-applications)

[`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md)A(z) -ban a csomópont számos paraméterét `AdaptiveSamplingTelemetryProcessor` módosíthatja. Az itt látható értékek az alapértelmezett értékek:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Az adaptív algoritmus [által](./correlation.md#data-model-for-telemetry-correlation) az egyes kiszolgálógazdákon összegyűjthető logikai műveletek **célaránya.** Ha a webalkalmazás számos gazdagépen fut, csökkentse ezt az értéket, hogy a célként megadott forgalomarányon belül maradjon a Application Insights portálon.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Az az időköz, amely alatt a telemetria aktuális sebességét újra kiértékeli a rendszer. Az értékelés mozgóátlagként történik. Érdemes lehet lerövidíteni ezt az időközt, ha a telemetria felelős a hirtelen hirtelen megnökkenéstől.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    A százalékos mintavételezés értékének megváltozása esetén milyen hamar lehet ismét csökkenteni a mintavételezési százalékos arányt kevesebb adat rögzítéséhez?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Ha a mintavétel százalékos értéke megváltozik, milyen hamar lehet ismét növelni a mintavételezési százalékot további adatok rögzítéséhez?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mivel a mintavételezési százalék változó, mi a minimálisan beállítható érték?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mivel a mintavételezési százalékos arány változó, mi a maximális beállítható érték?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgóátlag kiszámításakor ez határozza meg a legutóbbi értékhez hozzárendelni szükséges súlyt. Használjon 1-et vagy annál kisebb értéket. A kisebb értékek miatt az algoritmus kevésbé reagál a hirtelen változásokra.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    A minta telemetriai adatok mennyisége, amikor az alkalmazás éppen elindult. Ne csökkentse ezt az értéket a hibakeresés közben.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Azon típusok pontosvesszővel tagolt listája, amelyek esetében nem szeretné, hogy mintavételezésnek legyen kitéve. A felismert típusok a következőek: `Dependency` , , , , , , `Event` `Exception` `PageView` `Request` `Trace` . A rendszer a megadott típusú összes telemetriát továbbítja; A nem megadott típusok mintavétele meg lesz adva.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    A mintavételezéshez érintett típusok pontosvesszővel tagolt listája. A felismert típusok a következőek: `Dependency` , , , , , , `Event` `Exception` `PageView` `Request` `Trace` . A megadott típusok mintavétele a következő lesz: A többi telemetria minden esetben továbbítva lesz.

**Az adaptív mintavételezés** kikapcsolása előtt távolítsa el a `AdaptiveSamplingTelemetryProcessor` csomópont(okat) a `ApplicationInsights.config` ről.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatíva: Adaptív mintavételezés konfigurálása a kódban

Ahelyett, hogy beállíta a mintavételezési paramétert a fájlban, programozott módon is `.config` beállíthatja ezeket az értékeket.

1. Távolítsa el `AdaptiveSamplingTelemetryProcessor` az összes csomópontot a `.config` fájlból.
2. A következő kódrészlet használatával konfigurálhatja az adaptív mintavételezést:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Tudnivalók a telemetriai processzorokkal kapcsolatban.)](./api-filtering-sampling.md#filtering)

Az egyes telemetriatípusok mintavételi sebességét egyenként is módosíthatja, vagy akár kizárhat bizonyos típusokat a mintavételezésből:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET Core-alkalmazásokhoz

A `ApplicationInsights.config` Core-ASP.NET nincs, így minden konfiguráció kódon keresztül történik.
Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az összes ASP.NET Core-alkalmazáshoz. Letilthatja vagy testre szabhatja a mintavételezési viselkedést.

#### <a name="turning-off-adaptive-sampling"></a>Az adaptív mintavételezés kikapcsolása

Az alapértelmezett mintavételezési funkció letiltható a Application Insights hozzáadásakor a `ConfigureServices` metódusban, a `ApplicationInsightsServiceOptions` `Startup.cs` fájlban használva:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

A fenti kód letiltja az adaptív mintavételezést. A mintavételezés további testreszabási lehetőségekkel való hozzáadásához kövesse az alábbi lépéseket.

#### <a name="configure-sampling-settings"></a>Mintavételezési beállítások konfigurálása

A mintavételezési viselkedés testreszabásához használja a bővítmény metódusát az `TelemetryProcessorChainBuilder` alább látható módon.

> [!IMPORTANT]
> Ha ezzel a módszerrel konfigurálja a mintavételezést, a hívásakor ügyeljen arra, hogy a tulajdonságot `aiOptions.EnableAdaptiveSampling` `false` a következőre állítsa: `AddApplicationInsightsTelemetry()` . A módosítás után pontosan követnie kell az alábbi  kódblokk utasításait az adaptív mintavételezés a testreszabásokkal való újra engedélyezéséhez. Ha ezt nem így kell tenni, az felesleges adatbetörést eredményezhet. Mindig tesztelje a mintavételezési beállítások [](pricing.md#set-the-daily-cap) módosítása után, és állítsa be a megfelelő napi adatkorrekulátort a költségek szabályozása érdekében.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Adaptív mintavételezés konfigurálása Azure Functions

Az ezen az oldalon [található utasításokat követve](../../azure-functions/configure-monitoring.md#configure-sampling) konfigurálhatja az adaptív mintavételezést a Azure Functions.

## <a name="fixed-rate-sampling"></a>Rögzített sebességetű mintavételezés

A rögzített mintavételezés csökkenti a webkiszolgálóról és a webböngészőkből küldött forgalmat. Az adaptív mintavételezéstől eltérően az Ön által meghatározott sebességgel csökkenti a telemetriát. Rögzített sebességet használó mintavételezés érhető el ASP.NET, ASP.NET Core-, Java- és Python-alkalmazásokhoz.

Más mintavételezési technikákhoz hasonló ez is megőrzi a kapcsolódó elemeket. Az ügyfél és a kiszolgáló mintavételezését is szinkronizálja a kapcsolódó elemek megőrzése érdekében – például ha megtekint egy oldalnézetet a Keresésben, megkeresheti a kapcsolódó kiszolgálókéréseket. 

A Metrikaböngésző például a kérelmek és a kivételek száma szorozva van egy tényezővel a mintavételezési arány kompenzálására, hogy azok hozzávetőlegesen helyesek.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Rögzített sebességetű mintavételezés konfigurálása ASP.NET alkalmazásokhoz

1. **Adaptív mintavételezés letiltása:** A [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) fájlban távolítsa el vagy távolítsa el a csomópontot megjegyzésként. `AdaptiveSamplingTelemetryProcessor`

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Engedélyezze a rögzített sebességet mintavételező modult.** Adja hozzá ezt a kódrészletet a következő [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) hez:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Másik lehetőségként a mintavételezési paraméter fájlban történő beállítása helyett programozott módon is beállíthatja ezeket `ApplicationInsights.config` az értékeket:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Tudnivalók a telemetriai processzorokkal kapcsolatban.)](./api-filtering-sampling.md#filtering)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Rögzített sebességetű mintavételezés konfigurálása ASP.NET Core-alkalmazásokhoz

1. **Adaptív mintavételezés letiltása:** A metódusban a használatával `ConfigureServices` lehet módosításokat `ApplicationInsightsServiceOptions` végezni:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Engedélyezze a rögzített sebességet mintavételező modult.** A metódusban az alábbi kódrészletben látható módon lehet `Configure` módosításokat tenni:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Rögzített sebességet használó mintavételezés konfigurálása Java-alkalmazásokhoz

Alapértelmezés szerint nincs engedélyezve a mintavételezés a Java-ügynökben és az SDK-ban. Jelenleg csak a rögzített sebességet támogató mintavételezést támogatja. A Java nem támogatja az adaptív mintavételezést.

#### <a name="configuring-java-agent"></a>A Java-ügynök konfigurálása

1. Az [applicationinsights-agent-3.0.0-PREVIEW.5.jar letöltése](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. A mintavételezés engedélyezéséhez adja hozzá a következőket a `applicationinsights.json` fájlhoz:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>A Java SDK konfigurálása

1. Töltse le és konfigurálja a webalkalmazást a [legújabb Application Insights Java SDK-val.](./java-get-started.md)

2. **Engedélyezze a rögzített sebességet mintavételező modult** úgy, hogy hozzáadja a következő kódrészletet a `ApplicationInsights.xml` fájlhoz:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. A címke címkéiben az alábbi címkék használatával adhat hozzá vagy zárhat ki bizonyos telemetriai adatokat a `Processor` `FixedRateSamplingTelemetryProcessor` mintavételezésből:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

A mintavételezésből kizárható vagy kihagyható telemetriatípusok a következőek: `Dependency` , , , , és `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> A százalékos mintavételezéshez olyan százalékos arányt válasszon, amely közel van 100/N-hez, ahol az N egész szám.  A mintavételezés jelenleg nem támogat más értékeket.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Rögzített sebességetű mintavételezés konfigurálása OpenCensus Python-alkalmazásokhoz

Az alkalmazást a legújabb [OpenCensus-Azure Monitor meg.](./opencensus-python.md)

> [!NOTE]
> Rögzített sebességetű mintavételezés nem érhető el a metrikákhoz. Ez azt jelenti, hogy csak az egyéni metrikák a telemetria egyetlen típusa, ahol a mintavételezés NEM konfigurálható. A metrikák által kezelt összes telemetria elküldve lesz.

#### <a name="fixed-rate-sampling-for-tracing"></a>Rögzített sebességetű mintavételezés nyomkövetéshez ####
Megadhat egy `sampler` elemet is a(z) `Tracer` konfigurációja részeként. Ha nem ad meg explicit mintamintát, a rendszer alapértelmezés szerint a `ProbabilitySampler` értéket használja. A `ProbabilitySampler` alapértelmezés szerint 1/10000 arányt használ, ami azt jelenti, hogy minden 10000 kérelemből egy lesz elküldve a Application Insights. A továbbiakban megtudhatja, hogyan adhat meg mintavételezési frekvenciát.

A mintavételezési sebesség megadásához győződjön meg arról, hogy a 0,0 és 1,0 közötti mintavételezési sebességet tartalmazó `Tracer` mintavételezőt ad meg. Az 1,0-s mintavételezési arány 100%-ot jelent, ami azt jelenti, hogy minden kérés telemetriai adatként lesz elküldve a Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Rögzített sebességetű mintavételezés naplókhoz ####
A nem kötelező argumentumának módosításával konfigurálhatja a rögzített sebességet `AzureLogHandler` a `logging_sampling_rate` mintavételezését. Ha nincs megadva argumentum, a rendszer 1,0-s mintavételezési arányt használ. Az 1,0-s mintavételezési arány 100%-ot jelent, ami azt jelenti, hogy minden kérés telemetriai adatként lesz elküldve a Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Rögzített sebességet használó mintavételezés konfigurálása weblapok számára JavaScripttel

A JavaScript-alapú weblapok konfigurálhatóak a Application Insights. A telemetria a felhasználó böngészőjében futó ügyfélalkalmazásból lesz elküldve, és a lapok bármely kiszolgálóról üzemeltetve futnak.

Amikor [JavaScript-alapú](javascript.md)weblapokat konfigurál a Application Insights számára, módosítsa a javascript-kódrészletet, amely a Application Insights portálról.

> [!TIP]
> A ASP.NET JavaScripttel együtt, a kódrészlet általában a következőben található: `_Layout.cshtml` .

Szúrjon be egy sort, például `samplingPercentage: 10,` a eszközkulcs előtt:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

A százalékos mintavételezéshez olyan százalékot válasszon, amely közel van a 100/N értékhez, ahol az N egész szám. A mintavételezés jelenleg nem támogat más értékeket.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Kiszolgálóoldali és ügyféloldali mintavételezés koordinálása

Az ügyféloldali JavaScript SDK a kiszolgálóoldali SDK-val együtt rögzített sebességet használó mintavételezésben vesz részt. A rendszeroldali lapok csak attól a felhasználótól küldenek ügyféloldali telemetriát, amelyről a kiszolgálóoldali SDK úgy döntött, hogy szerepel a mintavételezésben. Ez a logika úgy lett kialakítva, hogy fenntartsa a felhasználói munkamenetek integritását az ügyfél- és kiszolgálóoldali alkalmazásokban. Ennek eredményeképpen a Application Insights bármely telemetriai elemében megtalálja a felhasználóhoz vagy munkamenethez tartozó összes többi telemetriai elemet, és a Keresés menüpontban navigálhat a kapcsolódó lapnézetek és kérések között.

Ha az ügyfél- és kiszolgálóoldali telemetria nem mutat koordinált mintákat:

* Ellenőrizze, hogy engedélyezte-e a mintavételezést a kiszolgálón és az ügyfélen is.
* Ellenőrizze, hogy az ügyfélen és a kiszolgálón is ugyanazt a százalékos mintavételezési arányt adja-e meg.
* Győződjön meg arról, hogy az SDK 2.0-s vagy újabb verziója van.

## <a name="ingestion-sampling"></a>Ingestion sampling (Mintavételezés be- és bebevétele

A betekintés mintavételezése ott működik, ahol a webkiszolgálóról, a böngészőkből és az eszközökről származó telemetria eléri Application Insights végpontot. Bár nem csökkenti az alkalmazásból küldött telemetriai forgalmat, de csökkenti a feldolgozott és megtartott (és felszámított) Application Insights.

Akkor használja ezt a mintavételezési típust, ha az alkalmazás gyakran túllépi a havi kvótáját, és nem használhatja egyik SDK-alapú mintavételezési típust sem. 

Állítsa be a mintavételezési sebességet a Használat és becsült költségek lapon:

![Az alkalmazás Áttekintés panelen kattintson a Beállítások, Kvóta, Minták elemre, válassza ki a mintavételezési sebességet, majd kattintson a Frissítés elemre.](./media/sampling/data-sampling.png)

Más mintavételezési típusokhoz hasonló az algoritmus a kapcsolódó telemetriai elemeket is megőrzi. Ha például a telemetriát vizsgálja a Keresésben, megkeresheti az adott kivételhez kapcsolódó kérést. A rendszer helyesen őrzi meg a metrikák számát, például a kérelmek és a kivételek számát.

A mintavételezéssel elvetett adatpontok nem érhetők el Application Insights szolgáltatásban, például a [Folyamatos exportálás funkcióban.](./export-telemetry.md)

Az adaptív vagy rögzített sebességetű mintavételezés közben a mintavételezés nem működik. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az ASP.NET SDK vagy a ASP.NET Core SDK használatakor, vagy ha az Application Insights engedélyezve van a [Azure App Service-ban ](azure-web-apps.md) vagy a Állapotmonitor. Amikor a Application Insights-szolgáltatásvégpont telemetriát kap, megvizsgálja a telemetriát, és ha a mintavételezési arány 100%-nál kisebb (ami azt jelzi, hogy a telemetria mintavételezése folyamatban van), akkor a rendszer figyelmen kívül hagyja a beállított mintavételi sebességet.

> [!WARNING]
> A portál csempén látható érték azt az értéket jelöli, amely a mintavételezéshez van beállítva. Nem a tényleges mintavételezési sebességet képviseli, ha bármilyen SDK-mintavételezés (adaptív vagy rögzített sebességetű mintavételezés) van működésben.

## <a name="when-to-use-sampling&quot;></a>Mikor kell mintavételezést használni?

A legtöbb kis és közepes méretű alkalmazás esetében általában nincs szükség mintavételezésre. A leghasznosabb diagnosztikai információk és a legpontosabb statisztikák az összes felhasználói tevékenységre vonatkozó adatok gyűjtésével szerezhetők be. 

A mintavételezés fő előnyei a következőek:

* Application Insights szolgáltatás eldobja (&quot;szabályozások") adatpontokat, amikor az alkalmazás nagyon nagy mennyiségű telemetriát küld rövid idő alatt. A mintavételezés csökkenti annak a valószínűségét, hogy az alkalmazás szabályozást fog látni.
* Hogy a [](pricing.md) tarifacsomag adatpontkvótája alatt maradjon. 
* A telemetria gyűjtéséből származó hálózati forgalom csökkentése. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételezést használjak?

**Akkor használjon mintavételezést, ha:**

* Gyakran használ havi telemetriai kvótát.
* Túl sok telemetriát kap a felhasználók webböngészőjéből.
* Az SDK egy olyan verzióját használja, amely nem támogatja a mintavételezést – például ASP.NET 2-esnél korábbi verziókat.

**Rögzített sebességetű mintavételezést akkor használjon, ha:**

* Szinkronizálni szeretné a mintavételezést az ügyfél és a [](./diagnostic-search.md)kiszolgáló között, hogy amikor a Keresésben vizsgálja az eseményeket, navigáljon az ügyfélen és a kiszolgálón a kapcsolódó események között, például a lapnézetek és a HTTP-kérések között.
* Biztos benne, hogy az alkalmazásnak megfelelő százalékos mintavételezési arányt kell alkalmaznia. A pontos metrikákhoz elég magasnak kell lennie, de a díjszabási kvótát és a szabályozási korlátokat meghaladó sebesség alatt kell lennie.

**Adaptív mintavételezés használata:**

Ha a mintavételezés egyéb formáinak használatára vonatkozó feltételek nem érvényesek, az adaptív mintavételezést javasoljuk. Ez a beállítás alapértelmezés szerint engedélyezve van a ASP.NET/ASP.NET Core SDK-ban. Nem csökkenti a forgalmat, amíg el nem ér egy bizonyos minimális sebességet, ezért az alacsony használatú helyek mintavétele valószínűleg egyáltalán nem történik meg.

## <a name="knowing-whether-sampling-is-in-operation"></a>Annak ismerete, hogy a mintavétel működik-e

A tényleges mintavételezési arány felderítéséhez az alkalmazás [](../logs/log-query-overview.md) helyének hely függetlenül használjon egy Analytics-lekérdezést, például a következőt:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Ha azt látja, hogy bármely típus esetében kisebb, mint 100, akkor a rendszer mintavétele az adott `RetainedPercentage` telemetriatípust jelenti.

> [!IMPORTANT]
> Application Insights mintavételezési technikák egyikében sem mintavételezik a munkamenetet, a metrikákat (beleértve az egyéni metrikákat) és a teljesítményszámlálók telemetriatípusait. Ezek a típusok mindig ki vannak zárva a mintavételezésből, mivel a pontosság csökkenése rendkívül nemkívánatos lehet ezekhez a telemetriatípusokhoz.

## <a name="how-sampling-works"></a>A mintavételezés működése

A mintavételezési algoritmus dönti el, hogy mely telemetriai elemeket kell eldobni, és amelyeket meg kell tartani. Ez attól függetlenül igaz, hogy a mintavételezést az SDK vagy a Application Insights végez. A mintavételezéssel kapcsolatos döntés több olyan szabályon alapul, amelyek célja az összes relált adatpont érintetlen maradása, olyan diagnosztikai élmény fenntartása a Application Insights-ban, amely még csökkentett adatkészlettel is kezelhető és megbízható. Ha például az alkalmazás egy sikertelen kérést tartalmaz egy mintában, a további telemetriai elemek (például a kérelemhez naplózott kivételek és nyomkövetések) megmaradnak. A mintavételezés vagy együtt tartja vagy eldobja őket. Ennek eredményeképpen, ha a kérelem részleteit a Application Insights, mindig láthatja a kérést a kapcsolódó telemetriai elemekkel együtt.

A mintavételezési döntés a kérés műveleti azonosítóján alapul, ami azt jelenti, hogy egy adott művelethez tartozó összes telemetriai elem megmarad vagy el lesz dobva. Azon telemetriaelemek esetén, amelyekhez nincs műveletazonosító beállítva (például HTTP-környezet nélkül az aszinkron szálakról jelentett telemetriaelemek), a mintavételezés egyszerűen az egyes típusú telemetriai elemek százalékos arányát rögzíti.

Amikor a telemetriát visszamutatja Önnek, a Application Insights szolgáltatás a hiányzó adatpontok kompenzálása érdekében a gyűjtési időpontban használt mintavételi százalékkal módosítja a metrikákat. Ezért a telemetriai adatok Application Insights a felhasználók statisztikailag helyes közelítéseket láthatnak, amelyek nagyon közel állnak a valós számokhoz.

A közelítés pontossága nagyban függ a konfigurált mintavételezési százaléktól. Emellett a pontosság is nő az olyan alkalmazások esetében, amelyek sok felhasználótól származó, nagy mennyiségű, általában hasonló kérést kezelnek. A jelentős terheléssel nem használható alkalmazások esetében azonban nincs szükség mintavételezésre, mivel ezek az alkalmazások általában a teljes telemetriai adatokat elküldik, miközben a kvótán belül maradnak anélkül, hogy adatvesztést okoznak a szabályozásban. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Mi az alapértelmezett mintavételezési viselkedés a ASP.NET és ASP.NET Core SDK-kban?*

* Ha a fenti SDK egyik legújabb verzióját használja, az adaptív mintavételezés alapértelmezés szerint engedélyezve van másodpercenként öt telemetriai elemet használva.
  Alapértelmezés szerint két csomópont van hozzáadva, amelyek közül az egyik tartalmazza a mintavételezés típusát, a másik pedig kizárja a típust a `AdaptiveSamplingTelemetryProcessor` `Event` `Event` mintavételezésből. Ez a konfiguráció azt jelenti, hogy az SDK megpróbálja öt telemetriai elemre korlátozni a típusú telemetriai elemeket, és öt egyéb típusú telemetriai elemet egyesítve biztosítja, hogy a mintavételezés a többi telemetriatípustól elkülönítve `Event` `Events` történik. Az eseményeket általában üzleti telemetriához használják, és valószínűleg nem befolyásolják a diagnosztikai telemetriakötetek.
  
  Az alábbiakban az alapértelmezett létrehozott `ApplicationInsights.config` fájl látható. A ASP.NET Core-ban ugyanez az alapértelmezett viselkedés van engedélyezve a kódban. Az alapértelmezett [viselkedést](#configuring-adaptive-sampling-for-aspnet-core-applications) az oldal korábbi szakaszában található példák segítségével módosíthatja.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*A telemetria mintavétele egynél több alkalommal is lehet?*

* Nem. A SamplingTelemetryProcessors figyelmen kívül hagyja a mintavételezési szempontokból származó elemeket, ha az elem már mintavételezésre van szükség. Ugyanez igaz a mintavételezésre is, amely nem alkalmaz mintavételezést azokra az elemekre, amelyek már mintavételezést tartalmaznak az SDK-ban.

*Miért nem mintavételez egyszerű "gyűjtse össze az egyes telemetriatípusokat X százalékban"?*

* Bár ez a mintavételezési módszer magas szintű pontosságot biztosít a metrikák közelítésében, a diagnosztikai adatok felhasználónként, munkamenetenként és kérésenként való korrelálása megszakítja a diagnosztikához kritikus fontosságú képességet. Ezért a mintavételezés jobban működik az olyan szabályzatokkal, mint az "alkalmazásfelhasználók X százalékának összes telemetriai elemének gyűjtése", vagy "az alkalmazáskérések X százalékának összes telemetriával kapcsolatos gyűjtése". A kérelmekhez nem társított telemetriai elemek (például a háttér aszinkron feldolgozás) esetén a tartalék az, hogy "az összes elem X százalékát gyűjti össze minden telemetriatípushoz". 

*Változhat a mintavétel százalékos aránya az idő során?*

* Igen, az adaptív mintavételezés fokozatosan módosítja a mintavétel százalékos arányát a telemetria aktuálisan megfigyelt mennyisége alapján.

*Ha rögzített sebességet használok, honnan tudható, hogy melyik százalékos mintavételezés fog a legjobban működni az alkalmazásom számára?*

* Az egyik módszer az adaptív mintavételezéssel kezdeni, annak kiderítésében, hogy milyen arányban rendezi az sebességet (lásd a fenti kérdést), majd átvált a rögzített sebességet használó mintavételezésre ezzel a sebességgel. 
  
    Ellenkező esetben meg kell találgatást. Elemezze az aktuális telemetriahasználatot a Application Insights, figyelje meg az esetlegesen bekövetkező szabályozásokat, és becsülje meg az összegyűjtött telemetria mennyiségét. Ez a három bemenet a kiválasztott tarifacsomaggal együtt javaslatot ad arra, hogy mennyivel szeretné csökkenteni a gyűjtött telemetria mennyiségét. A felhasználók számának növekedése vagy a telemetria mennyiségének egyéb növekedése azonban érvénytelenné teszi a becslést.

*Mi történik, ha a mintavétel százalékos arányát túl alacsonyra konfigurálom?*

* A túlzottan alacsony mintavételezési arányok túlzottan agresszív mintavételezést okoznak, és csökkentik a közelítések pontosságát Application Insights amikor megpróbálja kompenzálni az adatok vizualizációját az adatmennyiség csökkentése érdekében. A diagnosztikai élmény is negatív hatással lehet a folyamatra, mivel előfordulhat, hogy a rendszer néhány ritkán sikertelen vagy lassú kérést mintavételez ki.

*Mi történik, ha a mintavétel százalékos arányát túl magasra konfigurálom?*

* Ha túl magas mintavételezési arányt konfigurál (nem elég agresszív), az a gyűjtött telemetria mennyiségének nem megfelelő csökkenését váltja ki. Előfordulhat, hogy a telemetria szabályozással kapcsolatos adatvesztését tapasztalja, és a Application Insights használatának költsége magasabb lehet a tervezettnél a túlcsatolási díjak miatt.

*Milyen platformokon használhatok mintavételezést?*

* Ha az SDK nem végez mintavételezést, a rendszer automatikusan elvégzi a mintavételezést egy adott kötet feletti telemetria esetében. Ez a konfiguráció például akkor működik, ha a ASP.NET SDK vagy a Java SDK egy régebbi verzióját használja.
* Ha a jelenlegi ASP.NET vagy ASP.NET Core(Azure-ban vagy a saját kiszolgálón) üzemelő ASP.NET-t használja, alapértelmezés szerint adaptív mintavételezést kap, de a fent leírtak szerint átválthat rögzített díjszabásra. Rögzített sebességet tartalmazó mintavételezés esetén a böngésző SDK automatikusan szinkronizál a mintához kapcsolódó eseményekkel. 
* Ha az aktuális Java-ügynököt használja, konfigurálhatja a (Java SDK-hoz: ) a rögzített sebességet `applicationinsights.json` `ApplicationInsights.xml` használó mintavételezés bekapcsolához. A mintavételezés alapértelmezés szerint ki van kapcsolva. Rögzített sebességet tartalmazó mintavételezés esetén a böngésző SDK és a kiszolgáló automatikusan szinkronizál a mintával kapcsolatos eseményekre.

*Van néhány ritka esemény, amit mindig látni szeretnék. Hogyan jutnak el a mintavételezési modulon túlra?*

* Ennek legjobb módja egy egyéni [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)megírása, amely a elemet 100-ra állítja a megőrizni kívánt telemetriaelemen, ahogy az alább `SamplingPercentage` látható. Mivel az inicializálók garantáltan a telemetriafeldolgozók előtt futnak (beleértve a mintavételezést is), ez biztosítja, hogy minden mintavételezési technika figyelmen kívül hagyja ezt az elemet a mintavételezés során. Az egyéni telemetriainicializálók az ASP.NET SDK-ban, a ASP.NET Core SDK-ban, a JavaScript SDK-ban és a Java SDK-ban érhetők el. Konfigurálhat például egy telemetriai inicializálót az ASP.NET SDK használatával:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Régebbi SDK-verziók

Az adaptív mintavételezés az Application Insights ASP.NET ASP.NET 2.0.0-beta3-as és újabb, Microsoft.ApplicationInsights.AspNetCore SDK 2.2.0-beta1-es vagy újabb verziójához érhető el, és alapértelmezés szerint engedélyezve van.

A rögzített sebességet használó mintavételezés az SDK 2.0.0-ASP.NET és a Java SDK 2.0.1-es és újabb verzióinak egyik szolgáltatása.

Az ASP.NET SDK 2.5.0-beta2-es és a ASP.NET Core SDK 2.2.0-beta3-as verziója előtt a mintavételezési döntés a "felhasználót" meghatározó alkalmazások (azaz a leggyakoribb webalkalmazások) felhasználói azonosítójának kivonatán alapult. Azon alkalmazástípusok esetében, amelyek nem határoznak meg felhasználókat (például webszolgáltatásokat), a mintavételezési döntés a kérés műveleti azonosítójára alapult. A ASP.NET és ASP.NET legújabb verziói a műveleti azonosítót használják a mintavételezési döntéshez.

## <a name="next-steps"></a>Következő lépések

* [A szűrés](./api-filtering-sampling.md) szigorúbban szabályozhatja az SDK által küldött adatokat.
* Olvassa el a Telemetria optimalizálása a következővel: Fejlesztői [hálózat Application Insights.](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)

