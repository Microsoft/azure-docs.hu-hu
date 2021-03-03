---
title: Get-Metric Azure Monitor Application Insights
description: Ismerje meg, hogy a GetMetric () hívásával hogyan rögzíthet helyileg előre összevont mérőszámokat a .NET-és .NET Core-alkalmazásokhoz Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719780"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Egyéni metrika-gyűjtemény a .NET-ben és a .NET Core-ban

Az Azure Monitor Application Insights .NET-és .NET Core SDK-k két különböző módszerrel gyűjthetik össze az egyéni metrikákat, `TrackMetric()` és `GetMetric()` . A két módszer közötti fő különbség a helyi összesítés. `TrackMetric()` hiányzik az előzetes összesítés `GetMetric()` , miközben az összesítése már megtörtént. Az ajánlott módszer az Összesítés használata, ezért már `TrackMetric()` nem az egyéni metrikák gyűjtésének előnyben részesített módja. Ez a cikk végigvezeti a GetMetric () metódus használatával, valamint a működésének indoklásával.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric versus GetMetric

`TrackMetric()` egy mérőszámot jelölő nyers telemetria küld. Nem hatékony egyetlen telemetria-elem küldése minden értékhez. `TrackMetric()` a teljesítmény szempontjából nem hatékony, mivel minden `TrackMetric(item)` a telemetria inicializálók és processzorok teljes SDK-folyamatán keresztül halad. `TrackMetric()`A-től eltérően `GetMetric()` a a helyi összesítést kezeli, és ezt követően csak egy perc rögzített intervallumában küldi el az összesített összefoglaló metrikát. Tehát ha a második vagy akár az ezredmásodperc szintjén is figyelnie kell néhány egyéni metrikát, akkor a tárolási és hálózati forgalmi költségek csak percenkénti figyeléssel járnak. Ez jelentősen csökkenti a szabályozás kockázatát is, mivel az összesített metrika számára küldendő telemetria elemek teljes száma jelentősen csökken.

Application Insights a-n keresztül gyűjtött egyéni metrikák `TrackMetric()` `GetMetric()` nem tartoznak a [mintavételezésbe](./sampling.md). A mintavétel fontos mérőszámai olyan forgatókönyvekhez vezethetnek, amelyekben előfordulhat, hogy a metrikák körére épülő riasztások megbízhatatlanok lehetnek. Az egyéni mérőszámok soha nem mintavételezésével általában biztos lehet abban, hogy a riasztási küszöbértékek megszegése esetén a riasztás tüzet fog okozni.  Mivel azonban az egyéni metrikák nem mintául szolgálnak, néhány lehetséges probléma van.

Ha másodpercenként egy metrika trendeket kell követnie, vagy egy még részletesebb intervallumban, ez a következőket eredményezheti:

- Megnövekedett adattárolási költségek. A Azure Monitor számára elküldött adatmennyiséggel kapcsolatos díj. (Minél több adattal küldi el a figyelési költségeket.)
- Megnövekedett hálózati forgalom/teljesítmény terhelése. (Bizonyos helyzetekben ez a pénzügyi és az alkalmazások teljesítményével is járhat.)
- A betöltési szabályozás kockázata. (A Azure Monitor szolgáltatás adatpontokat veszít ("szabályozás"), ha az alkalmazás nagyon nagy telemetria rövid idő alatt küldi el.)

A szabályozás különösen fontos a mintavétel során, mivel a szabályozás nem fogadott riasztásokat eredményezhet, mert a riasztás kiváltásának feltétele helyileg, majd a betöltési végponton, a túl sok adat elküldése miatt eldobásra kerül. A .NET és a .NET Core esetében miért nem javasoljuk a használatát, `TrackMetric()` hacsak nem implementálta a saját helyi összesítési logikáját. Ha az összes példányt nyomon szeretné követni egy adott időszakra vonatkozóan, előfordulhat, hogy ez [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) jobb illeszkedést biztosít. Habár ne feledje, hogy az egyéni metrikákkal ellentétben az egyéni események mintavételezése is megtörténik. Természetesen továbbra is használhatja a `TrackMetric()` saját helyi összesítésének megírása nélkül, de ha így tesz, vegye figyelembe a buktatókat.

Az összefoglalás az `GetMetric()` ajánlott megközelítés, mivel az előzetes összesítést végzi, az összes Track () hívás értékeit összesíti, és percenként egyszer küld egy összegzést/összesítést. Ez jelentősen csökkentheti a költségek és a teljesítmény terhelését azáltal, hogy kevesebb adatpontot küld el, miközben továbbra is összegyűjti az összes releváns információt.

> [!NOTE]
> Csak a .NET-és .NET Core SDK-k rendelkeznek GetMetric () metódussal. Ha Java-t használ, használhat [mikrométer mérőszámokat](./micrometer-java.md) vagy `TrackMetric()` . A JavaScript és a Node.js továbbra is használhatja `TrackMetric()` , de tartsa szem előtt az előző szakaszban leírt kikötéseket. Python esetén a [OpenCensus. stats](./opencensus-python.md#metrics) használatával egyéni metrikákat küldhet, de a metrikák implementációja eltérő.

## <a name="getting-started-with-getmetric"></a>A GetMetric első lépései

Példánkban egy alapszintű .NET Core 3,1 Worker Service-alkalmazást fogunk használni. Ha pontosan szeretné replikálni a fenti példákkal használt tesztkörnyezetben, 1-6 kövesse a Application Insights [figyelése a Worker Service](./worker-service.md#net-core-30-worker-service-application) -ben című cikket, és vegye fel egy alapszintű feldolgozó szolgáltatás projekt-sablonba. Ezek a fogalmak minden olyan általános alkalmazásra érvényesek, ahol az SDK használható, beleértve a Web Apps és a konzol alkalmazásait is.

### <a name="sending-metrics"></a>Metrikák küldése

Cserélje le a fájl tartalmát a `worker.cs` következőre:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Ha a fenti kódot futtatja, és megtekinti a Visual Studio kimeneti ablakán keresztül küldött telemetria, vagy egy olyan eszközt, mint például a Telerik, akkor a ciklust többször is végrehajtja a rendszer, és a telemetria nem küldi el a rendszer, így a teszt a következőképpen néz ki 60: :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Ez az egyetlen telemetria-elem 41 különböző metrikai mérések összesítését jelöli. Mivel ugyanezt az értéket küldi újra és újra, egy 0-as szórás *(szórás)* van, amely megegyezik a *maximális (max)* és a *minimális (min)* értékkel. Az *Value* tulajdonság az összesített egyedi értékek összegét jelöli.

> [!NOTE]
> A GetMetric nem támogatja az utolsó érték (azaz a "mérőműszer") követését vagy a hisztogramok/eloszlások követését.

Ha megvizsgáljuk a Application Insights erőforrást a naplók (Analytics) szolgáltatásban, ez az egyéni telemetria-elem a következőképpen fog kinézni:

![Log Analytics lekérdezés nézet](./media/get-metric/log-analytics.png)

> [!NOTE]
> Míg a nyers telemetria-tétel nem tartalmazott explicit Sum tulajdonságot/mezőt a betöltés után, akkor létrehozunk egyet. Ebben az esetben mind a `value` , mind a `valueSum` tulajdonság ugyanazt a dolgot jelöli.

Az egyéni metrika telemetria a portál [_mérőszámok_](../essentials/metrics-charts.md) szakaszában is elérheti. [Naplózási és egyéni metrika](pre-aggregated-metrics-log-metrics.md)is. (Az alábbi képernyőképen a log-alapú példa látható.) ![Metrikák Explorer nézet](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Gyorsítótárazási metrika referenciája a nagy átviteli sebességű használathoz

Bizonyos esetekben nagyon gyakran figyelhetők meg a mérőszámok értékei. Például egy nagy átviteli sebességű szolgáltatás, amely 500-kérelmeket dolgoz fel/Másodszor, minden kérelem esetében 20 telemetria-metrikát szeretne kibocsátani. Ez azt jelenti, hogy a másodpercenkénti 10 000 érték nyomon követése. Ilyen nagy adatátviteli helyzetekben előfordulhat, hogy a felhasználóknak segítségre lehet szükségük az SDK-nak a keresések elkerülésével.

Ebben az esetben például a fenti példában a "ComputersSold" metrika esetében egy olyan leírót hajtottak végre, amely a 42 megfigyelt értéket követte. Ehelyett a leíró több nyomkövetési hívás esetén is gyorsítótárazható:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

A metrikai leíró gyorsítótárazása mellett a fenti példa a `Task.Delay` 50 ezredmásodpercet is csökkenti, hogy a hurok gyakrabban fusson a 772-es `TrackValue()` meghívások miatt.

## <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák

Az előző szakaszban szereplő példákban a nulla dimenziós metrikák láthatók. A metrikák többdimenziós is lehetnek. Jelenleg legfeljebb 10 dimenziót támogatunk.

 Íme egy példa egy egydimenziós metrika létrehozására:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Ha ezt a kódot legalább 60 másodpercig futtatja, három különböző telemetria-elemet fog elküldeni az Azure-ba, amelyek mindegyike az egyik három formai tényező összesítését jelképezi. A naplók (Analytics) nézetében a következő módon ellenőrizheti:

![A többdimenziós metrika log Analytics-nézete](./media/get-metric/log-analytics-multi-dimensional.png)

Valamint a metrikák Explorer felületén:

![Egyéni metrikák](./media/get-metric/custom-metrics.png)

Azonban észreveheti, hogy nem tudja felosztani a metrikát az új egyéni dimenzióval, vagy megtekintheti az egyéni dimenziót a metrikák nézettel:

![Támogatás felosztása](./media/get-metric/splitting-support.png)

A metrikus Explorer felhasználói felületén belüli többdimenziós metrikák alapértelmezés szerint nincsenek bekapcsolva Application Insights erőforrásokban.

### <a name="enable-multi-dimensional-metrics"></a>Többdimenziós mérőszámok engedélyezése

Ha Application Insights erőforráshoz szeretne többdimenziós mérőszámokat engedélyezni, válassza a **használat és becsült költségek**  >  **Egyéni metrikák**  >  **engedélyezése az egyéni metrika dimenziók** számára  >  **OK** lehetőséget. Erről további részleteket [itt](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)találhat.

Miután elvégezte ezt a módosítást, és új többdimenziós telemetria küld, a **felosztást is alkalmazhatja**.

> [!NOTE]
> A portálon csak az újonnan elküldett metrikák lesznek tárolva a szolgáltatásban.

![Felosztás alkalmazása](./media/get-metric/apply-splitting.png)

És tekintse meg a metrikák összesítéseit az egyes _FormFactor_ dimenziók esetében:

![Űrlap-tényezők](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>A MetricIdentifier használata, ha több mint három dimenzió van

Jelenleg 10 dimenzió támogatott, azonban a három dimenziónál nagyobb a következők használata `MetricIdentifier` :

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Egyéni metrika konfigurálása

Ha módosítani szeretné a metrika konfigurációját, ezt a metrika inicializálási helyén kell végrehajtania.

### <a name="special-dimension-names"></a>Speciális dimenziók nevei

A metrikák nem használják a telemetria-környezetét `TelemetryClient` , mert az állandóként elérhető speciális dimenziók nevei az `MetricDimensionNames` osztályban a legjobb megoldás a korlátozáshoz.

Az alábbi "speciális műveleti kérelem mérete" által elküldett metrika-összesítések **nem** rendelkeznek a `Context.Operation.Name` "speciális művelet" értékkel. Míg `TrackMetric()` vagy bármely más TrackXXX () helyesen van `OperationName` beállítva a "speciális művelet" értékre.

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

Ebben a körülmények között az `MetricDimensionNames` értékek megadásához használja a osztályban felsorolt speciális dimenziók nevét `TelemetryContext` .

Ha például a következő utasításból származó metrika-összesítést a rendszer a Application Insights Felhőbeli végpontra küldi, annak `Context.Operation.Name` adatmezője a "speciális művelet" értékre lesz állítva:

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

A speciális dimenzió értékei a rendszerbe másolódnak `TelemetryContext` , és nem lesznek "normál" dimenzióként használva. Ha a normál metrikai feltáráshoz is meg szeretné őrizni a műveleti dimenziót, létre kell hoznia egy külön dimenziót erre a célra:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Dimenzió-és idősorozat-korlát

 Annak megakadályozása érdekében, hogy a telemetria alrendszer véletlenül felhasználja az erőforrásokat, beállíthatja az adatsorozatok maximális számát metrikával. Az alapértelmezett határértékek száma nem haladhatja meg a 1000-as összes adatsorozatot, és nem haladhatja meg az 100-nál több különböző értéket.

 A dimenzió és az idősorozat-határértékek kontextusában azt használjuk, hogy `Metric.TrackValue(..)` megbizonyosodjon róla, hogy a korlátok megfigyelhetők. Ha a korlátok már nem teljesülnek, `Metric.TrackValue(..)` a "false" értéket ad vissza, és az érték nem lesz nyomon követve. Ellenkező esetben a rendszer "igaz" értéket ad vissza. Ez akkor hasznos, ha a metrika adatai felhasználói adatbevitelből származnak.

A `MetricConfiguration` konstruktor több lehetőséget is igénybe vehet, hogyan kezelheti a különböző adatsorozatokat a vonatkozó metrikán belül, valamint egy olyan osztály objektumát, `IMetricSeriesConfiguration` amely a metrika egyes sorainak összesítési viselkedését határozza meg:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` a metrika által tartalmazott adatsorozatok maximális száma. Ha elérte ezt a korlátot, a `TrackValue()` rendszer nem fogja követni a hívásokat.
* `valuesPerDimensionLimit` a dimenziók eltérő értékeinek megkorlátja hasonló módon.
* `restrictToUInt32Values` meghatározza, hogy a nem negatív egész értékeket kell-e követni.

Íme egy példa arra, hogyan lehet üzenetet küldeni, ha túllépi a felső korlátot:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Következő lépések

* [További ](./worker-service.md)információ a Worker Service-alkalmazások figyeléséről.
* További részletek a [naplózási és előre összesített metrikákkal](./pre-aggregated-metrics-log-metrics.md)kapcsolatban.
* [Metrika-kezelő](../essentials/metrics-getting-started.md)
* [ASP.net Core alkalmazások](asp-net-core.md) Application Insightsának engedélyezése
* Application Insights engedélyezése a ASP.NET- [alkalmazásokhoz](asp-net.md)
