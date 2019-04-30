---
title: Az Azure Application Insights-adatmodellt |} A Microsoft Docs
description: A folyamatos exportálás JSON-ból exportált, és használja szűrőként tulajdonságait ismerteti.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898916"
---
# <a name="application-insights-export-data-model"></a>Application Insights exportálási Data Model
Ez a táblázat által küldött telemetriát tulajdonságait a [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDK-k a portálra.
Láthatja, hogy ezeket a tulajdonságokat az adatkimenet bármilyen [folyamatos exportálás](export-telemetry.md).
Is megjelennek a tulajdonságszűrők [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) és [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md).

Tudnivalók:

* `[0]` a táblázatokban azt jelzi, hogy egy pont az elérési út, amelyekben egy index beszúrása de nem mindig 0.
* Idő időtartamok vannak mikromásodperces, így száma 10 000 000 tized == 1 másodperc.
* Dátum és idő (UTC), és az ISO formátumban van megadva `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Példa
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

## <a name="context"></a>Környezet
Az összes típusú telemetriát környezet szakasz egészül ki. Ezek a mezők közül nem mindegyik küldött az összes adatpont.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| context.custom.dimensions [0] |[objektum] |Kulcs – érték sztringpárok egyéni tulajdonságok paraméter határozza meg. Maximális kulcshossz 100, értékeket 1024 karakternél. A tulajdonság több mint 100 egyedi értékeket, kereshető, de nem használható a Szegmentálás. Maximális száma 200 kulcsok száma rendszerállapotkulcsot. |
| context.custom.metrics [0] |[objektum] |Állítsa be az egyéni mértékek paraméter és a TrackMetrics kulcs-érték párokat. Maximális kulcshossz 100 értékek lehetnek numerikus. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |logikai |Kérelem úgy tűnik, hogy egy robot vagy a webes tesztből származnak. |
| context.data.samplingRate |szám |A telemetriát a portálra küldött SDK által generált százalékos értéke. Tartomány 0,0-100.0. |
| Context.Device |objektum |Ügyféleszközök |
| Context.Device.Browser |string |IE Chrome-ot vagy... |
| context.device.browserVersion |string |Chrome 48.0... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| Context.Device.ID |string | |
| Context.Device.Locale |string |de-DE, en-GB, az... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |Gazda operációs rendszer |
| context.device.roleInstance |string |Kiszolgáló állomás azonosítója |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| Context.Device.Type |string |Számítógép esetén a böngészőben... |
| Context.Location |objektum |Ügyfélip származik. |
| Context.location.City |string |Ha az ismert ügyfélip, származik |
| Context.location.ClientIP |string |Utolsó nyolcszöggel anonimok 0-ra. |
| Context.location.Continent |string | |
| Context.location.Country |string | |
| Context.location.province |string |Állam vagy megye |
| context.operation.id |string |A művelet azonos azonosítóval rendelkező elem sablonobjektumhoz kapcsolódó elemként jelenik meg a portálon. Általában a kérelem azonosítója. |
| context.operation.name |string |URL-cím vagy kérelem |
| context.operation.parentId |string |Lehetővé teszi, hogy a beágyazott kapcsolódó elemek. |
| context.session.id |string |Az azonos forrásból érkező műveletek egy csoportját azonosítója. Egy műveletet anélkül 30 percig jeleket a munkamenet végén. |
| context.session.isFirst |logikai | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Hitelesített felhasználó](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |logikai | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | Ha egy elem betöltött Application Insights hozzárendelt egyedi azonosítója |

## <a name="events"></a>Események
Egyéni események által generált [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] események száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] esemény neve |string |Esemény neve.  Maximális hossz 250. |
| [0] esemény URL-címe |string | |
| [0] esemény urlData.base |string | |
| event [0] urlData.host |string | |

## <a name="exceptions"></a>Kivételek
Jelentések [kivételek](../../azure-monitor/app/asp-net-exceptions.md) a kiszolgálón, valamint a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| szerelvény [0] basicException |string | |
| [0] basicException száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] basicException exceptionGroup |string | |
| [0] basicException exceptionType |string | |
| [0] basicException failedUserCodeMethod |string | |
| [0] basicException failedUserCodeAssembly |string | |
| [0] basicException handledAt |string | |
| [0] basicException hasFullStack |logikai | |
| [0] basicException azonosítója |string | |
| [0] basicException metódus |string | |
| [0] basicException üzenet |string |Kivételre vonatkozó üzenet. Maximális hossz: 10k. |
| [0] basicException outerExceptionMessage |string | |
| [0] basicException outerExceptionThrownAtAssembly |string | |
| [0] basicException outerExceptionThrownAtMethod |string | |
| [0] basicException outerExceptionType |string | |
| [0] basicException outerId |string | |
| parsedStack [0] szerelvény [0] basicException |string | |
| [0] basicException parsedStack [0] fájlnév |string | |
| basicException [0] [0] parsedStack szint |egész szám | |
| basicException [0] [0] parsedStack sor |egész szám | |
| basicException [0] [0] parsedStack metódus |string | |
| [0] basicException verem |string |Maximális hossz: 10k |
| a typeName basicException [0] |string | |

## <a name="trace-messages"></a>Nyomkövetési üzenetek
Által küldött [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), illetve a [naplózási adapterek](../../azure-monitor/app/asp-net-trace-logs.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] üzenet naplózó_neve |string | |
| üzenet [0] Paraméterek |string | |
| nyers [0] üzenet |string |A naplófájlüzenetre maximális hossz: 10k. |
| [0] üzenet err |string | |

## <a name="remote-dependency"></a>Távoli függőségek
TrackDependency által küldött. Jelentés teljesítményét és használatát használt [függőségekhez intézett hívások](../../azure-monitor/app/asp-net-dependencies.md) a kiszolgálón, és az AJAX-hívások a böngészőben.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| az aszinkron remoteDependency [0] |logikai | |
| [0] remoteDependency baseName |string | |
| [0] remoteDependency commandName |string |Például "home/index" |
| [0] remoteDependency száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] remoteDependency dependencyTypeName |string |HTTP, SQL, ... |
| [0] remoteDependency durationMetric.value |szám |A befejezési válasz a függőségi hívás ideje |
| [0] remoteDependency azonosítója |string | |
| [0] remoteDependency neve |string |URL-címe. Maximális hossz 250. |
| [0] remoteDependency eredménykód |string |a HTTP-függőségek |
| [0] remoteDependency sikeres |logikai | |
| [0] remoteDependency típusa |string |HTTP, Sql... |
| [0] remoteDependency URL-címe |string |Maximális hossz 2000 |
| [0] remoteDependency urlData.base |string |Maximális hossz 2000 |
| [0] remoteDependency urlData.hashTag |string | |
| [0] remoteDependency urlData.host |string |Maximális hossza 200 |

## <a name="requests"></a>Kérelmek
Által küldött [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). A globális modulok ezzel jelentések kiszolgálói válaszidő mérése a kiszolgálón.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] kérések száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Példa: 4 =&gt; 25%. |
| kérelem [0] durationMetric.value |szám |A válasz érkező kérelem ideje. 1e7 == 1s |
| a [0] kérés azonosítója |string |Műveletazonosító |
| [0] kérelem neve |string |GET/POST + alap URL-címét.  Maximális hossz 250 |
| kérelem [0] responseCode |egész szám |Az ügyfélnek küldött HTTP-válasz |
| [0] sikeres kérelem |logikai |Alapértelmezett == (responseCode &lt; 400) |
| [0] kérelem URL-címe |string |Nem többek között a host |
| kérelem [0] urlData.base |string | |
| kérelem [0] urlData.hashTag |string | |
| kérelem [0] urlData.host |string | |

## <a name="page-view-performance"></a>Oldal nézet teljesítmény
A böngésző által küldött. Egy oldal, a felhasználótól a kérelem teljes (kivéve az aszinkron AJAX-hívások) megjeleníthető kezdeményezése feldolgozni időt méri.

Környezet értékek megjelenítése az ügyfél operációs rendszer és a böngésző verziója.

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] clientPerformance clientProcess.value |egész szám |A HTML-megjelenítés, a lap azért végétől ideje. |
| [0] clientPerformance neve |string | |
| [0] clientPerformance networkConnection.value |egész szám |A hálózati kapcsolat ideje. |
| [0] clientPerformance receiveRequest.value |egész szám |Idő a végén a kérést küld a válasz fogadása a HTML-kódot. |
| [0] clientPerformance sendRequest.value |egész szám |A Time-e a HTTP-kérelem küldése. |
| [0] clientPerformance total.value |egész szám |Idő elindítását, a kérést küldhet az oldal megjelenítése. |
| [0] clientPerformance URL-címe |string |A kérelem URL-címe |
| [0] clientPerformance urlData.base |string | |
| [0] clientPerformance urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| [0] clientPerformance urlData.protocol |string | |

## <a name="page-views"></a>Oldalmegtekintések
TrackPageView() által küldött vagy [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| [0] száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| [0] durationMetric.value megtekintése |egész szám |Igény szerint hozzáadhat trackPageView() vagy startTrackPage() - érték stopTrackPage(). Nem ugyanaz, mint clientPerformance értékeket. |
| [0] nézet neve |string |Lap címe.  Maximális hossz 250 |
| url megtekintése [0] |string | |
| [0] urlData.base megtekintése |string | |
| [0] urlData.hashTag megtekintése |string | |
| [0] urlData.host megtekintése |string | |

## <a name="availability"></a>Rendelkezésre állás
Jelentések [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md).

| Útvonal | Típus | Megjegyzések |
| --- | --- | --- |
| rendelkezésre állási [0] availabilityMetric.name |string |rendelkezésre állás |
| rendelkezésre állási [0] availabilityMetric.value |szám |1.0-s vagy 0,0 |
| rendelkezésre állási [0] száma |egész szám |100 / ([mintavételi](../../azure-monitor/app/sampling.md) sebesség). Ha például 4 =&gt; 25 %. |
| rendelkezésre állási [0] dataSizeMetric.name |string | |
| rendelkezésre állási [0] dataSizeMetric.value |egész szám | |
| rendelkezésre állási [0] durationMetric.name |string | |
| rendelkezésre állási [0] durationMetric.value |szám |Vizsgálat időtartama. 1e7==1s |
| rendelkezésre állási [0] üzenet |string |Diagnosztikai hiba |
| [0] rendelkezésre állási eredmény |string |Pass/sikertelen |
| rendelkezésre állási [0] runLocation |string |Http-kérés földrajzi forrása |
| rendelkezésre állási [0] testName |string | |
| rendelkezésre állási [0] testRunId |string | |
| rendelkezésre állási [0] testTimestamp |string | |

## <a name="metrics"></a>Mérőszámok
A trackmetric() függvény által létrehozott.

A metrika érték megtalálható context.custom.metrics[0]

Példa:

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
         } ] }
    }

## <a name="about-metric-values"></a>Metrikaértékek kapcsolatban
Standard objektum struktúra metrikaértékek metrikai jelentések és a máshol, is jelenti. Példa:

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

Jelenleg – Bár a előfordulhat, hogy módosítja a jövőben – az összes értéket a standard szintű SDK modulok által jelentett `count==1` , és csak a `name` és `value` mezők hasznosak. Az egyetlen eset, ahol lennének különböző lenne, ha a saját TrackMetric-hívások írási amely csoportban a többi paraméter.

A többi mező célja, hogy a metrikák, hogy az SDK-ban, a forgalom csökkentése érdekében a portálra. Például több egymást követő olvasmányok sikerült átlagos egyes metrikai jelentés elküldése előtt. Ezután lenne kiszámítása a min, max, szórás és összesített érték (sum vagy átlag), és állítsa száma a jelentés által képviselt értékek száma.

A fenti táblázatokban a ritkán használt mezők száma, min, max, stdDev és sampledValue kihagytunk azt.

Előre összesítése metrikák helyett használhat [mintavételi](../../azure-monitor/app/sampling.md) Ha lerövidítheti a telemetriai adatok mennyisége.

### <a name="durations"></a>Időtartamok
Azzal a különbséggel, jelezve, időtartamok tized-mikromásodperces jelennek meg, hogy 10000000.0 azt jelenti, hogy 1 másodperc.

## <a name="see-also"></a>Lásd még
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [A folyamatos exportálás](export-telemetry.md)
* [Kódminták](export-telemetry.md#code-samples)
