---
title: Azure Application Insights standard mérőszámok | Microsoft Docs
description: Ez a cikk a támogatott összesítésekkel és Dimenziókkal rendelkező Azure Application Insights mérőszámokat sorolja fel.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572353"
---
# <a name="application-insights-standard-metrics"></a>Standard mérőszámok Application Insights

A standard mérőszámok a gyűjtés során előre összevontak, és a lekérdezési időpontnál jobb teljesítményt biztosítanak. Ez az irányítópultok és a valós idejű riasztások legmegfelelőbb választását teszi lehetővé.

## <a name="availability-metrics"></a>Rendelkezésre állási metrikák

A rendelkezésre állási kategória metrikái lehetővé teszik a webalkalmazás állapotának megtekintését a világ minden pontján megfigyelt módon. [Adja meg a rendelkezésre állási teszteket](../app/monitor-web-app-availability.md) , hogy megkezdje az ebből a kategóriából származó mérőszámok használatát.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Rendelkezésre állás (availabilityResults/availabilityPercentage)
A *rendelkezésre állási* metrika a webteszt futtatásának azon hányadát mutatja, amely nem észlelt problémákat. A legalacsonyabb lehetséges érték a 0, amely azt jelzi, hogy a webteszt összes futtatása meghiúsult. A 100 érték azt jelenti, hogy a webteszt összes futtatása megfelelt az érvényesítési feltételeknek.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Százalék|Átlag|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Rendelkezésre állási teszt időtartama (availabilityResults/időtartam)

A *rendelkezésre állási teszt időtartamának* mérőszáma azt mutatja, hogy mennyi idő telt el a webes teszt futtatásához. A [többlépéses webes tesztek](../app/availability-multistep.md)esetében a metrika az összes lépés végrehajtásának teljes idejét mutatja.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Rendelkezésre állási tesztek (availabilityResults/darabszám)

A *rendelkezésre állási tesztek* mérőszáma Azure monitor által futtatott webes tesztek számát tükrözi.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|---|---|---|
|Darabszám|Darabszám|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Böngésző metrikái

A böngésző metrikáit a Application Insights JavaScript SDK gyűjti a valós végfelhasználói böngészőkből. Nagyszerű betekintést nyújtanak a felhasználói élménybe a webalkalmazással. A böngésző metrikái jellemzően nem mintavételezés alatt állnak, ami azt jelenti, hogy a használati számok nagyobb pontosságot biztosítanak, mint a kiszolgálóoldali metrikák, amelyeket mintavételezéssel lehet eldönteni.

> [!NOTE]
> A böngésző metrikáinak összegyűjtéséhez az alkalmazásnak a [Application Insights JavaScript SDK](../app/javascript.md)-val kell rendelkeznie.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Böngésző oldalának betöltési ideje (browserTimings/totalDuration)

|Mértékegység|Támogatott összesítések| Támogatott méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Ügyfél feldolgozási ideje (browserTiming/processingDuration)

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Oldal betöltési hálózati kapcsolati ideje (browserTimings/networkDuration)

|Mértékegység|Támogatott összesítések| Támogatott méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Válasz fogadása (browserTimings/receiveDuration)

|Mértékegység|Támogatott összesítések| Támogatott méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

### <a name="send-request-time-browsertimingssendduration"></a>Kérelem küldésének ideje (browserTimings/sendDuration)

|Mértékegység|Támogatott összesítések| Támogatott méretek|
|---|---|---|
|Ezredmásodpercben|Átlag, min, Max|Nincs|

## <a name="failure-metrics"></a>Hiba mérőszámai

A **hibák** mérőszámai a kérelmek feldolgozásával, a függőségi hívásokkal és a kidobott kivételekkel kapcsolatos problémákat mutatják.

### <a name="browser-exceptions-exceptionsbrowser"></a>Böngészőbeli kivételek (kivételek/böngésző)

Ez a metrika a böngészőben futó alkalmazás kódjából kiváltott kivételek számát mutatja. ```trackException()```A metrika csak a Application INSIGHTS API-hívással követett kivételeket tartalmazza.

|Mértékegység|Támogatott összesítések | Támogatott méretek|
|---|---|---|---|
| Darabszám | Darabszám | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Függőségi hívások hibái (függőségek/sikertelen)

A sikertelen függőségi hívások száma.

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
|Darabszám|Darabszám| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Kivételek (kivételek/darabszám)

Minden alkalommal, amikor kivételt naplóz Application Insights, az SDK [trackException () metódusát](../app/api-custom-events-metrics.md#trackexception) hívja meg. A kivételek mérőszáma a naplózott kivételek számát jeleníti meg.

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
|Darabszám|Darabszám|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Sikertelen kérelmek (kérelmek/sikertelen)

A *sikertelenként* megjelölt, nyomon követett kiszolgálói kérelmek száma. Alapértelmezés szerint a Application Insights SDK automatikusan megjelöl minden olyan kiszolgálói kérelmet, amely sikertelen kérelemként HTTP-5xx vagy 4xx adott vissza. Ezt a logikát testreszabhatja úgy, hogy módosítja a kérelem telemetria-eleme  *sikerességi* tulajdonságát egy [Egyéni telemetria-inicializálásban](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
|Darabszám|Darabszám|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Kiszolgálói kivételek (kivételek/kiszolgáló)

Ez a metrika a kiszolgálói kivételek számát jeleníti meg.

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
|Darabszám|Darabszám|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Teljesítményszámlálók

A **teljesítményszámlálók** kategóriájában lévő metrikák használatával érheti el [Application Insights által gyűjtött rendszerteljesítményi számlálókat](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Rendelkezésre álló memória (performanceCounters/availableMemory)

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
|Függőben lévő adatmennyiség: megabájt, gigabájt|Átlag, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Kivételi arány (performanceCounters/exceptionRate)

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
| Darabszám | Átlag, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP-kérelem végrehajtási ideje (performanceCounters/requestExecutionTime)

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
| Ezredmásodpercben | Átlag, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-kérelmek aránya (performanceCounters/requestsPerSecond)

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
| Kérések száma másodpercenként | Átlag, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-kérelmek az alkalmazás-várólistában (performanceCounters/requestsInQueue)

|Mértékegység|Támogatott összesítések | Támogatott méretek |
|---|---|---|---|
| Darabszám | Átlag, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Folyamat CPU (performanceCounters/processCpuPercentage)

A metrika azt mutatja, hogy a teljes processzor-kapacitás mekkora hányadát használja a figyelt alkalmazást működtető folyamat.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Százalék|Átlag, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Feldolgozási IO-arány (performanceCounters/processIOBytesPerSecond)

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Bájt/másodperc|Átlag, min, Max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Folyamat saját bájtjai (performanceCounters/processPrivateBytes)

Azon nem megosztott memória mennyisége, amelyet a figyelt folyamat az adatforráshoz lefoglalt.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Bájt|Átlag, min, Max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processzoridő (performanceCounters/processorCpuPercentage)

CPU-felhasználás a figyelt kiszolgálói példányon futó *összes* folyamat alapján.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
|Százalék|Átlag, min, Max|`Cloud role instance` |

>[!NOTE]
> A processzoridő-metrika nem érhető el az Azure App Servicesban üzemeltetett alkalmazásokhoz. A  [folyamat CPU](#process-cpu-performancecountersprocesscpupercentage) -metrikájának használatával követheti a app Servicesban üzemeltetett webalkalmazások CPU-kihasználtságát.

## <a name="server-metrics"></a>Kiszolgálói metrikák

### <a name="dependency-calls-dependenciescount"></a>Függőségi hívások (függőségek/darabszám)

Ez a metrika a függőségi hívások számával kapcsolatban áll.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Darabszám | Darabszám | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Függőség időtartama (függőségek/időtartam)

Ez a metrika a függőségi hívások időtartamára utal.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Idő | Átlag, min, Max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Kiszolgálói kérelmek aránya (kérelmek/darabszám)

Ez a metrika a webalkalmazás által fogadott bejövő kiszolgálói kérelmek számát mutatja.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Darabszám | Átlag | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Kiszolgálói kérelmek (kérelmek/darabszám)

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Darabszám | Darabszám | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Kiszolgáló válaszideje (kérelmek/időtartam)

Ez a metrika azt jelzi, hogy a kiszolgálók hogyan dolgozzák fel a beérkező kéréseket.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Idő | Átlag, min, Max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Használati metrikák

### <a name="page-view-load-time-pageviewsduration"></a>Oldal nézet betöltési ideje (oldalmegtekintés/időtartam)

Ez a metrika az oldalmegtekintési események betöltéséhez szükséges idő mennyiségére utal.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Idő | Átlag, min, Max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Oldalletöltések (oldalmegtekintések/darabszám)

A TrackPageView () Application Insights API-val naplózott oldalmegtekintési események száma.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Darabszám | Darabszám | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Nyomkövetések (nyomkövetés/darabszám)

A TrackTrace () Application Insights API-hívással naplózott nyomkövetési utasítások száma.

|Mértékegység|Támogatott összesítések|Támogatott méretek|
|---|---|---|
| Darabszám | Darabszám | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Következő lépések

* További információ a [napló alapú és a már aggregált mérőszámokról](./pre-aggregated-metrics-log-metrics.md).
* [A log-alapú metrikák lekérdezései és definíciói](../essentials/app-insights-metrics.md).