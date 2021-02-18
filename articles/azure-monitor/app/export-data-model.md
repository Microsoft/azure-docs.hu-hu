---
title: Az Azure Application Insights adatmodellje | Microsoft Docs
description: Leírja a folyamatos exportálásból a JSON-ban exportált tulajdonságokat, és szűrőként használatos.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: b4609d54c1c3c33a654dd58a3bceaca4974fda15
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584219"
---
# <a name="application-insights-export-data-model"></a>Application Insights adatmodell exportálása
Ez a táblázat a [Application Insights](./app-insights-overview.md) SDK-k által a portálra eljuttatott telemetria tulajdonságait sorolja fel.
Ezek a tulajdonságok a [folyamatos exportálásból](export-telemetry.md)származó adatkimenetben jelennek meg.
Emellett megjelennek a tulajdonságok szűrői a [metrika-kezelőben](../essentials/metrics-charts.md) és a [diagnosztikai keresésben](./diagnostic-search.md).

Megjegyzés:

* `[0]` ezekben a táblázatokban egy olyan pontot jelöl, amelyben egy indexet kell beszúrnia. Ez azonban nem mindig 0.
* Az időtartamok egy másodpercen belül vannak, így 10000000 = = 1 másodperc.
* A dátumok és időpontok UTC, és ISO formátumban vannak megadva `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Példa

```json
// A server report about an HTTP request
{
  "request": [
    {
      "urlData": { // derived from 'url'
        "host": "contoso.org",
        "base": "/",
        "hashTag": ""
      },
      "responseCode": 200, // Sent to client
      "success": true, // Default == responseCode<400
      // Request id becomes the operation id of child events
      "id": "fCOhCdCnZ9I=",  
      "name": "GET Home/Index",
      "count": 1, // 100% / sampling rate
      "durationMetric": {
        "value": 1046804.0, // 10000000 == 1 second
        // Currently the following fields are redundant:
        "count": 1.0,
        "min": 1046804.0,
        "max": 1046804.0,
        "stdDev": 0.0,
        "sampledValue": 1046804.0
      },
      "url": "/"
    }
  ],
  "internal": {
    "data": {
      "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
      "documentVersion": "1.61"
    }
  },
  "context": {
    "device": { // client browser
      "type": "PC",
      "screenResolution": { },
      "roleInstance": "WFWEB14B.fabrikam.net"
    },
    "application": { },
    "location": { // derived from client ip
      "continent": "North America",
      "country": "United States",
      // last octagon is anonymized to 0 at portal:
      "clientip": "168.62.177.0",
      "province": "",
      "city": ""
    },
    "data": {
      "isSynthetic": true, // we identified source as a bot
      // percentage of generated data sent to portal:
      "samplingRate": 100.0,
      "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
    },
    "user": {
      "isAuthenticated": false,
      "anonId": "us-tx-sn1-azr", // bot agent id
      "anonAcquisitionDate": "0001-01-01T00:00:00Z",
      "authAcquisitionDate": "0001-01-01T00:00:00Z",
      "accountAcquisitionDate": "0001-01-01T00:00:00Z"
    },
    "operation": {
      "id": "fCOhCdCnZ9I=",
      "parentId": "fCOhCdCnZ9I=",
      "name": "GET Home/Index"
    },
    "cloud": { },
    "serverDevice": { },
    "custom": { // set by custom fields of track calls
      "dimensions": [ ],
      "metrics": [ ]
    },
    "session": {
      "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
      "isFirst": true
    }
  }
}
```

## <a name="context"></a>Környezet
A telemetria összes típusát egy környezeti szakasz kíséri. A mezők nem mindegyike kerül továbbításra minden adatponttal.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| Context. Custom. dimensions [0] |objektum [] |Egyéni tulajdonságok paraméterrel beállított kulcs-érték karakterlánc-párok. A kulcs maximális hossza 100, az értékek maximális hossza 1024. Több mint 100 egyedi érték, a tulajdonság kereshető, de nem használható szegmentáláshoz. Maximális 200 kulcs/rendszerállapotkulcsot. |
| Context. Custom. mérőszámok [0] |objektum [] |Egyéni mérési paraméterek és TrackMetrics szerint beállított kulcs-érték párok. A kulcs maximális hossza 100, az értékek numerikusak lehetnek. |
| Context. eventTime |sztring |UTC |
| Context. isSynthetic |boolean |Úgy tűnik, hogy a kérelem egy robotból vagy egy webes tesztből származik. |
| Context. samplingRate |szám |A portálra eljuttatott SDK által generált telemetria százalékos aránya. Tartomány 0,0 – 100.0. |
| környezet. eszköz |object |Ügyféleszközök |
| Context. Device. Browser |sztring |IE, Chrome,... |
| Context. Device. browserVersion |sztring |Chrome 48,0,... |
| Context. Device. deviceModel |sztring | |
| Context. Device. deviceName |sztring | |
| context.device.id |sztring | |
| környezet. eszköz. területi beállítás |sztring |en-GB, de-DE,... |
| kontextus. Device. Network |sztring | |
| Context. Device. oemName |sztring | |
| Context. Device. os |sztring | |
| Context. Device. osVersion |sztring |Gazda operációs rendszer |
| Context. Device. roleInstance |sztring |A kiszolgáló gazdagépének azonosítója |
| Context. Device. roleName |sztring | |
| Context. Device. screenResolution |sztring | |
| Context. Device. Type |sztring |SZÁMÍTÓGÉP, böngésző,... |
| környezet. hely |object |Származtatva `clientip` . |
| kontextus. location. City |sztring |Származtatva `clientip` , ha ismert |
| Context. location. ügyfélip |sztring |Az utolsó nyolcszög értéke 0. |
| Context. location. kontinens |sztring | |
| környezet. hely. ország |sztring | |
| környezet. hely. tartomány |sztring |Állam vagy megye |
| context.operation.id |sztring |Azok az elemek, amelyek azonosak, a `operation id` portálon kapcsolódó elemként jelennek meg. Általában a `request id` . |
| context.operation.name |sztring |URL-cím vagy kérelem neve |
| Context. Operation. parentId |sztring |Lehetővé teszi a beágyazott kapcsolódó elemek használatát. |
| context.session.id |sztring |`Id` ugyanabból a forrásból származó műveletek csoportjából. Egy művelet nélküli, 30 perces időszak a munkamenet végét jelzi. |
| Context. Session. isFirst |boolean | |
| Context. user. accountAcquisitionDate |sztring | |
| Context. user. accountId |sztring | |
| Context. user. anonAcquisitionDate |sztring | |
| Context. user. anonId |sztring | |
| Context. user. authAcquisitionDate |sztring |[Hitelesített felhasználó](./api-custom-events-metrics.md#authenticated-users) |
| Context. user. authId |sztring | |
| Context. user. isAuthenticated |boolean | |
| Context. user. storeRegion |sztring | |
| internal.data.documentVersion |sztring | |
| internal.data.id |sztring | `Unique id` akkor van hozzárendelve, amikor a rendszer betölti az elemeket Application Insights |

## <a name="events"></a>Események
A [TrackEvent ()](./api-custom-events-metrics.md#trackevent)által generált egyéni események.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| esemény [0] darabszám |egész szám |100/([mintavételi](./sampling.md) sebesség). Például 4 = &gt; 25%. |
| esemény [0] név |sztring |Esemény neve.  Maximális hosszúság 250. |
| esemény [0] URL-cím |sztring | |
| esemény [0] urlData. Base |sztring | |
| esemény [0] urlData. Host |sztring | |

## <a name="exceptions"></a>Kivételek
A kiszolgáló és a böngésző [kivételeit](./asp-net-exceptions.md) jelenti.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| basicException [0] szerelvény |sztring | |
| basicException [0] darabszám |egész szám |100/([mintavételi](./sampling.md) sebesség). Például 4 = &gt; 25%. |
| basicException [0] exceptionGroup |sztring | |
| basicException [0] exceptionType |sztring | |
| basicException [0] failedUserCodeMethod |sztring | |
| basicException [0] failedUserCodeAssembly |sztring | |
| basicException [0] handledAt |sztring | |
| basicException [0] hasFullStack |boolean | |
| basicException [0] `id` |sztring | |
| basicException [0] metódus |sztring | |
| basicException [0] üzenet |sztring |Kivételi üzenet. Maximális hossz: 10k. |
| basicException [0] outerExceptionMessage |sztring | |
| basicException [0] outerExceptionThrownAtAssembly |sztring | |
| basicException [0] outerExceptionThrownAtMethod |sztring | |
| basicException [0] outerExceptionType |sztring | |
| basicException [0] outerId |sztring | |
| basicException [0] parsedStack [0] szerelvény |sztring | |
| basicException [0] parsedStack [0] fájlnév |sztring | |
| basicException [0] parsedStack [0] szint |egész szám | |
| basicException [0] parsedStack [0] sor |egész szám | |
| basicException [0] parsedStack [0] metódus |sztring | |
| basicException [0] verem |sztring |Maximális hossz 10k |
| basicException [0] typeName |sztring | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
A [TrackTrace](./api-custom-events-metrics.md#tracktrace)és a [naplózási adapterek](./asp-net-trace-logs.md)küldik.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| üzenet [0] loggerName |sztring | |
| üzenet [0] paraméter |sztring | |
| üzenet [0] nyers |sztring |A log üzenet, a maximális hossz 10k. |
| üzenet [0] severityLevel |sztring | |

## <a name="remote-dependency"></a>Távoli függőség
A TrackDependency küldte. A kiszolgáló [függőségeinek](./asp-net-dependencies.md) és a BÖNGÉSZŐBEN található Ajax-hívásoknak a teljesítményére és használatára irányuló hívások jelentésére szolgál.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| remoteDependency [0] aszinkron |boolean | |
| remoteDependency [0] baseName |sztring | |
| remoteDependency [0] commandName |sztring |Például "Home/index" |
| remoteDependency [0] darabszám |egész szám |100/([mintavételi](./sampling.md) sebesség). Például 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |sztring |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |szám |A meghívástól a válasz befejezésére való függőség szerinti idő |
| remoteDependency [0] `id` |sztring | |
| remoteDependency [0] név |sztring |URL. Maximális hosszúság 250. |
| remoteDependency [0] resultCode |sztring |HTTP-függőségből |
| remoteDependency [0] sikeres |boolean | |
| remoteDependency [0] típus |sztring |Http, SQL,... |
| remoteDependency [0] URL-cím |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData. Base |sztring |Maximális hossz 2000 |
| remoteDependency [0] urlData. hashTag |sztring | |
| remoteDependency [0] urlData. Host |sztring |Maximális hossz 200 |

## <a name="requests"></a>Kérelmek
A [TrackRequest](./api-custom-events-metrics.md#trackrequest)küldte. A standard modulok ezt a kiszolgálót használják a kiszolgálón mért válaszidő megjelentéséhez.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| kérelem [0] darabszám |egész szám |100/([mintavételi](./sampling.md) sebesség). Például: 4 = &gt; 25%. |
| kérelem [0] durationMetric. Value |szám |A kérelemtől érkező válasz időpontja. 1e7 = = 1s |
| kérelem [0] `id` |sztring |`Operation id` |
| kérelem [0] név |sztring |GET/POST + URL-cím alapja.  Maximális hossz 250 |
| kérelem [0] responseCode |egész szám |Az ügyfélnek küldött HTTP-válasz |
| kérelem [0] sikeres |boolean |Default = = (responseCode &lt; 400) |
| kérelem [0] URL-cím |sztring |Nem tartalmazza a gazdagépet |
| kérelem [0] urlData. Base |sztring | |
| kérelem [0] urlData. hashTag |sztring | |
| kérelem [0] urlData. Host |sztring | |

## <a name="page-view-performance"></a>Az oldal nézetének teljesítménye
A böngésző küldte. Megméri egy oldal feldolgozásának idejét, a felhasználótól a kérelem megjelenítésének befejezését kezdeményező (aszinkron AJAX-hívások kivételével).

A környezeti értékek az ügyfél operációs rendszerének és a böngészőnek a verzióját mutatják.

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |egész szám |A HTML-fájl fogadásának vége a lap megjelenítéséhez. |
| clientPerformance [0] név |sztring | |
| clientPerformance [0] networkConnection. Value |egész szám |Hálózati kapcsolat létesítéséhez szükséges idő. |
| clientPerformance [0] receiveRequest. Value |egész szám |A kérelem küldésének időpontja a HTML-válaszban való fogadáshoz. |
| clientPerformance [0] SendRequest hívás. Value |egész szám |A HTTP-kérelem elküldéséhez szükséges idő. |
| clientPerformance [0] Total. Value |egész szám |A kérés elküldésének időpontja a lap megjelenítéséhez. |
| clientPerformance [0] URL-cím |sztring |A kérelem URL-címe |
| clientPerformance [0] urlData. Base |sztring | |
| clientPerformance [0] urlData. hashTag |sztring | |
| clientPerformance [0] urlData. Host |sztring | |
| clientPerformance [0] urlData. Protocol |sztring | |

## <a name="page-views"></a>Lapmegtekintések
TrackPageView () vagy [stopTrackPage](./api-custom-events-metrics.md#page-views) küldte

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| nézet [0] száma |egész szám |100/([mintavételi](./sampling.md) sebesség). Például 4 = &gt; 25%. |
| [0] durationMetric. Value megtekintése |egész szám |Az érték opcionálisan be van állítva a trackPageView () vagy a startTrackPage ()-stopTrackPage () paraméterrel. Nem ugyanaz, mint a clientPerformance értéke. |
| nézet [0] neve |sztring |Lap címe  Maximális hossz 250 |
| [0] URL-cím megtekintése |sztring | |
| [0] urlData. Base megtekintése |sztring | |
| a [0] urlData. hashTag megtekintése |sztring | |
| a (z) [0] urlData. Host megtekintése |sztring | |

## <a name="availability"></a>Rendelkezésre állás
Jelentések [rendelkezésre állását ismertető webes tesztek](./monitor-web-app-availability.md).

| Elérési út | Típus | Jegyzetek |
| --- | --- | --- |
| rendelkezésre állás [0] availabilityMetric.name |sztring |availability |
| rendelkezésre állás [0] availabilityMetric. Value |szám |1,0 vagy 0,0 |
| rendelkezésre állás [0] darabszám |egész szám |100/([mintavételi](./sampling.md) sebesség). Például 4 = &gt; 25%. |
| rendelkezésre állás [0] dataSizeMetric.name |sztring | |
| rendelkezésre állás [0] dataSizeMetric. Value |egész szám | |
| rendelkezésre állás [0] durationMetric.name |sztring | |
| rendelkezésre állás [0] durationMetric. Value |szám |A teszt időtartama. 1e7 = = 1s |
| rendelkezésre állás [0] üzenet |sztring |Sikertelen diagnosztika |
| rendelkezésre állás [0] eredmény |sztring |Sikeres/sikertelen |
| rendelkezésre állás [0] runLocation |sztring |Http REQ földrajzi forrása |
| rendelkezésre állás [0] testName |sztring | |
| rendelkezésre állás [0] testRunId |sztring | |
| rendelkezésre állás [0] testTimestamp |sztring | |

## <a name="metrics"></a>Mérőszámok
Létrehozta: TrackMetric ().

A metrika értéke a kontextusban található. egyéni. mérőszámok [0]

Például:

```json
{
  "metric": [ ],
  "context": {
  ...
    "custom": {
      "dimensions": [
        { "ProcessId": "4068" }
      ],
      "metrics": [
        {
          "dispatchRate": {
            "value": 0.001295,
            "count": 1.0,
            "min": 0.001295,
            "max": 0.001295,
            "stdDev": 0.0,
            "sampledValue": 0.001295,
            "sum": 0.001295
          }
        }
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>A metrika értékei
A metrikai jelentésekben és máshol is szereplő metrikus értékeket a standard objektum struktúrája jelenti. Például:

```json
"durationMetric": {
  "name": "contoso.org",
  "type": "Aggregation",
  "value": 468.71603053650279,
  "count": 1.0,
  "min": 468.71603053650279,
  "max": 468.71603053650279,
  "stdDev": 0.0,
  "sampledValue": 468.71603053650279
}
```

Jelenleg – bár ez a jövőben is változhat – a standard SDK-modulok által jelentett összes értéknél, `count==1` és csak a `name` és a `value` mezők hasznosak. Az egyetlen eset, amikor más lenne, ha olyan TrackMetric-hívásokat ír, amelyekben a többi paramétert beállította.

A többi mező célja, hogy lehetővé tegye a metrikák összesítését az SDK-ban, hogy csökkentse a forgalmat a portálon. Előfordulhat például, hogy az egyes metrikai jelentések elküldése előtt több egymást követő beolvasás is megtörténhet. Ezután kiszámítja a minimum, a maximum, a szórás és az összesített érték (összeg vagy átlag) értékét, és a jelentés által képviselt olvasások számát adja meg.

A fenti táblázatokban kihagyta a ritkán használt mezők darabszámát, a min, a Max, a stdDev és a sampledValue.

A metrikák előzetes összesítése helyett [mintavételt](./sampling.md) is használhat, ha csökkentenie kell a telemetria mennyiségét.

### <a name="durations"></a>Időtartamok
Ha másként nincs jelezve, az időtartamok egy másodperc tizedében jelennek meg, így a 10000000,0 1 másodpercet jelent.

## <a name="see-also"></a>Lásd még
* [Application Insights](./app-insights-overview.md)
* [Folyamatos exportálás](export-telemetry.md)
* [Kódminták](export-telemetry.md#code-samples)

