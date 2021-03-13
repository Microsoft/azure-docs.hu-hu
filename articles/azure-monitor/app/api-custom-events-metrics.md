---
title: Application Insights API egyéni eseményekhez és mérőszámokhoz | Microsoft Docs
description: A használat és a problémák diagnosztizálásához helyezzen be néhány sornyi kódot az eszközön, az asztali alkalmazásban, a weblapon vagy a szolgáltatásban.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a3fa14dcd406f6372a7fb409b92d9db3e8404ca7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419031"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API egyéni eseményekhez és metrikákhoz

Szúrjon be néhány sornyi kódot az alkalmazásban, hogy megtudja, mit csinálnak a felhasználók, vagy segíthetnek a problémák diagnosztizálásában. Telemetria az eszköz-és asztali alkalmazásokból, a webes ügyfelekből és a webkiszolgálókról is küldhet. Az [Azure Application Insights](./app-insights-overview.md) Core telemetria API használatával egyéni eseményeket és mérőszámokat küldhet, valamint a szabványos telemetria saját verzióit. Ez az API ugyanazt az API-t használja, mint a standard Application Insights adatgyűjtők.

## <a name="api-summary"></a>API összefoglaló

Az alapszintű API minden platformon egységes, többek között a `GetMetric` (csak .net-es) változatokon kívül.

| Metódus | Alkalmazási cél |
| --- | --- |
| [`TrackPageView`](#page-views) |Lapok, képernyők, pengék vagy űrlapok. |
| [`TrackEvent`](#trackevent) |Felhasználói műveletek és egyéb események. A felhasználói viselkedés nyomon követésére vagy a teljesítmény figyelésére szolgál. |
| [`GetMetric`](#getmetric) |Nulla és többdimenziós mérőszámok, központilag konfigurált összesítés, csak C#. |
| [`TrackMetric`](#trackmetric) |A teljesítmény mérése, például a várólista hossza nem adott eseményekhez kapcsolódik. |
| [`TrackException`](#trackexception) |Diagnosztikai kivételek naplózása. Nyomon követheti a más eseményekhez kapcsolódó eseményeket, és megvizsgálhatja a verem nyomkövetéseit. |
| [`TrackRequest`](#trackrequest) |A kiszolgálói kérelmek gyakoriságának és időtartamának naplózása a teljesítmény elemzéséhez. |
| [`TrackTrace`](#tracktrace) |Erőforrás-diagnosztikai naplók üzenetei. A harmadik féltől származó naplókat is rögzítheti. |
| [`TrackDependency`](#trackdependency) |A külső összetevőkre irányuló hívások időtartamának és gyakoriságának naplózása, amelytől az alkalmazás függ. |

A telemetria-hívások többségéhez [tulajdonságokat és mérőszámokat is csatolhat](#properties) .

## <a name="before-you-start"></a><a name="prep"></a>Előkészületek

Ha még nincs hivatkozása Application Insights SDK-ra:

* Adja hozzá a Application Insights SDK-t a projekthez:

  * [ASP.NET projekt](./asp-net.md)
  * [ASP.NET Core projekt](./asp-net-core.md)
  * [Java-projekt](./java-get-started.md)
  * [Node.js projekt](./nodejs.md)
  * [JavaScript az egyes weblapokon](./javascript.md) 
* Az eszköz vagy a webkiszolgáló kódjában adja meg a következőt:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>TelemetryClient-példány beolvasása

Példány beszerzése `TelemetryClient` (kivéve a weblapokon található JavaScriptben):

A .NET/.NET Core-alkalmazások esetében [ASP.net Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) alkalmazások és [nem http/feldolgozók](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) esetében ajánlott a függőségi injektálási tároló egy példányának beszerzése a saját `TelemetryClient` dokumentációjában ismertetett módon.

Ha AzureFunctions v2 + vagy Azure WebJobs v3 +-t használ, kövesse a következő dokumentumot: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
A metódus elavult üzeneteinek megtekintéséhez látogasson el a [Microsoft/ApplicationInsights-DotNet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) webhelyre további részletekért.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

A TelemetryClient szál-biztonságos.

A ASP.NET és a Java-projektek esetében a bejövő HTTP-kérelmeket a rendszer automatikusan rögzíti. Előfordulhat, hogy a TelemetryClient további példányait is létre szeretné hozni az alkalmazás más moduljában. Előfordulhat például, hogy egy TelemetryClient-példánya van a middleware-osztályban az üzleti logikával kapcsolatos események jelentéséhez. A gép azonosításához olyan tulajdonságokat is beállíthat, mint például a UserId és a DeviceId. Ez az információ a példány által küldött összes eseményhez csatolva van.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Node.js-projektekben `new applicationInsights.TelemetryClient(instrumentationKey?)` létrehozhat egy új példányt, de ez csak olyan forgatókönyvek esetén javasolt, amelyek különálló konfigurációt igényelnek `defaultClient` .

## <a name="trackevent"></a>TrackEvent

Application Insights egy *egyéni esemény* egy olyan adatpont, amelyet [Metrikaböngésző](../essentials/metrics-charts.md) megadhat összesített számként, valamint a [diagnosztikai keresésben](./diagnostic-search.md) egyéni előfordulásként. (Nem kapcsolódik az MVC-hoz vagy más keretrendszer "eseményeihez".)

A `TrackEvent` különböző események számlálásához helyezzen be hívásokat a kódban. A felhasználók milyen gyakran választanak ki egy adott funkciót, milyen gyakran érnek el konkrét célokat, vagy hogy milyen gyakran használják bizonyos típusú hibákat.

Például egy játék alkalmazásban küldjön egy eseményt, amikor egy felhasználó megnyeri a játékot:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Egyéni események az Analyticsben

A telemetria a `customEvents` táblázat [Application Insights naplók lapján](../logs/log-query-overview.md) vagy a [használati élményben](usage-overview.md)érhető el. Az események származhatnak, `trackEvent(..)` vagy az [Analytics automatikus gyűjtésének beépülő moduljában](javascript-click-analytics-plugin.md)is előfordulhatnak.

 

Ha a [mintavételezés](./sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackEvent () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. Az egyéni események helyes számának megszerzéséhez ezért kódokat kell használnia, például: `customEvents | summarize sum(itemCount)` .

## <a name="getmetric"></a>GetMetric

Ha szeretné megtudni, hogyan használhatja a GetMetric () hívást, hogy a helyileg előre összevont metrikákat rögzítsen a .NET-és .NET Core-alkalmazásokhoz, látogasson el a [GetMetric](./get-metric.md) dokumentációra.

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> A Microsoft. ApplicationInsights. TelemetryClient. TrackMetric nem az előnyben részesített módszer a metrikák küldéséhez. A metrikákat mindig előre összesíteni kell a küldés előtt. Az egyik GetMetric (..) túlterheli az SDK-ra vonatkozó előzetes összesítési képességek eléréséhez szükséges metrikai objektumot. Ha saját előzetes összesítési logikát alkalmaz, akkor a TrackMetric () metódussal küldheti el az eredményül kapott összesítéseket. Ha az alkalmazás egy külön telemetria-elem küldését igényli minden alkalommal az idő összesítése nélkül, akkor valószínű, hogy az Event telemetria használati esete van; Lásd: TelemetryClient. TrackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

A Application Insights olyan metrikákat diagramokat is használhat, amelyek nincsenek az adott eseményekhez csatolva. Előfordulhat például, hogy rendszeres időközönként figyelheti a várólista hosszát. A metrikák esetében az egyes mérések kevésbé érdeklik, mint a változatok és a trendek, így a statisztikai diagramok hasznosak.

A metrikák Application Insightsre való küldéséhez használhatja az API-t `TrackMetric(..)` . Kétféle módon küldhet mérőszámot:

* Egyetlen érték. Minden alkalommal, amikor mérést végez az alkalmazásban, a megfelelő értéket kell elküldenie Application Insights. Tegyük fel például, hogy rendelkezik egy mérőszámmal, amely egy tárolóban lévő elemek számát mutatja. Egy adott időszakban először három elemet helyez el a tárolóba, majd két elemet távolít el. Ennek megfelelően kétszer is meghívja a következőt `TrackMetric` : az érték első átadása, `3` majd az érték `-2` . Application Insights mindkét értéket tárolja az Ön nevében.

* Összesítési. A metrikák használatakor az egyes mérések ritkán fordulnak elő. Ehelyett fontos összefoglalni, hogy mi történt egy adott időszakban. Egy ilyen összefoglaló neve _Összesítés_. A fenti példában az adott időszak összesített metrikai összege `1` és a metrika értékeinek száma `2` . Az összesítési módszer használatakor csak egyszer kell meghívnia `TrackMetric` az egyes időszakokat, és el kell küldenie az összesített értékeket. Ez az ajánlott módszer, mivel jelentősen csökkentheti a költségek és a teljesítmény terhelését azáltal, hogy kevesebb adatpontot küld Application Insightsra, miközben továbbra is összegyűjti az összes releváns információt.

### <a name="examples"></a>Példák

#### <a name="single-values"></a>Egyetlen érték

Egyetlen metrikai érték küldése:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Egyéni metrikák az Analyticsben

A telemetria `customMetrics` [Application Insights Analytics](../logs/log-query-overview.md)táblázatában érhető el. Az egyes sorok az alkalmazásban meghívást jelentenek `trackMetric(..)` .

* `valueSum` – Ez a mérések összege. A Mean értékének megszerzéséhez ossza el a következőt: `valueCount` .
* `valueCount` – Az ebben a hívásban összesített mérések száma `trackMetric(..)` .

## <a name="page-views"></a>Lapok nézetei

Egy eszköz vagy weblap alkalmazásban az oldal nézet telemetria alapértelmezés szerint a rendszer minden egyes képernyő vagy lap betöltésekor elküldi. Ezt azonban módosíthatja, ha további vagy eltérő időpontokban szeretné nyomon követni a lapok nézeteit. Ha például egy alkalmazásban lapokat vagy lapokat jelenít meg, érdemes nyomon követni egy oldalt, amikor a felhasználó új panelt nyit meg.

A felhasználói és munkamenet-adatküldés tulajdonságokként, valamint a lapok nézeteivel együtt történik, így a felhasználói és a munkamenet-diagramok életben lesznek, ha az oldal nézet telemetria.

### <a name="custom-page-views"></a>Egyéni lapok nézetei

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Ha több lap is található a különböző HTML-lapokon, megadhatja az URL-címet is:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Időzítési lapok nézetei

Alapértelmezés szerint a rendszer az **oldal nézet betöltési idejét** jeleníti meg, amikor a böngésző elküldi a kérést, amíg a böngésző lapja betöltési eseménye meg nem történik.

Ehelyett a következők közül választhat:

* Explicit időtartam beállítása a [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) -hívásban: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` .
* Használja az oldal nézet időzítési hívásait `startTrackPage` és `stopTrackPage` .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Az első paraméterként használt név a Start és a stop hívásokat társítja. Alapértelmezés szerint az aktuális oldal nevét adja meg.

Az eredményül kapott oldal betöltési időtartama a Metrikaböngésző a kezdő és a leállítási hívások közötti intervallumból származik. Ez a tényleges időintervallum.

### <a name="page-telemetry-in-analytics"></a>Az Analytics telemetria lapja

Az [Analytics](../logs/log-query-overview.md) két táblájában a böngésző műveleteiből származó adatok jelennek meg:

* A `pageViews` tábla az URL-címmel és a lap címével kapcsolatos információkat tartalmaz.
* A `browserTimings` tábla az ügyfél teljesítményével kapcsolatos információkat tartalmaz, például a bejövő adat feldolgozásához szükséges időt.

Annak megállapítása, hogy a böngésző mennyi ideig tart a különböző lapok feldolgozása:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

A különböző böngészők népszerűek észlelése:

```kusto
pageViews
| summarize count() by client_Browser
```

A lapok nézeteinek AJAX-hívásokhoz való társításához kapcsolódjon függőségekkel:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

A Server SDK a TrackRequest használatával naplózza a HTTP-kérelmeket.

Azt is meghívhatja, ha olyan kontextusban szeretné szimulálni a kérelmeket, amelyben nem fut a webszolgáltatási modul.

Azonban a kérelem küldésének ajánlott módja a telemetria, ahol a kérelem <a href="#operation-context">műveleti környezetként</a>működik.

## <a name="operation-context"></a>Műveleti környezet

A telemetria elemek összekapcsolhatók a műveleti környezettel való társítással. A standard szintű kérelem-követési modul ezt a kivételeket és a HTTP-kérelem feldolgozása közben elküldött egyéb eseményeket is végrehajtja. A [Search](./diagnostic-search.md) és az [Analytics szolgáltatásban](../logs/log-query-overview.md)a művelet azonosítójának használatával könnyedén megtalálhatja a kérelemhez társított eseményeket.

A korrelációval kapcsolatos további részletekért tekintse meg a [telemetria korrelációját Application Insightsban](./correlation.md) .

A telemetria manuális nyomon követéséhez a legegyszerűbb módszer a telemetria korrelációjának biztosítása a következő minta használatával:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

A műveleti környezet beállításával együtt `StartOperation` egy telemetria-elem jön létre a megadott típushoz. A művelet eldobásakor a telemetria-tételt küldi el, vagy ha explicit módon meghívja `StopOperation` . Ha `RequestTelemetry` telemetria-típusként használja, annak időtartama az Indítás és a Leállítás közötti időzített időközre van állítva.

A művelet hatókörén belül jelentett telemetria a művelet "Children" részévé válnak. A műveleti környezetek beágyazva lehetnek.

A Search (keresés) területen a **kapcsolódó elemek** listájának létrehozásához a műveleti környezet használható:

![Kapcsolódó elemek](./media/api-custom-events-metrics/21.png)

Az egyéni műveletek nyomon követésével kapcsolatos további információkért lásd: [Egyéni műveletek nyomon követése Application Insights .net SDK-val](./custom-operations-tracking.md) .

### <a name="requests-in-analytics"></a>Az elemzési kérelmek

[Application Insights elemzésekben](../logs/log-query-overview.md)a kérelmek megjelennek a `requests` táblázatban.

Ha a [mintavételezés](./sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket fog megjeleníteni. Például a itemCount = = 10 érték azt jelenti, hogy a trackRequest () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A kérelmek helyes számának és az átlagos időtartamnak a kérelmek nevei szerint szegmentált eléréséhez használja a következő kódot:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Kivételek küldése Application Insightsre:

* A probléma gyakoriságának megjelölése, hogy [megszámolja őket](../essentials/metrics-charts.md).
* Az [egyes előfordulások vizsgálatához](./diagnostic-search.md).

A jelentések tartalmazzák a verem nyomkövetését.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Az SDK-k sok kivételt kapnak automatikusan, így nem mindig explicit módon kell meghívnia a TrackException.

* ASP.NET: [kód írása a Catch kivételekhez](./asp-net-exceptions.md).
* Java EE: [a kivételek automatikusan kifoghatók](./java-get-started.md#exceptions-and-request-failures).
* JavaScript: a kivételek automatikusan kifoghatók. Ha le szeretné tiltani az automatikus gyűjtést, adjon hozzá egy sort a weblapokon beszúrt kódrészlethez:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Kivételek az Analyticsben

[Application Insights elemzésekben](../logs/log-query-overview.md)a kivételek megjelennek a `exceptions` táblázatban.

Ha a [mintavétel](./sampling.md) folyamatban van, a `itemCount` tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackException () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A kivételek típusával szegmentált kivételek helyes számának beszerzéséhez használja a következő kódot:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A fontos stack-információk nagy része már ki van választva különálló változóknak, de a szerkezet kihúzásával `details` További információkat érhet el. Mivel ez a struktúra dinamikus, az eredményt a várt típusra kell átadnia. Például:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Ha a kivételeket a kapcsolódó kérésekkel társítja, használjon illesztést:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

A TrackTrace segítségével diagnosztizálhatja a problémákat, ha a "morzsa Trail" üzenetet küld Application Insights. Elküldheti a diagnosztikai adatgyűjtés részleteit, és megvizsgálhatja azokat a [diagnosztikai keresésben](./diagnostic-search.md).

A .NET- [naplózási adapterek](./asp-net-trace-logs.md) ezt az API-t használják harmadik féltől származó naplók küldésére a portálon.

A Java [standard szintű naplózók (például a Log4J](./java-trace-logs.md) ) esetében a Logback Application Insights Log4J vagy Logback-hozzáfűzéset használ, hogy harmadik féltől származó naplókat küldjön a portálra.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Ügyfél/böngésző oldali JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Naplózhat egy diagnosztikai eseményt, például beírhat vagy elhagyhat egy metódust.

 Paraméter | Leírás
---|---
`message` | Diagnosztikai adatként. Sokkal hosszabb lehet, mint a név.
`properties` | Karakterlánc leképezése karakterláncként: további, a portálon a [kivételek szűréséhez](#properties) használt információk. Az alapértelmezett érték üres.
`severityLevel` | Támogatott értékek: [SeverityLevel. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Megkeresheti az üzenet tartalmát, de (a tulajdonság értékeitől eltérően) nem szűrheti.

A méretkorlát `message` értéke sokkal nagyobb, mint a tulajdonságok korlátja.
A TrackTrace előnye, hogy viszonylag hosszú adatmennyiséget helyezhet el az üzenetben. Például elvégezheti az adatposták küldését.  

Emellett súlyossági szintet is hozzáadhat az üzenethez. És hasonlóan más telemetria is, hozzáadhat tulajdonságokat is a különböző nyomkövetési csoportok szűréséhez vagy kereséséhez. Például:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

A [Search](./diagnostic-search.md)szolgáltatásban egyszerűen kiszűrheti az adott adatbázishoz kapcsolódó adott súlyossági szint összes üzenetét.

### <a name="traces-in-analytics"></a>Nyomkövetés az Analyticsben

[Application Insights elemzésekben](../logs/log-query-overview.md)a TrackTrace megjelenő hívások megjelennek a `traces` táblázatban.

Ha a [mintavételezés](./sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 azt jelenti, hogy a 10 meghívása `trackTrace()` esetén a mintavételi folyamat csak az egyiket továbbítja. A nyomkövetési hívások helyes számának megszerzéséhez használja a következő kódot: `traces | summarize sum(itemCount)` .

## <a name="trackdependency"></a>TrackDependency

A TrackDependency hívásával követheti a külső kódrészletre irányuló hívások válaszideje és sikerességi arányát. Az eredmények a portál függőségi diagramjaiban jelennek meg. Az alábbi kódrészletet fel kell venni, ahol függőségi hívás történik.

> [!NOTE]
> A .NET és a .NET Core esetében használhatja a `TelemetryClient.StartOperation` (kiterjesztés) metódust is, amely kitölti a `DependencyTelemetry` korrelációhoz és más tulajdonságokhoz szükséges tulajdonságokat, például a kezdési időt és az időtartamot, így nem kell egyéni időzítőt létrehoznia az alábbi példákkal. További információért olvassa el a jelen cikk [a kimenő függőségek nyomon követéséről szóló szakaszát](./custom-operations-tracking.md#outgoing-dependencies-tracking).

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Ne feledje, hogy a kiszolgálói SDK-k olyan [függőségi modult](./asp-net-dependencies.md) tartalmaznak, amely felkeresi és nyomon követi bizonyos függőségi hívásokat automatikusan – például az adatbázisok és a REST API-k számára. Telepítenie kell egy ügynököt a kiszolgálóra a modul működésének érdekében. 

A javában bizonyos függőségi hívásokat automatikusan nyomon követhetik a [Java Agent](./java-agent.md)használatával.

Ezt a hívást akkor használja, ha nyomon szeretné követni, hogy az automatikus követés ne kapjon hívásokat, vagy ha nem szeretné telepíteni az ügynököt.

A standard függőség-követési modul C# nyelven való kikapcsolásához szerkessze [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) , és törölje a hivatkozást a következőre: `DependencyCollector.DependencyTrackingTelemetryModule` . A Java-ban ne telepítse a Java-ügynököt, ha nem szeretné automatikusan gyűjteni a standard függőségeket.

### <a name="dependencies-in-analytics"></a>Az elemzés függőségei

[Application Insights elemzésekben](../logs/log-query-overview.md)a trackDependency-hívások megjelennek a `dependencies` táblázatban.

Ha a [mintavételezés](./sampling.md) folyamatban van, a ItemCount tulajdonság 1-nél nagyobb értéket jelenít meg. Például a itemCount = = 10 érték azt jelenti, hogy a trackDependency () 10 hívása a mintavételezési folyamat csak az egyiket továbbítja. A cél összetevő által szegmentált függőségek helyes számának beszerzéséhez használja a következő kódot:

```kusto
dependencies
| summarize sum(itemCount) by target
```

A függőségeknek a kapcsolódó kérésekkel való hozzárendeléséhez használjon illesztést:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Az adatkiürítés

Az SDK általában rögzített időközönként küld adatokat (általában 30 mp), vagy ha a puffer megtelt (általában 500 elem). Bizonyos esetekben azonban érdemes lehet kiüríteni a puffert, például ha az SDK-t olyan alkalmazásban használja, amely le van állítva.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

A [kiszolgáló telemetria-csatornája](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)aszinkron módon működik.

Ideális esetben a flush () metódust kell használni az alkalmazás leállítási tevékenységében.

## <a name="authenticated-users"></a>Hitelesített felhasználók

Egy webalkalmazásban a felhasználók a [cookie-k által azonosított](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool)(alapértelmezés szerint). Előfordulhat, hogy egy felhasználó többször is megszámolható, ha egy másik gépről vagy böngészőből fér hozzá az alkalmazáshoz, vagy ha cookie-kat töröl.

Ha a felhasználók bejelentkeznek az alkalmazásba, pontosabban megadhatja a hitelesített felhasználói azonosító beállítását a böngésző kódjában:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Egy ASP.NET web MVC-alkalmazásban, például:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nem szükséges a felhasználó tényleges bejelentkezési nevének használata. Csak olyan AZONOSÍTÓnak kell lennie, amely egyedi az adott felhasználó számára. Nem tartalmazhat szóközt vagy a karaktereket `,;=|` .

A felhasználói azonosító a munkamenet-cookie-ban is be van állítva, és a kiszolgálónak is el lesz küldve. Ha a Server SDK telepítve van, a rendszer az ügyfél és a kiszolgáló telemetria környezeti tulajdonságainak részeként elküldi a hitelesített felhasználói azonosítót. Ezután szűrheti és keresheti meg a keresést.

Ha az alkalmazás a felhasználókat fiókokba csoportosítja, akkor a fiók azonosítóját is átadhatja (ugyanazzal a karakteres korlátozásokkal).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

[Metrikaböngésző](../essentials/metrics-charts.md)létrehozhat egy olyan diagramot, amely **felhasználói, hitelesített** és **felhasználói fiókokat** számol fel.

A megadott felhasználóneveket és fiókokat tartalmazó ügyfél-adatpontokat is [megkeresheti](./diagnostic-search.md) .

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Az adatai szűrése, keresése és szegmentálása tulajdonságok használatával

Az eseményekhez tulajdonságok és mérések is csatolhatók (valamint mérőszámok, oldalletöltések, kivételek és egyéb telemetria adatok).

A *Tulajdonságok* olyan karakterlánc-értékek, amelyek segítségével szűrheti a telemetria a használati jelentésekben. Ha például az alkalmazás több játékot is biztosít, a játék nevét csatolhatja az egyes eseményekhez, így láthatja, hogy mely játékok népszerűbbek.

A karakterlánc hossza legfeljebb 8192 lehet. (Ha nagy adattömböket szeretne küldeni, használja a TrackTrace üzenet paraméterét.)

A *metrikák* olyan numerikus értékek, amelyek grafikus formában jeleníthetők meg. Például érdemes megtekinteni, hogy van-e fokozatos növekedés a játékosok által elért pontszámok között. A gráfok az eseménnyel ellátott tulajdonságok szerint szegmentálva lehetnek, így különálló vagy halmozott gráfokat is letölthet a különböző játékok számára.

Ahhoz, hogy a metrika helyesen jelenjen meg, a nullánál nagyobbnak vagy azzal egyenlőnek kell lennie.

Bizonyos korlátozások vonatkoznak a használható [Tulajdonságok, tulajdonságértékek és mérőszámok számára](#limits) .

*JavaScript*

```javascript
appInsights.trackEvent({
  name: 'some event',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});

appInsights.trackPageView({
  name: 'some page',
  properties: { // accepts any type
    prop1: 'string',
    prop2: 123.45,
    prop3: { nested: 'objects are okay too' }
  }
});
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Ügyeljen arra, hogy ne naplózza a személyazonosításra alkalmas adatokat a tulajdonságok között.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Tulajdonságok és metrikák beállításának alternatív módja

Ha kényelmesebb, egy különálló objektumban gyűjthet egy esemény paramétereit:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Ne használja ugyanazt a telemetria-példányt (ebben `event` a példában) a Track * () többszöri hívásához. Ez a telemetria helytelen konfigurációval való elküldése miatt fordulhat elő.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Egyéni mérések és tulajdonságok az elemzésekben

Az [elemzésben](../logs/log-query-overview.md)az egyéni metrikák és tulajdonságok az `customMeasurements` `customDimensions` egyes telemetria-rekordok és attribútumaik szerint jelennek meg.

Ha például hozzáadta a "Game" nevű tulajdonságot a kérelem telemetria, ez a lekérdezés a "játék" különböző értékeinek előfordulásait számolja, és megjeleníti az egyéni metrika "pontszáma" átlagát:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Figyelje meg az alábbiakat:

* Ha Kinyer egy értéket a customDimensions vagy a customMeasurements JSON-ból, dinamikus típussal rendelkezik, és ezért el kell azt tennie `tostring` `todouble` .
* A [mintavétel](./sampling.md)lehetőségének figyelembevételéhez a következőt kell használnia: `sum(itemCount)` `count()` .

## <a name="timing-events"></a><a name="timed"></a> Időzítési események

Néha diagramot szeretne készíteni, hogy mennyi ideig tart egy művelet végrehajtása. Előfordulhat például, hogy meg szeretné tudni, hogy a felhasználók milyen hosszú időt vesznek igénybe a játékokban. Ehhez a mérési paramétert használhatja.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Egyéni telemetria alapértelmezett tulajdonságai

Ha az Ön által írt egyéni események esetében meg szeretné határozni az alapértelmezett tulajdonságértékeket, beállíthatja őket egy TelemetryClient-példányban. Ezek az ügyféltől érkező összes telemetria-elemhez csatlakoznak.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Az egyes telemetria-hívások felülbírálják a tulajdonságok szótárában lévő alapértelmezett értékeket.

*JavaScript-alapú webes ügyfelek* esetén használja a JavaScript telemetria inicializáló.

*Ha tulajdonságokat szeretne hozzáadni az összes telemetria*, beleértve a standard gyűjtemények moduljaiból származó adatokkal is, [implementálja `ITelemetryInitializer`](./api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Telemetria mintavételezése, szűrése és feldolgozása

Az SDK-ból való elküldése előtt kódot írhat a telemetria feldolgozásához. A feldolgozás a szabványos telemetria-modulokból, például a HTTP-kérelmek gyűjtésével és a függőségek gyűjtésével elküldett adatokra vonatkozik.

[Adja hozzá](./api-filtering-sampling.md#add-properties) a telemetria tulajdonságokat a megvalósításhoz `ITelemetryInitializer` . Felveheti például a más tulajdonságok alapján számított verziószámokat vagy értékeket.

A [szűrés](./api-filtering-sampling.md#filtering) módosíthatja vagy elvetheti a TELEMETRIA az SDK-ból való elküldésük előtt `ITelemetryProcessor` . Ön határozza meg, hogy mi történjen az elküldésen vagy elvetve, de a metrikák hatásait kell figyelembe vennie. Az elemek elvetésének módjától függően elveszítheti a kapcsolódó elemek közötti váltás lehetőségét.

A [mintavétel](./api-filtering-sampling.md) egy csomagolt megoldás, amely csökkenti az alkalmazásból a portálra továbbított adatok mennyiségét. Ez nem befolyásolja a megjelenített metrikákat. Ez azonban nem befolyásolja a problémák diagnosztizálását a kapcsolódó elemek, például a kivételek, a kérelmek és az oldalletöltések közötti navigálás során.

[További információk](./api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Telemetria letiltása

A telemetria gyűjtésének és továbbításának *dinamikus leállítása és elindítása* :

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

A *kiválasztott standard gyűjtők letiltása*– például TELJESÍTMÉNYSZÁMLÁLÓK, HTTP-kérelmek vagy függőségek – törölje vagy írja be a megfelelő sorokat a [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Ezt például akkor teheti meg, ha a saját TrackRequest-adatait szeretné elküldeni.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

A *kiválasztott standard gyűjtők letiltásához*– például a teljesítményszámlálók, a HTTP-kérelmek vagy a függőségek – inicializáláskor, a lánc konfigurációs módszereinek beállítása az SDK inicializálási kódjához:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Ha az inicializálás után le szeretné tiltani ezeket a gyűjtőket, használja a következő konfigurációs objektumot: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Fejlesztői mód

A hibakeresés során hasznos lehet a telemetria gyorsítása a folyamaton keresztül, hogy az eredmények azonnal megjelenjenek. Emellett további üzeneteket is megadhat, amelyek segítségével nyomon követheti a telemetria kapcsolatos problémákat. Kapcsolja ki az éles környezetben, mert lelassíthatja az alkalmazást.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Node.js esetében engedélyezheti a fejlesztői üzemmódot, ha engedélyezi a belső naplózást a `setInternalLogging` és `maxBatchSize` a 0 értékre, ami azt eredményezi, hogy a telemetria az adatgyűjtés után azonnal elküldjék.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> A kialakítási kulcs beállítása a kiválasztott egyéni telemetria

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamic Instrumentation-kulcs

A fejlesztési, tesztelési és éles környezetek telemetria elkerülése érdekében [létrehozhat külön Application Insights erőforrásokat](./create-new-resource.md) , és módosíthatja a kulcsokat a környezettől függően.

Ahelyett, hogy a rendszerállapot-kulcsot beolvassa a konfigurációs fájlból, beállíthatja azt a kódban. Állítsa be a kulcsot egy inicializálási metódusban, például global.aspx.cs egy ASP.NET-szolgáltatásban:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

A weblapokon érdemes lehet azt beállítani a webkiszolgáló állapotáról, nem pedig a szó szerinti kódolást a szkriptben. Például egy ASP.NET-alkalmazásban generált weblapon:

*JavaScript a Borotvában*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

A TelemetryClient rendelkezik egy környezeti tulajdonsággal, amely az összes telemetria-adattal együtt elküldett értékeket tartalmazza. Ezeket általában a standard telemetria modulok határozzák meg, de saját maguk is megadhatók. Például:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Ha saját maga állítja be ezeket az értékeket, érdemes lehet eltávolítani a megfelelő sort a [ApplicationInsights.configból ](./configuration-with-applicationinsights-config.md), hogy az értékek és a standard értékek ne legyenek zavarosak.

* **Összetevő**: az alkalmazás és annak verziója.
* **Eszköz**: azon eszközre vonatkozó információ, amelyen az alkalmazás fut. (A Web Apps szolgáltatásban ez az a kiszolgáló vagy ügyfél-eszköz, amelyről a telemetria küldték.)
* **InstrumentationKey**: a Application Insights erőforrás az Azure-ban, ahol megjelenik a telemetria. Általában a ApplicationInsights.config.
* **Hely**: az eszköz földrajzi helye.
* **Művelet**: a web Appsben a jelenlegi HTTP-kérelem. Más típusú alkalmazások esetében beállíthatja, hogy az események csoportosítva legyenek.
  * **Azonosító**: olyan generált érték, amely különböző eseményeket kapcsol össze, így ha bármely eseményt megvizsgál a diagnosztikai keresésben, megtalálhatja a kapcsolódó elemeket.
  * **Name (név**): egy azonosító, amely általában a HTTP-kérelem URL-címe.
  * **SyntheticSource**: Ha nem null értékű vagy üres, egy karakterlánc, amely azt jelzi, hogy a kérelem forrása robotként vagy webes tesztként lett azonosítva. Alapértelmezés szerint a rendszer kizárja a számításokat a Metrikaböngészőban.
* **Tulajdonságok**: az összes telemetria-adattal továbbított tulajdonságok. Az egyes Track * hívásokban felülbírálható.
* **Munkamenet**: a felhasználó munkamenete. Az azonosító egy generált értékre van állítva, amely akkor változik, ha a felhasználó egy ideig nem aktív.
* **Felhasználó**: felhasználói adatok.

## <a name="limits"></a>Korlátok

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Az adatsebességi korlát elérésének elkerüléséhez használja a [mintavételezést](./sampling.md).

Az adatok megőrzési időtartamának megállapításához tekintse meg az [adatmegőrzést és az adatvédelmet](./data-retention-privacy.md).

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [ASP.NET-hivatkozás](/dotnet/api/overview/azure/insights)
* [Java-referenciák](/java/api/overview/azure/appinsights)
* [JavaScript-hivatkozás](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>SDK-kód 

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-csomagok](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Kérdések

* *Milyen kivételeket okozhatnak Track_ () hívások?*

    Nincsenek. A try-catch záradékokban nem kell becsomagolni őket. Ha az SDK problémákba ütközik, a hibakeresési konzol kimenetében üzeneteket fog naplózni, és – ha az üzenetek – a diagnosztikai keresés során jelentkeznek.
* *Van REST API az adatok a portálról való lekéréséhez?*

    Igen, az [adatelérési API](https://dev.applicationinsights.io/). Az adatok kinyerésének egyéb módjai közé tartozik az [elemzésből való exportálás Power bi](./export-power-bi.md) és a [folyamatos exportálás](./export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>További lépések

* [Események és naplók keresése](./diagnostic-search.md)
* [Hibaelhárítás](../faq.md)
