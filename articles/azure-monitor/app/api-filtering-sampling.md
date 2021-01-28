---
title: Szűrés és előfeldolgozás a Application Insights SDK-ban | Microsoft Docs
description: Telemetria-processzorok és telemetria-inicializálók írása az SDK-hoz a telemetria a Application Insights portálra való elküldése előtt.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cb9159b98b219c6fb04beb7bbbaade64fc72a30b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927751"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Telemetria szűrése és előfeldolgozása az Application Insights SDK-ban

A Application Insights SDK beépülő moduljait úgy is megírhatja és konfigurálhatja, hogy a Application Insights szolgáltatásba való elküldése előtt testreszabja és feldolgozza a telemetria.

* A [mintavétel](sampling.md) csökkenti a telemetria mennyiségét anélkül, hogy befolyásolná a statisztikát. Összekapcsolja a kapcsolódó adatpontokat, így a probléma diagnosztizálásakor megtekintheti őket. A portálon a teljes számlálás megszorozza a mintavételezéssel.
* A telemetria processzorokkal történő szűréssel kiszűrheti a telemetria az SDK-ban, mielőtt elküldi a kiszolgálónak. Csökkentheti például a telemetria mennyiségét a robotoktól érkező kérések kizárásával. A szűrés a mintavételnél nagyobb alapszintű megközelítés a forgalom csökkentése érdekében. Így hatékonyabban vezérelheti a továbbított adatokat, de hatással lehet a statisztikára. Kiszűrheti például az összes sikeres kérést.
* A telemetria inicializálók az alkalmazásból eljuttatott bármely telemetria [hozzáadhatnak vagy módosíthatnak tulajdonságokat](#add-properties) , beleértve a standard modulok telemetria is. Hozzáadhat például számított értékeket vagy verziószámokat, amelyekkel szűrheti a portálon található adatok mennyiségét.
* [Az SDK API](./api-custom-events-metrics.md) egyéni események és metrikák küldésére szolgál.

Előkészületek:

* Telepítse a megfelelő SDK-t az alkalmazáshoz: [ASP.net](asp-net.md), [ASP.net Core](asp-net-core.md), [nem http/Worker for .net/.net Core](worker-service.md)vagy [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Szűrés

Ezzel a technikával közvetlenül szabályozhatja a telemetria-adatfolyamban foglalt vagy kizárni kívánt feladatokat. A szűréssel elvégezheti a telemetria-elemek küldését Application Insightsba. A szűrést mintavételezéssel vagy külön is használhatja.

A telemetria szűréséhez írja be a telemetria-processzort, és regisztrálja azt a következővel: `TelemetryConfiguration` . Minden telemetria a processzoron halad át. Dönthet úgy, hogy elhúzza a streamből, vagy a lánc következő processzorára adja. A telemetria a standard moduloktól, például a HTTP-kérelem gyűjtőtől és a függőségi gyűjtőtől, valamint a nyomon követett telemetria is beletartozik. Kiszűrheti például a telemetria érkező kéréseket a robotok vagy a sikeres függőségi hívások alapján.

> [!WARNING]
> Az SDK-ból a processzorok használatával elküldött telemetria szűrésével eldöntheti a portálon megjelenő statisztikákat, és megnehezíti a kapcsolódó elemek követését.
>
> Ehelyett érdemes [mintavételezést](./sampling.md)használni.
>
>

### <a name="create-a-telemetry-processor-c"></a>Telemetria processzor létrehozása (C#)

1. Szűrő létrehozásához implementálja a következőt: `ITelemetryProcessor` .

    A telemetria processzorok létrehoznak egy feldolgozási láncot. Egy telemetria-feldolgozó példányának létrehozásakor a lánc következő processzorára mutató hivatkozást kap. Ha egy telemetria adatpontot továbbítanak a folyamat metódusának, az a munkája után meghívja (vagy nem hívja meg) a lánc következő telemetria-processzorát.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Adja hozzá a processzort.

ASP.NET- **alkalmazások**

Szúrja be ezt a kódrészletet a ApplicationInsights.configba:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Karakterlánc-értékeket adhat át a. config fájlból úgy, hogy nyilvános nevesített tulajdonságokat biztosít az osztályban.

> [!WARNING]
> Ügyeljen arra, hogy a. config fájlban lévő típus neve és a hozzá tartozó tulajdonságok nevei megfeleljenek a kódban szereplő osztálynak és tulajdonságoknak. Ha a. config fájl nem létező típusra vagy tulajdonságra hivatkozik, előfordulhat, hogy az SDK beavatkozás nélkül nem tud telemetria küldeni.
>

Azt is megteheti, hogy a szűrőt a kódban inicializálja. Egy megfelelő inicializálási osztályban, például a AppStart a alkalmazásban `Global.asax.cs` Helyezze be a processzort a láncba:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

A pont után létrehozott telemetria-ügyfelek a processzorokat fogják használni.

ASP.NET **Core/Worker Service-alkalmazások**

> [!NOTE]
> `ApplicationInsights.config` `TelemetryConfiguration.Active` ASP.net Core alkalmazásokhoz vagy a Microsoft. ApplicationInsights. WorkerService SDK-val való használata esetén a processzor hozzáadása nem érvényes.

[ASP.net Core](asp-net-core.md#adding-telemetry-processors) vagy [WorkerService](worker-service.md#adding-telemetry-processors)használatával írt alkalmazások esetén az új telemetria-processzor hozzáadását a bővítmény metódusának használatával végezheti el `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` , ahogy az látható. Ezt a metódust az `ConfigureServices` osztály metódusa hívja meg `Startup.cs` .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Példa szűrők

#### <a name="synthetic-requests"></a>Szintetikus kérelmek

Kiszűri a botokat és a webes teszteket. Bár a Metrikaböngésző lehetővé teszi a szintetikus források kiszűrését, ez a beállítás csökkenti a forgalom és a betöltés méretét azáltal, hogy az SDK-ban szűri őket.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Sikertelen hitelesítés

Kiszűri a kérelmeket "401" válasszal.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>A gyors távoli függőségi hívások kiszűrése

Ha csak lassú hívásokat szeretne diagnosztizálni, a gyors szűrést is kiszűrheti.

> [!NOTE]
> Ez a szűrés a portálon megjelenő statisztikákat fogja feldönteni.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Függőségi problémák diagnosztizálása

[Ez a blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) leírja a függőségi problémák diagnosztizálására szolgáló projektet, ha automatikusan küldi el a rendszeres pingeléseket a függőségek számára.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-alapú webalkalmazások

**Szűrés a ITelemetryInitializer használatával**

1. Hozzon létre egy telemetria inicializáló visszahívási függvényt. A visszahívási függvény paraméterként veszi igénybe a `ITelemetryItem` feldolgozás alatt álló eseményt. A `false` visszahívási eredményből a telemetria elem kiszűrésére kerül vissza.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Adja hozzá a telemetria inicializáló visszahívását:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Tulajdonságok hozzáadása/módosítása: ITelemetryInitializer

A telemetria inicializálók segítségével további információkkal bővítheti a telemetria, vagy felülbírálhatja a szabványos telemetria-modulok által beállított telemetria-tulajdonságokat.

A webcsomagok Application Insights például a HTTP-kérelmekkel kapcsolatos telemetria gyűjti. Alapértelmezés szerint a >= 400 értékkel rendelkező kérelem sikertelenként van megjelölve. Ha azonban sikerrel szeretné kezelni a 400-et, megadhat egy telemetria inicializáló, amely beállítja a siker tulajdonságot.

Ha telemetria inicializáló ad meg, akkor a rendszer akkor hívja meg, ha a Track * () metódusok bármelyikét meghívja. Ide tartoznak `Track()` a standard telemetria-modulok által meghívott metódusok. Az egyezmény szerint ezek a modulok nem állítanak be olyan tulajdonságot, amelyet már beállított egy inicializáló. A telemetria inicializálók a telemetria processzorok hívása előtt hívhatók. Így az inicializálók által végzett dúsítások láthatók a processzorok számára.

**Az inicializálás megadása**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **alkalmazások: az inicializálás betöltése**

ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Azt is megteheti, hogy az inicializálást a kódban, például a Global.aspx.cs-ben hozza létre:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Tekintse meg [ezt a mintát](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

ASP.NET **Core/Worker Service-alkalmazások: az inicializálás betöltése**

> [!NOTE]
> `ApplicationInsights.config` `TelemetryConfiguration.Active` ASP.net Core-alkalmazásokhoz vagy a Microsoft. ApplicationInsights. WorkerService SDK-val való használathoz vagy nem érvényes inicializálást adhat hozzá.

A [ASP.net Core](asp-net-core.md#adding-telemetryinitializers) vagy [WorkerService](worker-service.md#adding-telemetryinitializers)használatával írt alkalmazások esetében az új telemetria inicializáló hozzáadásához vegye fel azt a függőség-befecskendező tárolóba, ahogy az látható. Ez a `Startup.ConfigureServices` metódusban történik.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript telemetria inicializálók
*JavaScript*

Közvetlenül a portálról kapott inicializálási kód után szúrjon be egy telemetria-inicializálást:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

A telemetria elemben elérhető nem egyéni tulajdonságok összegzését lásd: [Application Insights export adatmodell](./export-data-model.md).

Tetszőleges számú inicializáló hozzáadhat. Ezeket a rendszer a hozzáadásuk sorrendjében hívja meg.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python telemetria processzorok

A OpenCensus Pythonban található telemetria-processzorok egyszerűen visszahívási függvények, amelyek az exportálás előtt dolgozzák fel a telemetria. A visszahívási függvénynek el kell fogadnia egy [boríték](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) -adattípust paraméterként. Ha ki szeretné szűrni a telemetria exportálását, győződjön meg arról, hogy a visszahívási függvény visszatér `False` . Azure Monitor adattípusok sémája a [githubon](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)található borítékokban látható.

> [!NOTE]
> Módosíthatja `cloud_RoleName` a `ai.cloud.role` mező attribútumának módosításával `tags` .

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Tetszőleges számú processzort adhat hozzá. Ezeket a rendszer a hozzáadásuk sorrendjében hívja meg. Ha egy processzor kivételt vet fel, az nem befolyásolja a következő processzorokat.

### <a name="example-telemetryinitializers"></a>Példa TelemetryInitializers

#### <a name="add-a-custom-property"></a>Egyéni tulajdonság hozzáadása

A következő minta-inicializálás egy egyéni tulajdonságot hoz létre minden követett telemetria.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Felhőbeli szerepkör nevének hozzáadása

A következő minta-inicializálás a Felhőbeli szerepkör nevét állítja be minden követett telemetria.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Információk hozzáadása a HttpContext

Az alábbi minta-inicializálás beolvassa az adatokat a alkalmazásból [`HttpContext`](/aspnet/core/fundamentals/http-context) , és hozzáfűzi egy `RequestTelemetry` példányhoz. A `IHttpContextAccessor` automatikusan megadja a konstruktor függőségi befecskendezését.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor és ITelemetryInitializer

Mi a különbség a telemetria processzorok és a telemetria inicializálók között?

* Vannak átfedések abban, hogy mit tehet velük. Mindkettő felhasználható a telemetria tulajdonságainak hozzáadására vagy módosítására is, de azt javasoljuk, hogy erre a célra használja az inicializálást.
* A telemetria inicializálók mindig a telemetria processzorok előtt futnak.
* A telemetria inicializálók többször is meghívhatók. Az egyezmény szerint nem állítanak be olyan tulajdonságot, amely már be van állítva.
* A telemetria-processzorok lehetővé teszik a telemetria-elemek teljes cseréjét vagy elvetését.
* Minden regisztrált telemetria-inicializálás minden telemetria számára garantált. A telemetria processzorok esetében az SDK garantálja az első telemetria-processzor meghívását. Azt határozza meg, hogy a rendszer a többi processzort hívja-e meg, vagy sem, az előző telemetria processzorok.
* A telemetria inicializálók használatával gazdagíthatja a telemetria a további tulajdonságokkal, vagy felülbírálhatja a meglévőket. Telemetria-processzor használatával szűrheti ki a telemetria.

## <a name="troubleshoot-applicationinsightsconfig"></a>Hibakeresés ApplicationInsights.config

* Győződjön meg arról, hogy a teljes típus neve és a szerelvény neve helyes.
* Győződjön meg arról, hogy a applicationinsights.config fájl a kimeneti könyvtárban található, és tartalmazza a legutóbbi módosításokat.

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Az API áttekintése](./api-custom-events-metrics.md)
* [ASP.NET-hivatkozás](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK-kód 

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Következő lépések
* [Események és naplók keresése](./diagnostic-search.md)
* [mintavételi](./sampling.md)
* [Hibaelhárítás](../faq.md)

