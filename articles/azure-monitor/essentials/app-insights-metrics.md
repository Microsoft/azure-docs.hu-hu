---
title: Azure Application Insights log-alapú mérőszámok | Microsoft Docs
description: Ez a cikk a támogatott összesítésekkel és Dimenziókkal rendelkező Azure Application Insights mérőszámokat sorolja fel. A log-alapú metrikák részletei tartalmazzák a mögöttes Kusto-lekérdezési utasításokat.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.openlocfilehash: bd28fa9f20775be659de6cef99f9ceda29fe52cc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049134"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights log-alapú metrikák

Application Insights log-alapú metrikák segítségével elemezheti a figyelt alkalmazások állapotát, hatékony irányítópultokat hozhat létre, és beállíthatja a riasztásokat. Kétféle metrika létezik:

* A jelenet mögötti [log-alapú mérőszámok](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) le vannak fordítva a tárolt események [Kusto-lekérdezéseinek](/azure/kusto/query/) .
* A [standard mérőszámok](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) előre összesített idősorozatként vannak tárolva.

Mivel a *standard mérőszámok* előzetes összesítése a gyűjtemény során történik, jobb teljesítményt biztosítanak a lekérdezés időpontjában. Ez jobb választást tesz lehetővé az irányítópultok és a valós idejű riasztások számára. A *log-alapú mérőszámok* több dimenzióval rendelkeznek, így az adatelemzés és az alkalmi diagnosztika kiváló lehetőséget biztosít számukra. A [névtér-választóval](./metrics-getting-started.md#create-your-first-metric-chart) válthat a log-alapú és a standard mérőszámok között a [metrikák Explorerben](./metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>A cikkben szereplő lekérdezések értelmezése és használata

Ez a cikk a támogatott összesítésekkel és Dimenziókkal rendelkező metrikákat sorolja fel. A log-alapú metrikák részletei tartalmazzák a mögöttes Kusto-lekérdezési utasításokat. A kényelmes használat érdekében minden lekérdezés alapértelmezett értékeket használ az idő részletességének, a diagram típusának és a méretezés időnként, amely leegyszerűsíti a Log Analytics lekérdezés használatát anélkül, hogy módosításra lenne szükség.

Ha ugyanazt a metrikát ábrázolja a [metrikák Explorerben](./metrics-getting-started.md), nincsenek alapértelmezett beállítások – a lekérdezés a diagram beállításai alapján dinamikusan módosul:

- A kiválasztott **időtartomány** egy további, *ahol timestamp...* záradékban van lefordítva, hogy csak a kijelölt időtartományból válassza ki az eseményeket. Például egy diagram, amely a legutóbbi 24 óra adattárat mutatja, a lekérdezés tartalmazza a *(z) | where időbélyeg > ago (24 óra)*.

- A kiválasztott **időrészletesség** a végső *összegzésbe kerül... raktárhely (időbélyeg, [Time Grain]) záradék alapján* .

- A kiválasztott **szűrési** méretek további *Where* záradékokra vannak lefordítva.

- A kiválasztott **felosztott diagram** dimenzió egy extra összefoglaló tulajdonságra van lefordítva. Ha például a diagramot a *hely* alapján osztja szét, és egy 5 perces részletességgel ábrázolja azt, akkor a program összegzi az *Összefoglaló* záradékot *... raktárhely alapján (timestamp, 5 m), hely*.

> [!NOTE]
> Ha még nem ismeri a Kusto lekérdezési nyelvét, a Kusto-utasítások másolásával és beillesztésével az Log Analytics lekérdezési ablaktáblán végezheti el a módosítások végrehajtása nélkül. Az alapszintű diagram megjelenítéséhez kattintson a **Futtatás** gombra. Ahogy elkezdi megérteni a lekérdezési nyelv szintaxisát, elindíthatja a kisebb módosításokat, és megtekintheti a módosítás hatását. A saját adatai megismerése nagyszerű lehetőséget jelent a [log Analytics](../logs/log-analytics-tutorial.md) és [Azure monitor](../overview.md)teljes teljesítményének megkezdésére.

## <a name="availability-metrics"></a>Rendelkezésre állási metrikák

A rendelkezésre állási kategória metrikái lehetővé teszik a webalkalmazás állapotának megtekintését a világ minden pontján megfigyelt módon. [Adja meg a rendelkezésre állási teszteket](../app/monitor-web-app-availability.md) , hogy megkezdje az ebből a kategóriából származó mérőszámok használatát.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Rendelkezésre állás (availabilityResults/availabilityPercentage)
A *rendelkezésre állási* metrika a webteszt futtatásának azon hányadát mutatja, amely nem észlelt problémákat. A legalacsonyabb lehetséges érték a 0, amely azt jelzi, hogy a webteszt összes futtatása meghiúsult. A 100 érték azt jelenti, hogy a webteszt összes futtatása megfelelt az érvényesítési feltételeknek.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Százalék|Átlag|Futtatási hely, teszt neve|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Rendelkezésre állási teszt időtartama (availabilityResults/időtartam)

A *rendelkezésre állási teszt időtartamának* mérőszáma azt mutatja, hogy mennyi idő telt el a webes teszt futtatásához. A [többlépéses webes tesztek](../app/availability-multistep.md)esetében a metrika az összes lépés végrehajtásának teljes idejét mutatja.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Futtatás helye, teszt neve, teszt eredménye

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Rendelkezésre állási tesztek (availabilityResults/darabszám)

A *rendelkezésre állási tesztek* mérőszáma Azure monitor által futtatott webes tesztek számát tükrözi.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Darabszám|Darabszám|Futtatás helye, teszt neve, teszt eredménye|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Böngésző metrikái

A böngésző metrikáit a Application Insights JavaScript SDK gyűjti a valós végfelhasználói böngészőkből. Nagyszerű betekintést nyújtanak a felhasználói élménybe a webalkalmazással. A böngésző metrikái jellemzően nem mintavételezés alatt állnak, ami azt jelenti, hogy a használati számok nagyobb pontosságot biztosítanak, mint a kiszolgálóoldali metrikák, amelyeket mintavételezéssel lehet eldönteni.

> [!NOTE]
> A böngésző metrikáinak összegyűjtéséhez az alkalmazásnak a [Application Insights JavaScript SDK](../app/javascript.md)-val kell rendelkeznie.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Böngésző oldalának betöltési ideje (browserTimings/totalDuration)

|Mértékegység|Támogatott összesítések|Előre összevont méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Ügyfél feldolgozási ideje (browserTiming/processingDuration)

|Mértékegység|Támogatott összesítések|Előre összevont méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Oldal betöltési hálózati kapcsolati ideje (browserTimings/networkDuration)

|Mértékegység|Támogatott összesítések|Előre összevont méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Válasz fogadása (browserTimings/receiveDuration)

|Mértékegység|Támogatott összesítések|Előre összevont méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Kérelem küldésének ideje (browserTimings/sendDuration)

|Mértékegység|Támogatott összesítések|Előre összevont méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Hiba mérőszámai

A **hibák** mérőszámai a kérelmek feldolgozásával, a függőségi hívásokkal és a kidobott kivételekkel kapcsolatos problémákat mutatják.

### <a name="browser-exceptions-exceptionsbrowser"></a>Böngészőbeli kivételek (kivételek/böngésző)

Ez a metrika a böngészőben futó alkalmazás kódjából kiváltott kivételek számát mutatja. ```trackException()```A metrika csak a Application INSIGHTS API-hívással követett kivételeket tartalmazza.

|Mértékegység|Támogatott összesítések|Előre összevont méretek|Jegyzetek|
|---|---|---|---|
|Darabszám|Darabszám|Nincs|A log-alapú verzió a **Sum** összesítést használja.|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Függőségi hívások hibái (függőségek/sikertelen)

A sikertelen függőségi hívások száma.

|Mértékegység|Támogatott összesítések|Előre összevont méretek|Jegyzetek|
|---|---|---|---|
|Darabszám|Darabszám|Nincs|A log-alapú verzió a **Sum** összesítést használja.|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Kivételek (kivételek/darabszám)

Minden alkalommal, amikor kivételt naplóz Application Insights, az SDK [trackException () metódusát](../app/api-custom-events-metrics.md#trackexception) hívja meg. A kivételek mérőszáma a naplózott kivételek számát jeleníti meg.

|Mértékegység|Támogatott összesítések|Előre összevont méretek|Jegyzetek|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkör neve, felhőalapú szerepkör-példány, eszköz típusa|A log-alapú verzió a **Sum** összesítést használja.|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Sikertelen kérelmek (kérelmek/sikertelen)

A *sikertelenként* megjelölt, nyomon követett kiszolgálói kérelmek száma. Alapértelmezés szerint a Application Insights SDK automatikusan megjelöl minden olyan kiszolgálói kérelmet, amely sikertelen kérelemként HTTP-5xx vagy 4xx adott vissza. Ezt a logikát testreszabhatja úgy, hogy módosítja a kérelem telemetria-eleme  *sikerességi* tulajdonságát egy [Egyéni telemetria-inicializálásban](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Mértékegység|Támogatott összesítések|Előre összevont méretek|Jegyzetek|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkör-példány, Felhőbeli szerepkör neve, valós vagy szintetikus forgalom, kérelmek teljesítménye, válasz kódja|A log-alapú verzió a **Sum** összesítést használja.|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Kiszolgálói kivételek (kivételek/kiszolgáló)

Ez a metrika a kiszolgálói kivételek számát jeleníti meg.

|Mértékegység|Támogatott összesítések|Előre összevont méretek|Jegyzetek|
|---|---|---|---|
|Darabszám|Darabszám|Felhőbeli szerepkör neve, felhőalapú szerepkör-példány|A log-alapú verzió a **Sum** összesítést használja.|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Teljesítményszámlálók

A **teljesítményszámlálók** kategóriájában lévő metrikák használatával érheti el [Application Insights által gyűjtött rendszerteljesítményi számlálókat](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Rendelkezésre álló memória (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Kivételi arány (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP-kérelem végrehajtási ideje (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-kérelmek aránya (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-kérelmek az alkalmazás-várólistában (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Folyamat CPU (performanceCounters/processCpuPercentage)

A metrika azt mutatja, hogy a teljes processzor-kapacitás mekkora hányadát használja a figyelt alkalmazást működtető folyamat.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Százalék|Átlag, min, Max|Felhőalapú szerepkör-példány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Feldolgozási IO-arány (performanceCounters/processIOBytesPerSecond)

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Bájt/másodperc|Átlag, min, Max|Felhőalapú szerepkör-példány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Folyamat saját bájtjai (performanceCounters/processPrivateBytes)

Azon nem megosztott memória mennyisége, amelyet a figyelt folyamat az adatforráshoz lefoglalt.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Bájt|Átlag, min, Max|Felhőalapú szerepkör-példány

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processzoridő (performanceCounters/processorCpuPercentage)

CPU-felhasználás a figyelt kiszolgálói példányon futó *összes* folyamat alapján.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Százalék|Átlag, min, Max|Felhőalapú szerepkör-példány

>[!NOTE]
> A processzoridő-metrika nem érhető el az Azure App Servicesban üzemeltetett alkalmazásokhoz. A  [folyamat CPU](#process-cpu-performancecountersprocesscpupercentage) -metrikájának használatával követheti a app Servicesban üzemeltetett webalkalmazások CPU-kihasználtságát.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Kiszolgálói metrikák

### <a name="dependency-calls-dependenciescount"></a>Függőségi hívások (függőségek/darabszám)

Ez a metrika a függőségi hívások számával kapcsolatban áll.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Függőség időtartama (függőségek/időtartam)

Ez a metrika a függőségi hívások időtartamára utal.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Kiszolgálói kérelmek (kérelmek/darabszám)

Ez a metrika a webalkalmazás által fogadott bejövő kiszolgálói kérelmek számát mutatja.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Kiszolgáló válaszideje (kérelmek/időtartam)

Ez a metrika azt jelzi, hogy a kiszolgálók hogyan dolgozzák fel a beérkező kéréseket.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Használati metrikák

### <a name="page-view-load-time-pageviewsduration"></a>Oldal nézet betöltési ideje (oldalmegtekintés/időtartam)

Ez a metrika az oldalmegtekintési események betöltéséhez szükséges idő mennyiségére utal.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Oldalletöltések (oldalmegtekintések/darabszám)

A TrackPageView () Application Insights API-val naplózott oldalmegtekintési események száma.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Munkamenetek (munkamenetek/darabszám)

Ez a metrika a különböző munkamenet-azonosítók darabszámára vonatkozik.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Nyomkövetések (nyomkövetés/darabszám)

A TrackTrace () Application Insights API-hívással naplózott nyomkövetési utasítások száma.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Felhasználók (felhasználók/darabszám)

Azon különálló felhasználók száma, akik hozzáfértek az alkalmazáshoz. A metrika pontossága jelentős hatással lehet a telemetria mintavétel és szűrés használatával.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Felhasználók, hitelesített (felhasználó/hitelesített)

Az alkalmazásba hitelesített különböző felhasználók száma.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```