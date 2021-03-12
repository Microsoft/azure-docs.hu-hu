---
title: host.jsAzure Functions 2. x esetén
description: A v2 futtatókörnyezettel rendelkező fájl Azure Functions host.jsdokumentációja.
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: cbedf2212c52d8f1996d3cce0d96d494313ea525
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608818"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x és újabb verziók – host.json referencia 

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2. verzió +](functions-host-json.md)

A metaadatokat tartalmazó fájl *host.js* globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk azokat a beállításokat sorolja fel, amelyek a Azure Functions futtatókörnyezet 2. x verziójával kezdődnek.  

> [!NOTE]
> Ez a cikk Azure Functions 2. x és újabb verziókban használható.  Az 1. x függvények host.jsának hivatkozását lásd:host.jsaz [ Azure functions 1. x](functions-host-json-v1.md)esetében.

A más Function app konfigurációs lehetőségei az [Alkalmazásbeállítások](functions-app-settings.md) (telepített alkalmazások esetében) vagy a [local.settings.js](functions-run-local.md#local-settings-file) fájlon (helyi fejlesztés esetén) kezelhetők.

A rendszer a kötésekhez kapcsolódó host.jskonfigurációit egyformán alkalmazza a Function alkalmazás egyes függvényei. 

Az Alkalmazásbeállítások használatával [felülbírálhatja vagy alkalmazhatja a beállításokat a környezetekben](#override-hostjson-values) .

## <a name="sample-hostjson-file"></a>Példa host.jsfájlra

A (z) 2. x + verzióban található fájlon a következő minta *host.js* minden lehetséges beállítással rendelkezik (kivéve a csak belső használatra vonatkozó beállításokat).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

A cikk következő fejezetei ismertetik az egyes legfelső szintű tulajdonságokat. Ha másként nincs megadva, az összes megadása nem kötelező.

## <a name="aggregator"></a>aggregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Ez a beállítás a [naplózás](#logging)gyermeke.

A Application Insights vezérlőelem-beállításai, beleértve a [mintavételi beállításokat](./configure-monitoring.md#configure-sampling)is.

A teljes JSON-struktúra esetében tekintse [meg a fájl host.jskorábbi példáját](#sample-hostjson-file).

> [!NOTE]
> A napló mintavételezése miatt előfordulhat, hogy egyes végrehajtások nem jelennek meg a Application Insights figyelő paneljén. A naplók mintavételezésének elkerüléséhez adja hozzá `excludedTypes: "Request"` a értéket a következőhöz: `samplingSettings` .

| Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| samplingSettings | n.a. | Lásd: [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Élő metrikák gyűjtésének engedélyezése. |
| enableDependencyTracking | true | A függőségek követésének engedélyezése. |
| enablePerformanceCountersCollection | true | Engedélyezi a kudu teljesítményszámlálók gyűjtését. |
| liveMetricsInitializationDelay | 00:00:15 | Csak belső használatra. |
| httpAutoCollectionOptions | n.a. | Lásd: [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | n.a. | Lásd: [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

További információ ezekről a beállításokról: [mintavétel Application Insightsban](../azure-monitor/app/sampling.md). 

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| isEnabled | true | Engedélyezheti vagy letilthatja a mintavételezést. | 
| maxTelemetryItemsPerSecond | 20 | Az egyes kiszolgálók gazdagépén másodpercenként naplózott telemetria-elemek megcélzott száma. Ha az alkalmazás számos gazdagépen fut, csökkentse ezt az értéket, hogy az a forgalom teljes célján belül maradjon. | 
| evaluationInterval | 01:00:00 | A telemetria aktuális sebességének újraértékelésének időköze. A kiértékelést mozgóátlagként kell végrehajtani. Előfordulhat, hogy le kívánja rövidíteni ezt az intervallumot, ha a telemetria a hirtelen törtek. |
| initialSamplingPercentage| 100,0 | A mintavételi folyamat elején alkalmazott kezdeti mintavételezési arány dinamikusan változik a százalékos arányban. Hibakeresés közben ne csökkentse az értéket. |
| samplingPercentageIncreaseTimeout | 00:00:01 | A mintavételezés százalékos értékének megváltozásakor ez a tulajdonság határozza meg, hogy a későbbiekben milyen hamar Application Insights a mintavételezési százalékos arány a további adatmennyiségek rögzítéséhez. |
| samplingPercentageDecreaseTimeout | 00:00:01 | A mintavételezés százalékos értékének megváltozásakor ez a tulajdonság határozza meg, hogy a későbbiekben milyen hamar Application Insights a mintavételezési százalékos arány a kevesebb adattal való rögzítéshez. |
| minSamplingPercentage | 0.1 | Mivel a mintavételezési százalék változó, ez a tulajdonság határozza meg a minimálisan megengedett mintavételi százalékot. |
| maxSamplingPercentage | 100,0 | Mivel a mintavételezési százalék változó, ez a tulajdonság határozza meg a maximálisan megengedett mintavételi százalékot. |
| movingAverageRatio | 1.0 | A mozgóátlag kiszámításakor a legutóbbi értékhez rendelt súlyozást. 1 értékkel egyenlő vagy annál kisebb értéket használjon. A kisebb értékek miatt az algoritmus kevésbé lesz újraaktiválva a hirtelen változásokkal. |
| excludedTypes | null | Pontosvesszővel tagolt lista, amelyet nem szeretne mintát venni. A felismert típusok a következők:,,,, `Dependency` `Event` `Exception` `PageView` `Request` és `Trace` . A rendszer továbbítja a megadott típusok összes példányát. a nem megadott típusok mintául szolgálnak. |
| includedTypes | null | A mintavételhez használni kívánt típusok pontosvesszővel tagolt listája; az üres lista minden típust magában foglalja. Írja be az `excludedTypes` itt felsorolt felülbírálási típusok listán szereplő típust. A felismert típusok a következők:,,,, `Dependency` `Event` `Exception` `PageView` `Request` és `Trace` . A megadott típusok példányainak mintája; a nem megadott vagy vélelmezett típusú típusok mintavételezés nélkül továbbítódnak. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Engedélyezheti vagy letilthatja a http-eseményindítók kiterjesztett HTTP-kérelmi információit: bejövő kérelmek korrelációs fejlécei, többtényezős kulcsok támogatása, HTTP-metódus, elérési út és válasz. |
| enableW3CDistributedTracing | true | Engedélyezheti vagy letilthatja a W3C elosztott nyomkövetési protokoll támogatását (és bekapcsolja az örökölt korrelációs sémát). Alapértelmezés szerint engedélyezve `enableHttpTriggerExtendedInfoCollection` , ha igaz. Ha `enableHttpTriggerExtendedInfoCollection` a értéke false (hamis), ez a jelző csak a kimenő kérelmekre vonatkozik, nem a bejövő kérelmekre. |
| enableResponseHeaderInjection | true | Engedélyezheti vagy letilthatja a többszörös összetevők korrelációs fejlécének a válaszokban való befecskendezését. Az injekció engedélyezése lehetővé teszi, hogy Application Insights egy alkalmazás-hozzárendelést, hogy több rendszerállapot-kulcs legyen használatban. Alapértelmezés szerint engedélyezve `enableHttpTriggerExtendedInfoCollection` , ha igaz. Ez a beállítás nem érvényes `enableHttpTriggerExtendedInfoCollection` , ha a értéke hamis. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

A pillanatképekkel kapcsolatos további információkért tekintse meg a [.NET-alkalmazásokban előforduló kivételek hibakeresését](../azure-monitor/app/snapshot-debugger.md) ismertető témakört, valamint a [Application Insights Snapshot Debugger vagy Pillanatképek megtekintését engedélyező problémákat](../azure-monitor/app/snapshot-debugger-troubleshoot.md)

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- | 
| agentEndpoint | null | A Application Insights Snapshot Debugger szolgáltatáshoz való kapcsolódáshoz használt végpont. Ha NULL, a rendszer egy alapértelmezett végpontot használ. |
| captureSnapshotMemoryWeight | 0,5 | A folyamat aktuális memória-méretéhez megadott súlyozás, ha a rendszer ellenőrzi, hogy van-e elég memória a pillanatkép elvégzéséhez. A várt érték nagyobb, mint 0 megfelelő tört (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | A telemetria-processzor letiltása előtt a pillanatképek kérésére vonatkozó sikertelen kérelmek számának korlátozása.|
| handleUntrackedExceptions | true | Engedélyezheti vagy letilthatja a Application Insights telemetria által nem követett kivételek nyomon követését. |
| isEnabled | true | Pillanatkép-gyűjtemények engedélyezése vagy letiltása | 
| isEnabledInDeveloperMode | hamis | A pillanatkép-gyűjtemények engedélyezése vagy letiltása fejlesztői módban. |
| isEnabledWhenProfiling | true | Engedélyezheti vagy letilthatja a pillanatképek létrehozását, még akkor is, ha a Application Insights Profiler részletes profilkészítési munkamenetet gyűjt. |
| isExceptionSnappointsEnabled | hamis | Engedélyezheti vagy letilthatja a kivételek szűrését. |
| isLowPrioritySnapshotUploader | true | Meghatározza, hogy a SnapshotUploader folyamat a normál prioritás alatt fusson-e. |
| maximumCollectionPlanSize | 50 | Az egyiktől a 9999-ig terjedő tartományban bármikor nyomon követhető problémák maximális száma. |
| maximumSnapshotsRequired | 3 | Egy adott problémára összegyűjtött Pillanatképek maximális száma az egyiktől a 999-ig terjedő tartományban. Probléma lehet az alkalmazás egyéni Throw utasítása. Ha a problémához összegyűjtött Pillanatképek száma eléri ezt az értéket, a rendszer nem gyűjt további pillanatképeket a problémához, amíg vissza nem állítja a problémát számlálókat (lásd: `problemCounterResetInterval` ), és a `thresholdForSnapshotting` korlát újra elérhetővé válik. |
| problemCounterResetInterval | 24:00:00 | Milyen gyakran kell alaphelyzetbe állítani a probléma számlálóit egy perc és hét nap között. Ha eléri ezt az időközt, a rendszer az összes probléma számát nullára állítja vissza. A meglévő olyan problémák, amelyek már elértek a pillanatképek küszöbértékét, de még nem hozták létre a pillanatképek számát a alkalmazásban `maximumSnapshotsRequired` , aktívak maradnak. |
| provideAnonymousTelemetry | true | Meghatározza, hogy a névtelen használatot és a hibát telemetria kell-e küldeni a Microsoftnak. Ez a telemetria akkor használható, ha felveszi a kapcsolatot a Microsofttal a Snapshot Debugger problémáinak elhárítása érdekében. A használati minták figyelésére is használható. |
| reconnectInterval | 00:15:00 | Az Snapshot Debugger-végponthoz való Újrakapcsolódás gyakorisága. A megengedett tartomány egy perc és egy nap között lehet. |
| shadowCopyFolder | null | A bináris fájlok árnyékmásolat-másolásához használandó mappát adja meg. Ha nincs beállítva, az alábbi környezeti változók által megadott mappák a következő sorrendben lesznek kipróbálva: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Ha az értéke igaz, a SnapshotUploader csak egy példánya gyűjt és tölt fel pillanatképeket több, a InstrumentationKey-t használó alkalmazáshoz. Ha hamis értékre van állítva, a SnapshotUploader minden (ProcessName, InstrumentationKey) rekord esetében egyedi lesz. |
| snapshotInLowPriorityThread | true | Meghatározza, hogy a pillanatképek feldolgozása alacsony IO-prioritású szálon történjen-e. A pillanatképek létrehozása gyors művelet, de a pillanatképnek a Snapshot Debugger szolgáltatásba való feltöltéséhez először a lemezre kell írni minidump. Ez a SnapshotUploader folyamat során történik. Ha ezt az értéket igaz értékre állítja, az alacsony prioritású IO-t használja a minidump írásához, amely nem fog versenyezni az alkalmazás erőforrásaival. Ha ezt az értéket hamis értékre állítja, a minidump létrehozása az alkalmazás lelassulásának rovására kerül. |
| snapshotsPerDayLimit | 30 | Egy nap alatt engedélyezett Pillanatképek maximális száma (24 óra). Ez a korlát a Application Insights szolgáltatási oldalon is érvényben van. A feltöltések díjszabása napi 50-ra korlátozódik (ez a rendszerállapot-kulcs). Ez az érték segít megelőzni a feltöltés során a végül elutasított további pillanatképek létrehozását. A nulla érték teljesen eltávolítja a korlátot, ami nem ajánlott. |
| snapshotsPerTenMinutesLimit | 1 | A pillanatképek maximális száma 10 percen belül. Bár ehhez az értékhez nincs felső korlát, körültekintően kell megnövelni az éles számítási feladatokhoz, mert ez hatással lehet az alkalmazás teljesítményére. A pillanatképek létrehozása gyorsan elvégezhető, de a pillanatkép minidump létrehozása és a Snapshot Debugger szolgáltatásba való feltöltése sokkal lassabb művelet, amely az erőforrásokhoz (CPU és I/O) való alkalmazással verseng. |
| tempFolder | null | Meghatározza a minidumps és a feltöltő naplófájljainak írására szolgáló mappát. Ha nincs beállítva, akkor a rendszer a *%temp%\Dumps* használja. |
| thresholdForSnapshotting | 1 | Hányszor Application Insights kell megtekintenie egy kivételt, mielőtt megkéri a pillanatképeket. |
| uploaderProxy | null | Felülbírálja a pillanatkép-feltöltő folyamat során használt proxykiszolgálót. Előfordulhat, hogy ezt a beállítást kell használnia, ha az alkalmazás egy proxykiszolgálón keresztül csatlakozik az internethez. A Snapshot Collector az alkalmazás folyamatán belül fut, és ugyanazokat a proxybeállításokat fogja használni. A pillanatkép-feltöltő azonban külön folyamatként fut, és előfordulhat, hogy manuálisan kell konfigurálnia a proxykiszolgálót. Ha ez az érték null, a Snapshot Collector megkísérli automatikusan felderíteni a proxy címeit a System .net. Request. DefaultWebProxy és a pillanatkép-feltöltő értékének átadásával. Ha ez az érték nem null, akkor a rendszer nem használja az automatikus észlelést, és az itt megadott proxykiszolgálót fogja használni a pillanatkép-Feltöltőben. |

## <a name="blobs"></a>Blobok

A konfigurációs beállítások a [Storage blob-eseményindítók és-kötések](functions-bindings-storage-blob.md#hostjson-settings)szolgáltatásban találhatók.  

## <a name="cosmosdb"></a>cosmosDb

A konfigurációs beállítás [Cosmos db eseményindítókban és kötésekben](functions-bindings-cosmosdb-v2-output.md#host-json)található.

## <a name="customhandler"></a>customHandler

Egyéni kezelő konfigurációs beállításai. További információ: [Azure functions egyéni kezelők](functions-custom-handlers.md#configuration).

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|Tulajdonság | Alapértelmezett | Leírás |
| --------- | --------- | --------- |
| defaultExecutablePath | n.a. | A végrehajtható fájl, amely az egyéni kezelői folyamatként indul el. Az egyéni kezelők használata kötelező beállítás, és az értéke a Function alkalmazás gyökeréhez képest relatív. |
| workingDirectory | *function alkalmazás gyökere* | Az a munkakönyvtár, amelyben el kell indítani az egyéni kezelő folyamatát. Ez egy opcionális beállítás, amely értéke a Function alkalmazás gyökeréhez képest relatív. |
| argumentumok | n.a. | Az egyéni kezelői folyamatnak átadandó parancssori argumentumok tömbje. |
| enableForwardingHttpRequest | hamis | Ha be van állítva, az összes olyan függvény, amely csak HTTP-triggerből és HTTP-kimenetből áll, a rendszer az egyéni kezelő [kérelmének adattartalma](functions-custom-handlers.md#request-payload)helyett az eredeti http-kérelmet továbbítja. |

## <a name="durabletask"></a>durableTask

A konfigurációs beállítás a [Durable functions kötéseiben](durable/durable-functions-bindings.md#host-json)található.

## <a name="eventhub"></a>eventHub

A konfigurációs beállítások az [Event hub-eseményindítókban és-kötésekben](functions-bindings-event-hubs.md#host-json)találhatók. 

## <a name="extensions"></a>Extensions

Tulajdonság, amely egy olyan objektumot ad vissza, amely tartalmazza az összes kötési beállítást, például a [http](#http) -t és a [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

A bővítmények lehetővé teszik, hogy a Function alkalmazáshoz hozzáadjon egy kompatibilis functions-bővítményt. További információ: [bővítmények a helyi fejlesztéshez](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funkciók

A gazdagép által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyileg futtatott](functions-run-local.md)használatra készült. Az Azure-ban a Function apps szolgáltatásban a [függvények letiltása a Azure Functionsben](disable-function.md) című cikkben ismertetett lépéseket követve letilthatja az egyes függvényeket, és nem használhatja ezt a beállítást.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes függvény időtúllépési időtartamát jelzi. A TimeSpan karakterlánc-formátumát követi. 

| Csomag típusa | Alapértelmezett (min.) | Maximum (min.) |
| -- | -- | -- |
| Használat | 5 | 10 |
| <sup>1</sup> . prémium | 30 | -1 (nem kötött)<sup>2</sup> |
| Dedikált (App Service) | 30 | -1 (nem kötött)<sup>2</sup> |

<sup>1</sup> a prémium szintű csomag végrehajtása csak 60 percig garantált, de technikailag nem kötött.   
<sup>2</sup> az érték a nem `-1` kötött végrehajtást jelzi, de a rögzített felső korlát fenntartása ajánlott.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

A [gazdagép állapotának figyelésére](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)vonatkozó konfigurációs beállítások.

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|engedélyezve|true|Megadja, hogy engedélyezve van-e a szolgáltatás. | 
|healthCheckInterval|10 másodperc|Az időszakos háttér állapotának ellenőrzése közötti időtartam. | 
|healthCheckWindow|2 perc|A beállítással együtt használt csúszó Time-ablak `healthCheckThreshold` .| 
|healthCheckThreshold|6|Az állapot-ellenőrzések maximális száma a gazdagép újraindítása előtt.| 
|counterThreshold|0,80|Az a küszöbérték, amelynél a teljesítményszámláló a nem megfelelő állapotot veszi figyelembe.| 

## <a name="http"></a>http

A konfigurációs beállítások a http- [Eseményindítók és-kötések](functions-bindings-http-webhook-output.md#hostjson-settings)szolgáltatásban találhatók.

## <a name="logging"></a>naplózás

A Function alkalmazás naplózási viselkedését szabályozza, beleértve a Application Insightst is.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Meghatározza, hogy a fájlok naplózása milyen szintű legyen engedélyezve.  A lehetőségek a következők:, `never` `always` `debugOnly` . |
|Naplózási szint|n.a.|Az alkalmazásban lévő függvények naplózási kategóriájának szűrését meghatározó objektum. Ezzel a beállítással szűrheti az adott függvények naplózását. További információ: a [naplózási szintek konfigurálása](configure-monitoring.md#configure-log-levels). |
|konzol|n.a.| A [konzol](#console) naplózási beállítása. |
|applicationInsights|n.a.| A [applicationInsights](#applicationinsights) beállítás. |

## <a name="console"></a>konzol

Ez a beállítás a [naplózás](#logging)gyermeke. A konzol naplózását vezérli, ha nem hibakeresési módban van.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|isEnabled|hamis|Engedélyezheti vagy letilthatja a konzol naplózását.| 

## <a name="manageddependency"></a>managedDependency

A felügyelt függőség egy olyan szolgáltatás, amely jelenleg csak PowerShell-alapú függvények esetén támogatott. Lehetővé teszi a függőségek automatikus kezelését a szolgáltatással. Ha a `enabled` tulajdonság értéke `true` , a `requirements.psd1` rendszer feldolgozza a fájlt. A függőségek akkor frissülnek, ha minden másodlagos verzió megjelent. További információ: [felügyelt függőség](functions-reference-powershell.md#dependency-management) a PowerShell-cikkben.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>üzenetsorok

A konfigurációs beállítások a [Storage üzenetsor-eseményindítók és-kötések](functions-bindings-storage-queue.md#host-json)szolgáltatásban találhatók.  

## <a name="retry"></a>retry

Az [újrapróbálkozási házirend](./functions-bindings-error-pages.md#retry-policies-preview) beállításait szabályozza az alkalmazás összes végrehajtásához.

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|stratégia|null|Kötelező. A használt újrapróbálkozási stratégia. Az érvényes értékek a következők: `fixedDelay` vagy `exponentialBackoff` .|
|Maxretrycount csak|null|Kötelező. Az újrapróbálkozások maximális száma egy függvény végrehajtásakor. `-1` azt jelenti, hogy határozatlan ideig próbálkozik.|
|delayInterval|null|Az újrapróbálkozások közötti késleltetés a `fixedDelay` stratégia használatával.|
|minimumInterval|null|A stratégia használatának minimális újrapróbálkozási késleltetése `exponentialBackoff` .|
|maximumInterval|null|A stratégia használatának maximális újrapróbálkozási késleltetése `exponentialBackoff` .| 

## <a name="sendgrid"></a>sendGrid

A konfigurációs beállítás a SendGrid- [Eseményindítók és-kötések](functions-bindings-sendgrid.md#host-json)szolgáltatásban található.

## <a name="servicebus"></a>serviceBus

A konfigurációs beállítás [Service Bus eseményindítókban és kötésekben](functions-bindings-service-bus-output.md#host-json)található.

## <a name="singleton"></a>Singleton

Az egyszeri zárolási viselkedés konfigurációs beállításai. További információért lásd az egyszeri [támogatással kapcsolatos GitHub-problémát](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|lockPeriod|00:00:15|Az az időszak, ameddig a rendszer a működési szintet zárolja. A zárolások automatikus megújítása.| 
|listenerLockPeriod|00:01:00|A figyelő zárolásának időtartama.| 
|listenerLockRecoveryPollingInterval|00:01:00|A figyelő zárolásának helyreállításához használt időintervallum, ha a figyelő zárolása nem szerezhető be indításkor.| 
|lockAcquisitionTimeout|00:01:00|Az a maximális időtartam, ameddig a futtatókörnyezet megpróbál zárolást benyerni.| 
|lockAcquisitionPollingInterval|n.a.|A zárolási beszerzési kísérletek közötti időköz.| 

## <a name="version"></a>version

Ez az érték a host.jssémájának verzióját jelzi. A `"version": "2.0"` v2-es futtatókörnyezetet vagy újabb verziót célzó Function alkalmazás verziójának karakterlánca szükséges. A (z) v2 és v3 közötti séma-változások esetében nincsenek host.jsek.

## <a name="watchdirectories"></a>watchDirectories

A módosításokat figyelő [megosztott kód-címtárak](functions-reference-csharp.md#watched-directories) készlete.  Gondoskodik arról, hogy a könyvtárakban lévő kódok változásakor a függvények a módosításokat is felveszik.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

Egy vagy több olyan fájl nevét tartalmazó tömb, amelyet az alkalmazás újraindítását igénylő módosítások esetében figyelnek.  Ez garantálja, hogy ha a fájlokban megváltoznak a kód, a rendszer a frissítéseket a függvények által felvette.

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>Értékek felülbírálása host.js

Előfordulhat, hogy olyan példányok vannak, amelyekben egy adott környezethez tartozó host.jsban szeretné konfigurálni vagy módosítani a megadott beállításokat, anélkül, hogy a host.jsmagát a fájlra módosítaná.  Az értékek meghatározott host.jsfelülbírálásával egyenértékű értéket hozhat létre az alkalmazás-beállításként. Ha a futtatókörnyezet megkeresi az alkalmazás formátumát `AzureFunctionsJobHost__path__to__setting` , felülbírálja az egyenértékű host.jsa `path.to.setting` JSON-ban található beállításban. Ha alkalmazás-beállításként van kifejezve, a `.` JSON-hierarchia jelzésére használt pont () egy dupla aláhúzás () karakterrel lesz helyettesítve `__` . 

Tegyük fel például, hogy helyileg futtatta az alkalmazás-betekintési mintavételt. Ha módosította a fájl helyi host.jsét a Application Insights letiltásához, akkor előfordulhat, hogy a módosítást a rendszer az üzembe helyezés során leküldi az éles alkalmazásnak. Ennek biztonságosabbá tételéhez inkább hozzon létre egy alkalmazás `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` -beállítást a `local.settings.json` fájlban. Ezt a következő fájlban tekintheti meg `local.settings.json` , amely nem jelenik meg:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató a host.jsfájl frissítéséhez](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Lásd: globális beállítások a környezeti változókban](functions-app-settings.md)
