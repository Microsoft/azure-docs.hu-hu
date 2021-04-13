---
title: Telemetria mintavételezés az Azure Application Insightsban | Microsoft Docs
description: A telemetria mennyiségének megtartása a vezérlés alatt.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: a03dab43c12b372fc52e7516821fe7aef22d2e16
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305242"
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel

A mintavétel az [Azure Application Insights](./app-insights-overview.md)egyik funkciója. A telemetria-forgalom, az adatforgalmi költségek és a tárolási költségek csökkentése ajánlott módszer, miközben az alkalmazásadatok statisztikailag helyes elemzését is megőrzi. A mintavétel emellett segít elkerülni Application Insights a telemetria szabályozását. A mintavételezési szűrő kiválasztja a kapcsolódó elemeket, így az elemek között lehet navigálni, amikor diagnosztikai vizsgálatokat végez.

Ha a mérőszámok a portálon jelennek meg, akkor a rendszer a mintavétel során újranormalizálja őket. Ezzel a módszerrel csökkentheti a statisztikára gyakorolt hatást.

## <a name="brief-summary"></a>Rövid összefoglalás

* Három különböző típusú mintavétel létezik: adaptív mintavételezés, rögzített sebességű mintavételezés és betöltési mintavételezés.
* Az adaptív mintavételezés alapértelmezés szerint engedélyezve van a Application Insights ASP.NET és ASP.NET Core szoftverfejlesztői készletek (SDK-k) legújabb verzióiban. [Azure functions](../../azure-functions/functions-overview.md)is használja.
* A rögzített méretű mintavételezés a ASP.NET, a ASP.NET Core, a Java (az ügynök és az SDK), valamint a Python Application Insights SDK-k legújabb verzióiban érhető el.
* A betöltési mintavételezés a Application Insights szolgáltatási végponton működik. Ez csak akkor érvényes, ha más mintavételezés nem érvényes. Ha az SDK mintákat vesz fel a telemetria, a betöltési mintavételezés le van tiltva.
* Webalkalmazások esetén, ha egyéni eseményeket naplóz, és meg kell győződnie arról, hogy az események egy csoportjának megőrzése vagy elvetése együtt történik, az eseményeknek azonos `OperationId` értékkel kell rendelkezniük.
* Ha elemzési lekérdezéseket ír, [vegye figyelembe a mintavételezést](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). Különösen a rekordok számbavétele helyett használja a parancsot `summarize sum(itemCount)` .
* Egyes telemetria-típusok, beleértve a teljesítménymutatókat és az egyéni metrikákat, mindig attól függetlenül tartanak, hogy engedélyezve van-e a mintavételezés.

A következő táblázat összefoglalja az egyes SDK-típusokhoz és az alkalmazások típusaihoz elérhető mintavételi típusokat:

| Application Insights SDK | Adaptív mintavételezés támogatott | Rögzített arányú mintavételezés támogatott | A betöltési mintavételezés támogatott |
|-|-|-|-|
| ASP.NET | [Igen (alapértelmezés szerint)](#configuring-adaptive-sampling-for-aspnet-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-applications) | Csak akkor, ha nincs más mintavételezés |
| ASP.NET Core | [Igen (alapértelmezés szerint)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Csak akkor, ha nincs más mintavételezés |
| Azure Functions | [Igen (alapértelmezés szerint)](#configuring-adaptive-sampling-for-azure-functions) | No | Csak akkor, ha nincs más mintavételezés |
| Java | No | [Igen](#configuring-fixed-rate-sampling-for-java-applications) | Csak akkor, ha nincs más mintavételezés |
| Node.JS | No | [Igen](./nodejs.md#sampling) | Csak akkor, ha nincs más mintavételezés
| Python | No | [Igen](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Csak akkor, ha nincs más mintavételezés |
| Minden más | Nem | Nem | [Igen](#ingestion-sampling) |

> [!NOTE]
> A lap nagy részén található információk a Application Insights SDK-k aktuális verzióira vonatkoznak. Az SDK-k régebbi verzióival kapcsolatos információkért [tekintse meg az alábbi szakaszt](#older-sdk-versions).

## <a name="types-of-sampling"></a>Mintavételi típusok

Három különböző mintavételi módszer létezik:

* Az **adaptív mintavételezés** automatikusan elvégzi az SDK-ból a ASP.net/ASP.net Core-alkalmazásban eljuttatott telemetria mennyiségét, valamint a Azure functions. Ez az alapértelmezett mintavételezés a ASP.NET vagy a ASP.NET Core SDK használatakor. Az adaptív mintavételezés jelenleg csak a ASP.NET kiszolgálóoldali telemetria és Azure Functions esetén érhető el.

* A **rögzített arányú mintavételezés** csökkenti a ASP.NET vagy ASP.net Core vagy Java-kiszolgálóról és a felhasználói böngészőkből eljuttatott telemetria mennyiségét. Beállítja a díjszabást. Az ügyfél és a kiszolgáló szinkronizálja a mintavételezést, hogy a keresés során navigáljon a kapcsolódó nézetek és kérések között.

* A betöltési **mintavételezés** a Application Insights szolgáltatási végponton történik. A rendszer elveti az alkalmazásból érkező egyes telemetria a beállított mintavételi sebességgel. Nem csökkenti az alkalmazásból eljuttatott telemetria forgalmat, de segít megőrizni a havi kvótán belül. A betöltési mintavételezés legfőbb előnye, hogy az alkalmazás újbóli üzembe helyezése nélkül állíthatja be a mintavételezési sebességet. A betöltési mintavételezés egységesen működik az összes kiszolgáló és ügyfél esetében, de nem alkalmazható, ha bármilyen más típusú mintavétel működik.

> [!IMPORTANT]
> Ha az adaptív vagy rögzített arányú mintavételi módszerek engedélyezve vannak a telemetria, a betöltési mintavételezés le van tiltva az adott telemetria. Az SDK-szinten a mintavételezésből kizárt telemetria-típusok azonban továbbra is a portálon beállított díjszabás szerint lesznek betöltési mintavételezések.

## <a name="adaptive-sampling"></a>Adaptív mintavételezés

Az adaptív mintavételezés hatással van a webkiszolgáló-alkalmazásból a Application Insights szolgáltatási végpontra elküldett telemetria mennyiségére.

> [!TIP]
> Az adaptív mintavételezés alapértelmezés szerint engedélyezve van a ASP.NET SDK vagy a ASP.NET Core SDK használatakor, és alapértelmezés szerint engedélyezve van a Azure Functions számára is.

A kötetet a rendszer automatikusan módosítja, hogy a megadott maximális forgalmon belül maradjon, és a beállítással legyen vezérelve `MaxTelemetryItemsPerSecond` . Ha az alkalmazás alacsony telemetria (például hibakeresés vagy alacsony kihasználtság miatt) hoz létre, akkor a mintavételi processzor nem távolítja el az elemeket, amíg a kötet lejjebb van `MaxTelemetryItemsPerSecond` . Ahogy a telemetria mennyisége növekszik, a mintavételezési sebességet úgy kell beállítani, hogy a cél kötetet lehessen elérni. A rendszer rendszeres időközönként újraszámítja a beállítást, és a kimenő átviteli sebesség mozgóátlagán alapul.

A cél kötet eléréséhez a létrehozott telemetria némelyikét elveti a rendszer. Más típusú mintavételezéshez hasonlóan az algoritmus is megőrzi a kapcsolódó telemetria elemeket. Ha például a keresés során megkeresi a telemetria, az adott kivételhez kapcsolódó kérést is megtalálhatja.

A metrikák számát, például a kérelmek arányát és a kivételek arányát úgy kell beállítani, hogy kompenzálni lehessen a mintavételezési sebességet, hogy a mérőszám-kezelőben megközelítőleg helyes értékeket mutassanak.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET-alkalmazásokhoz

> [!NOTE]
> Ez a szakasz a ASP.NET alkalmazásokra vonatkozik, nem ASP.NET Core alkalmazásokra. [További információ az adaptív mintavételezés konfigurálásáról ASP.NET Core alkalmazásokhoz a jelen dokumentum későbbi részében.](#configuring-adaptive-sampling-for-aspnet-core-applications)

A-ben [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) számos paramétert módosíthat a `AdaptiveSamplingTelemetryProcessor` csomópontban. A megjelenített számok az alapértelmezett értékek:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Az adaptív algoritmus által az **egyes kiszolgálók gazdagépén** összegyűjtött [logikai műveletek](./correlation.md#data-model-for-telemetry-correlation) céljának aránya. Ha a webalkalmazás sok gazdagépen fut, csökkentse ezt az értéket úgy, hogy a Application Insights portálon maradjon a forgalmi arányon belül.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    A telemetria aktuális sebességének újraértékelésének időköze. A kiértékelést mozgóátlagként kell végrehajtani. Előfordulhat, hogy le kívánja rövidíteni ezt az intervallumot, ha a telemetria a hirtelen törtek.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    A mintavételezés százalékos értékének megváltozásakor a kevesebb adatmennyiséget a mintavételezési százalékos arány csökkentése érdekében?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    A mintavételezés százalékos értékének megváltozása esetén milyen hamar megnövelheti a mintavételi százalékot, hogy további adatmennyiséget rögzítsen?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mivel a mintavételi százalék változó, mi a minimális érték, amelyet be lehet állítani?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mivel a mintavételi százalék változó, mi az a maximális érték, amelyet be lehet állítani?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgóátlag kiszámításakor ez határozza meg azt a súlyozást, amelyet a legutóbbi értékhez kell rendelni. 1 értékkel egyenlő vagy annál kisebb értéket használjon. A kisebb értékek miatt az algoritmus kevésbé lesz újraaktiválva a hirtelen változásokkal.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Az alkalmazás első indításakor telemetria mennyiség. A hibakeresés során ne csökkentse ezt az értéket.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    A mintavételezés alá nem vonható típusok pontosvesszővel tagolt listája. A felismert típusok a következők:,,, `Dependency` `Event` `Exception` `PageView` , `Request` , `Trace` . A rendszer továbbítja a megadott típusok összes telemetria. a nem megadott típusok mintavételezésre kerülnek.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Egy pontosvesszővel tagolt lista, amelynek a mintavételezését el szeretné végezni. A felismert típusok a következők:,,, `Dependency` `Event` `Exception` `PageView` , `Request` , `Trace` . A megadott típusok mintavételezésre kerülnek; a többi típus összes telemetria mindig továbbítva lesz.

Az adaptív mintavételezés **kikapcsolásához** távolítsa el a `AdaptiveSamplingTelemetryProcessor` csomópont (oka) t a (z) rendszerből `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatíva: adaptív mintavételezés konfigurálása a kódban

A mintavételi paraméternek a fájlban való beállítása helyett `.config` programozott módon állíthatja be ezeket az értékeket.

1. Távolítsa el az összes `AdaptiveSamplingTelemetryProcessor` csomópontot a `.config` fájlból.
2. Az adaptív mintavételezés konfigurálásához használja a következő kódrészletet:

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

    ([További információ a telemetria processzorokról](./api-filtering-sampling.md#filtering).)

Az egyes telemetria-típusok mintavételi sebességét egyenként is beállíthatja, vagy kizárhat bizonyos típusokat is a mintavételből:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Az adaptív mintavételezés konfigurálása ASP.NET Core alkalmazásokhoz

`ApplicationInsights.config`ASP.net Core alkalmazások esetében nem, így az összes konfiguráció kód használatával történik.
Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az összes ASP.NET Core alkalmazáshoz. Letilthatja vagy testreszabhatja a mintavételezési viselkedést.

#### <a name="turning-off-adaptive-sampling"></a>Az adaptív mintavételezés kikapcsolása

Az alapértelmezett mintavételi funkció letiltható a Application Insights szolgáltatás hozzáadásakor a metódusban a `ConfigureServices` `ApplicationInsightsServiceOptions` `Startup.cs` fájlon belül:

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

A fenti kód letiltja az adaptív mintavételezést. Kövesse az alábbi lépéseket a mintavételezés további testreszabási lehetőségekkel való hozzáadásához.

#### <a name="configure-sampling-settings"></a>Mintavételezési beállítások konfigurálása

A `TelemetryProcessorChainBuilder` mintavételezési viselkedés testreszabásához használja az alább látható bővítmény-metódusokat.

> [!IMPORTANT]
> Ha ezt a módszert használja a mintavételezés konfigurálásához, ügyeljen arra, hogy a `aiOptions.EnableAdaptiveSampling` tulajdonságot a `false` híváskor állítsa be `AddApplicationInsightsTelemetry()` . A módosítás elvégzése után kövesse az alábbi kódrészlet utasításait **pontosan** ahhoz, hogy ismét engedélyezze az adaptív mintavételezést a testreszabott testreszabásokkal. Ha ezt elmulasztja, akkor az adatfeldolgozás felesleges. Mindig tesztelje a mintavételezés utáni beállításokat, és állítson be egy megfelelő [napi adatkorlátot](pricing.md#set-the-daily-cap) , amely segít a költségek szabályozásában.

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Az adaptív mintavételezés konfigurálása Azure Functionshoz

Az ezen a [lapon](../../azure-functions/configure-monitoring.md#configure-sampling) található utasításokat követve konfigurálhatja a Azure functions futó alkalmazások adaptív mintavételezését.

## <a name="fixed-rate-sampling"></a>Rögzített arányú mintavételezés

A rögzített arányú mintavételezés csökkenti a webkiszolgálóról és a webböngészőkből eljuttatott forgalmat. Az adaptív mintavételezéstől eltérően az Ön által meghatározott rögzített arányban csökkenti a telemetria. A ASP.NET, a ASP.NET Core, a Java és a Python alkalmazások esetében rögzített méretű mintavételezés érhető el.

Más mintavételi technikákhoz hasonlóan a kapcsolódó elemek is megmaradnak. Emellett szinkronizálja az ügyfél és a kiszolgáló mintavételezését is, hogy a kapcsolódó elemek megmaradjanak – például amikor megtekinti a keresési oldal nézetét, megkeresheti a kapcsolódó kiszolgálói kéréseket. 

Metrikaböngésző esetében a kérelmek és a kivételek számát a rendszer a mintavételezési sebesség kompenzálására szolgáló tényezővel szorozza meg, így azok körülbelül helyesek.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Rögzített arányú mintavételezés konfigurálása ASP.NET-alkalmazásokhoz

1. **Adaptív mintavételezés letiltása**: a (z [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) ), eltávolítása vagy Megjegyzés a `AdaptiveSamplingTelemetryProcessor` csomóponthoz.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Engedélyezze a rögzített sebességű mintavételi modult.** Adja hozzá ezt a kódrészletet a következőhöz [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Másik lehetőségként a mintavételi paraméter a fájlban való beállítása helyett `ApplicationInsights.config` programozott módon állíthatja be ezeket az értékeket:

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

    ([További információ a telemetria processzorokról](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Rögzített arányú mintavételezés konfigurálása ASP.NET Core alkalmazásokhoz

1. Az **adaptív mintavételezés letiltása**: a metódusban végrehajtott módosítások a következő használatával hajthatók végre `ConfigureServices` `ApplicationInsightsServiceOptions` :

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

2. **Engedélyezze a rögzített sebességű mintavételi modult.** A módosításokat az `Configure` alábbi kódrészletben látható módon lehet elvégezni:

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

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Rögzített arányú mintavételezés konfigurálása Java-alkalmazásokhoz

Alapértelmezés szerint nem engedélyezett a mintavétel a Java-ügynökben és az SDK-ban. Jelenleg csak a rögzített arányú mintavételezést támogatja. Az adaptív mintavételezés nem támogatott a javában.

#### <a name="configuring-java-agent"></a>Java-ügynök konfigurálása

1. A [applicationinsights-Agent-3.0.0-preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) letöltése

1. A mintavételezés engedélyezéséhez adja hozzá a következőt a `applicationinsights.json` fájlhoz:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>A Java SDK konfigurálása

1. Töltse le és konfigurálja a webalkalmazást a legújabb [Application Insights Java SDK](./java-get-started.md)-val.

2. A **rögzített arányú mintavételi modul engedélyezéséhez** adja hozzá a következő kódrészletet a `ApplicationInsights.xml` fájlhoz:

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

3. A címkén belül a következő címkék használatával kiválaszthatja vagy kizárhatja a mintavételezés bizonyos típusait a telemetria `Processor` `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

A mintavételből befoglalható vagy kizárható telemetria-típusok a következők:,,,, `Dependency` `Event` `Exception` `PageView` `Request` és `Trace` .

> [!NOTE]
> A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám.  A mintavétel jelenleg nem támogatja a többi értéket.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Rögzített arányú mintavételezés konfigurálása a OpenCensus Python-alkalmazásokhoz

Az alkalmazást a legújabb [OpenCensus Azure monitor-exportőrökkel](./opencensus-python.md)alakíthatja ki.

> [!NOTE]
> A metrikai exportőrök nem vehetik igénybe a rögzített arányú mintavételezést. Ez azt jelenti, hogy az egyéni metrikák az egyetlen olyan telemetria, ahol a mintavételezés nem konfigurálható. A metrikák exportőre a nyomon követett összes telemetria elküldi.

#### <a name="fixed-rate-sampling-for-tracing"></a>Rögzített sebességű mintavételezés a nyomkövetéshez ####
Megadhat egy `sampler` elemet is a(z) `Tracer` konfigurációja részeként. Ha nincs megadva explicit sampler, a `ProbabilitySampler` rendszer alapértelmezés szerint a értéket fogja használni. Alapértelmezés szerint a `ProbabilitySampler` 1/10000-as sebességet fogja használni, ami azt jelenti, hogy a rendszer minden 10000-kérelemből egyet küld Application Insights. A továbbiakban megtudhatja, hogyan adhat meg mintavételezési frekvenciát.

A mintavételezési sebesség megadásához győződjön meg róla, hogy a `Tracer` mintavételezési sebesség 0,0 és 1,0 közötti mintavételi sebességgel van megadva. A 1,0-es mintavételi sebesség a 100%-ot jelöli, ami azt jelenti, hogy az összes kérést a rendszer telemetria fogja elküldeni Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Rögzített arányú mintavételezés a naplókhoz ####
`AzureLogHandler`A nem kötelező argumentum módosításával konfigurálhatja a rögzített arányú mintavételezést `logging_sampling_rate` . Ha nem ad meg argumentumot, a rendszer 1,0 mintavételezési sebességet fog használni. A 1,0-es mintavételi sebesség a 100%-ot jelöli, ami azt jelenti, hogy az összes kérést a rendszer telemetria fogja elküldeni Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Rögzített sebességű mintavételezés konfigurálása weblapokhoz JavaScript-sel

A JavaScript-alapú weblapok a Application Insights használatára konfigurálhatók. A rendszer elküldi a telemetria a felhasználó böngészőjében futó ügyfélalkalmazástól, és a lapok bármely kiszolgálóról futtathatók.

Ha [Application Insights JavaScript-alapú weblapjait konfigurálja](javascript.md), módosítsa a Application Insights portálról beolvasott JavaScript-kódrészletet.

> [!TIP]
> A JavaScript részét képező ASP.NET-alkalmazásokban a kódrészlet általában bekerül `_Layout.cshtml` .

Szúrjon be egy sort, például a kialakítási `samplingPercentage: 10,` kulcs előtt:

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

A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám. A mintavétel jelenleg nem támogatja a többi értéket.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Kiszolgálóoldali és ügyféloldali mintavétel koordinálása

Az ügyféloldali JavaScript SDK a kiszolgálóoldali SDK-val együtt a rögzített arányú mintavételezésben is részt vesz. Az átalakított lapok csak ugyanarról a felhasználótól küldik el az ügyféloldali telemetria, amelyhez a kiszolgálóoldali SDK a mintavételezésbe belefoglalja a döntést. Ez a logika úgy van kialakítva, hogy fenntartsa a felhasználói munkamenetek integritását az ügyfél és a kiszolgálóoldali alkalmazások között. Ennek eredményeképpen a Application Insights bármely konkrét telemetria megtalálhatja a felhasználóhoz vagy a munkamenethez tartozó összes többi telemetria, és a kereséshez a kapcsolódó nézetek és kérések között lehet navigálni.

Ha az ügyfél és a kiszolgálóoldali telemetria nem jeleníti meg a koordinált mintákat:

* Ellenőrizze, hogy a kiszolgálón és az ügyfélen is engedélyezve van-e a mintavételezés.
* Győződjön meg arról, hogy ugyanazt a mintavételezési százalékot állítja be az ügyfél és a kiszolgáló között.
* Győződjön meg arról, hogy az SDK verziószáma 2,0 vagy újabb.

## <a name="ingestion-sampling"></a>Betöltési mintavételezés

A betöltési mintavételezés azon a ponton működik, ahol a webkiszolgálóról, böngészőkből és eszközökből származó telemetria eléri a Application Insights szolgáltatási végpontot. Bár ez nem csökkenti az alkalmazásból eljuttatott telemetria-forgalmat, csökkenti a feldolgozott és a megőrzött és a Application Insights által felszámított összeget.

Akkor használja ezt a mintavételezési típust, ha az alkalmazás gyakran túllépi a havi kvótát, és nem használhatja az SDK-alapú mintavételezési típusokat sem. 

Állítsa be a mintavételezési sebességet a használati és becsült költségek oldalon:

![Az alkalmazás áttekintés paneljén kattintson a beállítások, kvóta, minták, majd a mintavételi sebesség lehetőségre, és kattintson a frissítés elemre.](./media/sampling/data-sampling.png)

Más típusú mintavételezéshez hasonlóan az algoritmus is megőrzi a kapcsolódó telemetria-elemeket. Ha például a keresés során megkeresi a telemetria, az adott kivételhez kapcsolódó kérést is megtalálhatja. A metrikák számát, például a kérelmek arányát és a kivételek arányát megfelelően megőrzi a rendszer.

A mintavételezéssel elvetett adatpontok nem érhetők el semmilyen Application Insights funkcióban, például a [folyamatos exportálásban](./export-telemetry.md).

A betöltési mintavételezés nem működik, miközben az adaptív vagy a rögzített arányú mintavétel működik. Az adaptív mintavétel alapértelmezés szerint engedélyezve van, ha a ASP.NET SDK vagy a ASP.NET Core SDK használatban van, vagy ha a Application Insights engedélyezve van [Azure app Service ](azure-web-apps.md) vagy Állapotmonitor használatával. Ha a telemetria a Application Insights szolgáltatás végpontja fogadja, akkor megvizsgálja a telemetria, és ha a mintavételezési sebesség 100%-nál kisebb (amely azt jelzi, hogy a telemetria mintavételes), akkor a rendszer figyelmen kívül hagyja a beállított betöltési mintavételezési sebességet.

> [!WARNING]
> A portál csempén megjelenő érték jelzi a betöltési mintavételezéshez beállított értéket. Nem felel meg a tényleges mintavételezési aránynak, ha az SDK-mintavétel (adaptív vagy rögzített arányú mintavétel) folyamatban van.

## <a name="when-to-use-sampling&quot;></a>Mikor kell használni a mintavételezést

Általánosságban elmondható, hogy a legtöbb kis-és közepes méretű alkalmazáshoz nem kell mintavételezést végeznie. A leghasznosabb diagnosztikai információk és a legpontosabb statisztikák a felhasználói tevékenységekre vonatkozó adatgyűjtéssel szerezhetők be. 

A mintavétel fő előnyei a következők:

* Application Insights a szolgáltatás elveszíti az adatpontokat (&quot;szabályozások"), amikor az alkalmazás nagyon nagy telemetria rövid idő alatt küldi el. A mintavétel csökkenti annak a valószínűségét, hogy az alkalmazás a szabályozást fogja látni.
* Az adatpontok [kvótáján](pricing.md) belül megtarthatja az árképzési szintet. 
* A telemetria-gyűjtemény hálózati forgalmának csökkentése érdekében. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételezést érdemes használni?

**A betöltési mintavételezés használata, ha:**

* Gyakran a telemetria havi kvótáját használja.
* Túl sok telemetria kap a felhasználók webböngészőjéből.
* Az SDK olyan verzióját használja, amely nem támogatja a mintavételezést – például a 2 ASP.NET korábbi verziók esetében.

**A rögzített arányú mintavételezés használata:**

* Szinkronizált mintavételezést kíván az ügyfél és a kiszolgáló között, így ha a [Keresés](./diagnostic-search.md)során eseményeket vizsgál, az ügyfél és a kiszolgáló kapcsolódó eseményei között, például az oldalletöltések és a HTTP-kérelmek között is megtekintheti az adatokat.
* Biztos abban, hogy az alkalmazáshoz tartozó mintavételi százalék megfelelő. Elég magasnak kell lennie ahhoz, hogy pontos mérőszámok legyenek, de a díjszabása nem haladhatja meg az árképzési kvótát és a szabályozási korlátot.

**Adaptív mintavételezés használata:**

Ha a mintavételezés más formáinak használatára vonatkozó feltételek nem érvényesek, javasoljuk az adaptív mintavételezést. Ez a beállítás alapértelmezés szerint engedélyezve van a ASP.NET/ASP.NET Core SDK-ban. Nem csökkenti a forgalmat, amíg el nem éri egy bizonyos minimális értéket, ezért az alacsony használatú helyeket valószínűleg nem lehet mintavételezéssel elszámolni.

## <a name="knowing-whether-sampling-is-in-operation"></a>Annak ismerete, hogy a mintavétel működik-e

Ha a tényleges mintavételezési sebességet szeretné felderíteni, függetlenül attól, hogy hol alkalmazták, használjon egy [elemzési lekérdezést](../logs/log-query-overview.md) , például a következőt:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Ha úgy látja, hogy `RetainedPercentage` bármely típusnál kisebb, mint 100, akkor az adott típusú telemetria mintavételezése folyamatban van.

> [!IMPORTANT]
> Application Insights nem a munkamenetet, a metrikákat (beleértve az egyéni metrikákat) vagy a teljesítményszámláló telemetria-típusait a mintavételi módszerek valamelyikében. Ezeket a típusokat a rendszer mindig kizárja a mintavételezésből, mivel a pontosság csökkentése igen nemkívánatos lehet a telemetria-típusok esetében.

## <a name="how-sampling-works"></a>A mintavétel működése

A mintavételi algoritmus eldönti, hogy mely telemetria-elemeket kell eldobni, és melyeket kell megőrizni. Ez igaz, hogy az SDK vagy a Application Insights szolgáltatás végzi-e a mintavételt. A mintavételi döntés több olyan szabályon alapul, amely az összes egymással összefüggő adatpont megőrzését célozza, és a diagnosztikai élmény fenntartása a Application Insights, amely akár csökkentett adatkészlettel is végrehajtható és megbízható. Ha például az alkalmazás egy, a mintában található sikertelen kérést tartalmaz, a rendszer megőrzi a további telemetria elemeket (például a kérelemben naplózott kivételeket és nyomkövetési adatokat). A mintavétel megtartja vagy eldobja őket. Ennek eredményeképpen, amikor megtekinti a kérelem részleteit a Application Insightsban, mindig láthatja a kérést a hozzá tartozó telemetria-elemekkel együtt.

A mintavételi döntés a kérelem műveleti AZONOSÍTÓján alapul, ami azt jelenti, hogy egy adott művelethez tartozó összes telemetria-elem megmarad vagy el van dobva. Azon telemetria elemek esetében, amelyek nem rendelkeznek műveleti AZONOSÍTÓval (például a HTTP-környezet nélküli aszinkron szálakból jelentett telemetria-elemek), a mintavétel egyszerűen rögzíti az egyes típusú telemetria elemek százalékos arányát.

Ha visszaküldi a telemetria, a Application Insights szolgáltatás a gyűjtemény időpontjában használt mintavételi százalékkal módosítja a mérőszámokat, hogy kompenzálja a hiányzó adatpontokat. Ezért, amikor a Application Insights telemetria tekinti meg, a felhasználók statisztikailag helyes közelítéseket látnak, amelyek nagyon közel vannak a valós számokhoz.

A közelítés pontossága nagyrészt a beállított mintavételi százaléktól függ. Emellett a pontosság a sok felhasználótól származó, általában hasonló kérelmek nagy mennyiségét kezelő alkalmazások esetében is növekszik. Másfelől azonban a jelentős terheléssel nem rendelkező alkalmazások esetében nincs szükség mintavételezésre, mivel ezek az alkalmazások általában a kvótán belül is elküldhetik az összes telemetria, anélkül, hogy adatvesztést okozna a szabályozás. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Mi az alapértelmezett mintavételi viselkedés a ASP.NET és a ASP.NET Core SDK-k között?*

* Ha a fenti SDK legújabb verzióinak egyikét használja, az adaptív mintavétel alapértelmezés szerint öt telemetria elemmel van engedélyezve.
  Alapértelmezés szerint két `AdaptiveSamplingTelemetryProcessor` csomópont van hozzáadva, és az egyik tartalmazza a `Event` mintavételezési típust, míg a másik a `Event` mintavételből kizárja a típust. Ez a konfiguráció azt jelenti, hogy az SDK megpróbálja a telemetria-elemeket öt telemetria-elemre korlátozni `Event` , és öt telemetria-elemet egyesíteni az összes többi típustól, így biztosítva, hogy a `Events` rendszer a többi telemetria-típustól külön mintát vesz. Az eseményeket általában üzleti telemetria használják, és a legvalószínűbb, hogy a diagnosztikai telemetria kötetek nem érinthetik.
  
  A következő ábrán a `ApplicationInsights.config` generált alapértelmezett fájl látható. A ASP.NET Coreban ugyanaz az alapértelmezett viselkedés van engedélyezve a kódban. A [lap korábbi részében szereplő példákkal](#configuring-adaptive-sampling-for-aspnet-core-applications) módosíthatja ezt az alapértelmezett viselkedést.

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

*Egynél többször is telemetria a mintavétel?*

* Nem. A SamplingTelemetryProcessors figyelmen kívül hagyja a mintavételezési megfontolások elemeit, ha az elem már mintavétel alatt áll. Ugyanez érvényes a betöltési mintavételezésre is, amely nem alkalmazza a mintavételezést az SDK-ban már megvizsgált elemekre.

*Miért nem egy egyszerű "az egyes telemetria-típusok X százalékának összegyűjtése"?*

* Habár ez a mintavételezési módszer magas szintű pontosságot biztosít a mérőszámok közelítésében, a felhasználó, a munkamenetek és a kérések esetében a diagnosztikai adatok összekapcsolhatók, ami kritikus fontosságú a diagnosztika szempontjából. Ezért a mintavételezés jobban működik olyan házirendekkel, mint például a "az alkalmazások felhasználóinak X százalékához tartozó telemetria elemek összegyűjtése", vagy "az összes telemetria összegyűjtése az alkalmazási kérelmek X százalékához". A kérésekhez nem társított telemetria-elemek (például a háttérben futó aszinkron feldolgozás) esetében a tartalék az összes elem X százalékának összegyűjtése az egyes telemetria-típusokhoz. 

*Az idő múlásával a mintavételezés százalékos változása is megváltozik?*

* Igen, az adaptív mintavételezés fokozatosan megváltoztatja a mintavételezési százalékot a telemetria aktuálisan megfigyelt mennyisége alapján.

*Ha fix sebességű mintavételezést használok, Honnan tudhatom, hogy melyik mintavételi százalék fog működni a legmegfelelőbben az alkalmazáshoz?*

* Az egyik módszer az, hogy az adaptív mintavételezéssel kezdődjön, milyen arányban kell megállapítania (lásd a fenti kérdést), majd váltson a rögzített arányú mintavételezésre az adott díjszabás használatával. 
  
    Ellenkező esetben meg kell találnia. Elemezze az aktuális telemetria-használatot Application Insightsban, figyelje meg az esetlegesen előforduló szabályozást, és becsülje meg az összegyűjtött telemetria mennyiségét. Ez a három bemenet a kiválasztott díjszabási csomaggal együtt arra utal, hogy mennyit érdemes csökkenteni az összegyűjtött telemetria mennyiségét. Előfordulhat azonban, hogy a felhasználók számának növekedése vagy a telemetria mennyiségének más eltolódása érvényteleníti a becslést.

*Mi történik, ha úgy konfigurálja a mintavételi százalékot, hogy túl alacsony legyen?*

* A túlságosan alacsony mintavételezési százalékarányok túlzottan agresszív mintavételezést okoznak, és csökkentik a közelítések pontosságát, amikor Application Insights megkísérli kompenzálni az adatok vizualizációját az adatmennyiség csökkentése érdekében. Az is előfordulhat, hogy a diagnosztikai élmény negatív hatással van, mivel előfordulhat, hogy a ritkán fellépő vagy lassú kérelmek közül néhányat nem lehet kivenni.

*Mi történik, ha úgy konfigurálja a mintavételi százalékot, hogy túl magas legyen?*

* Ha túl magasra állítja a mintavételezési százalékot (nem elég agresszív), a nem megfelelő mértékben csökkenti az összegyűjtött telemetria mennyiségét. Továbbra is tapasztalhatja a szabályozással kapcsolatos telemetria adatvesztést, és a Application Insights használatának költségei magasabbak lehetnek, mint a túlhasználatos költségek miatt.

*Milyen platformokon használhatok mintavételezést?*

* Ha az SDK nem végez mintavételezést, a betöltési mintavételezés automatikusan megtörténhet egy adott köteten felüli bármely telemetria esetében. Ez a konfiguráció például akkor fog működni, ha a ASP.NET SDK vagy a Java SDK egy régebbi verzióját használja.
* Ha az aktuális ASP.NET vagy ASP.NET Core SDK-kat használja (az Azure-ban vagy a saját kiszolgálón), akkor alapértelmezés szerint az adaptív mintavételezést is igénybe veheti, de a fent leírtak szerint válthat a rögzített arányra. A rögzített arányú mintavételezéssel a böngésző SDK automatikusan szinkronizál a mintavételsel kapcsolatos eseményekkel. 
* Ha a jelenlegi Java-ügynököt használja, beállíthatja `applicationinsights.json` (Java SDK-hoz, konfiguráláshoz `ApplicationInsights.xml` ) a rögzített arányú mintavételezés bekapcsolásához. A mintavétel alapértelmezés szerint ki van kapcsolva. A rögzített arányú mintavételezéssel a böngésző SDK és a kiszolgáló automatikusan szinkronizál a mintavételezéssel kapcsolatos eseményekkel.

*Bizonyos ritkán előforduló események mindig látni szeretnék. Hogyan szerezhetem be a mintavételi modult a múltban?*

* Ennek a legjobb módja az, ha egy egyéni [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)ír, amely a 100-et a `SamplingPercentage` megőrizni kívánt telemetria-elemre állítja be az alább látható módon. Mivel az inicializálók garantáltan a telemetria processzorok (beleértve a mintavételezést is) előtt futnak, ez biztosítja, hogy az összes mintavételi módszer figyelmen kívül hagyja ezt az elemet a mintavételezési megfontolások alapján. Az egyéni telemetria inicializálók a ASP.NET SDK-ban, a ASP.NET Core SDK-ban, a JavaScript SDK-ban és a Java SDK-ban érhetők el. Beállíthat például egy telemetria-inicializálást a ASP.NET SDK használatával:

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

Az adaptív mintavételezés Application Insights a ASP.NET v 2.0.0-beta3 és újabb, a Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0 – béta és újabb verziókban érhető el, és alapértelmezés szerint engedélyezve van.

A fix sebességű mintavételezés az SDK egyik funkciója, amely a 2.0.0 és a Java SDK 2.0.1-es és újabb verzióiban elérhető ASP.NET-verziókban érhető el.

A v 2.5.0 előtt – a ASP.NET SDK Beta2, valamint a ASP.NET Core SDK-hoz készült v 2.2.0-beta3 a mintavételi döntés alapjául a felhasználói azonosító kivonata a "user" (azaz a leggyakoribb webalkalmazások) definiáló alkalmazásokhoz. Azon alkalmazások típusai esetében, amelyek nem határoznak meg felhasználókat (például webszolgáltatásokat), a mintavételi döntés a kérelem műveleti AZONOSÍTÓján alapul. A ASP.NET és ASP.NET Core SDK-k legújabb verziói a mintavételi döntés műveleti AZONOSÍTÓját használják.

## <a name="next-steps"></a>Következő lépések

* A [szűréssel](./api-filtering-sampling.md) szigorúbban VEZÉRELHETI az SDK által küldött adatokat.
* Olvassa el a fejlesztői hálózat című cikket a [telemetria optimalizálása Application Insightsával](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

