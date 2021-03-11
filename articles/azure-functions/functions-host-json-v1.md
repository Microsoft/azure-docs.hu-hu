---
title: host.jsAzure Functions 1. x esetén
description: Dokumentáció a v1 futtatókörnyezettel rendelkező fájl Azure Functions host.jsához.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 48dba50b384731befdc7fba7c418e542994cedd9
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608954"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.jsAzure Functions 1. x esetén

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Azure Functions futtatókörnyezet verzióját: "]
> * [1-es verzió](functions-host-json-v1.md)
> * [2-es verzió](functions-host-json.md)

A metaadatokat tartalmazó fájl *host.js* globális konfigurációs beállításokat tartalmaz, amelyek a Function app összes funkcióját érintik. Ez a cikk a v1 futtatókörnyezethez elérhető beállításokat sorolja fel. A JSON-séma a következő helyen található: http://json.schemastore.org/host .

> [!NOTE]
> Ez a cikk az 1. x Azure Functions.  A 2. x és újabb függvények host.jsának hivatkozását a következő témakörben találhatja meg: [host.jsAzure functions 2. x](functions-host-json.md).

Az [alkalmazás beállításaiban](functions-app-settings.md)az egyéb Function app konfigurációs beállításai is kezelhetők.

A beállítások egyes host.jscsak akkor használhatók, ha a fájl [local.settings.js](functions-run-local.md#local-settings-file) helyileg fut.

## <a name="sample-hostjson-file"></a>Példa host.jsfájlra

Az alábbi minta- *host.jsa* fájlokban minden lehetséges beállítás meg van adva.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
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
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

A cikk következő fejezetei ismertetik az egyes legfelső szintű tulajdonságokat. Ha másként nincs megadva, az összes megadása nem kötelező.

## <a name="aggregator"></a>aggregátor

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

A [Azure Cosmos db trigger és kötések](functions-bindings-cosmosdb.md)konfigurációs beállításai.

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|GatewayMode|Átjáró|A függvény által a Azure Cosmos DB szolgáltatáshoz való csatlakozáskor használt kapcsolati mód. A lehetőségek a következők `Direct` , `Gateway`|
|Protokoll|Https|A függvény által a Azure Cosmos DB szolgáltatáshoz való kapcsolódáskor használt kapcsolati protokoll.  A [két mód magyarázata itt](../cosmos-db/performance-tips.md#networking) olvasható|
|leasePrefix|n.a.|Az alkalmazás összes függvényében használandó bérlet-előtag.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Az [Event hub-eseményindítók és-kötések](functions-bindings-event-hubs.md#functions-1x)konfigurációs beállításai.

## <a name="functions"></a>funkciók

A gazdagép által futtatott függvények listája. Az üres tömb az összes függvény futtatását jelenti. Csak [helyileg futtatott](functions-run-local.md)használatra készült. Az Azure-ban a Function apps szolgáltatásban a [függvények letiltása a Azure Functionsben](disable-function.md) című cikkben ismertetett lépéseket követve letilthatja az egyes függvényeket, és nem használhatja ezt a beállítást.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Az összes függvény időtúllépési időtartamát jelzi. A kiszolgáló nélküli fogyasztási csomag esetében az érvényes tartomány 1 másodperc és 10 perc között van, az alapértelmezett érték pedig 5 perc. Egy App Service tervben nincs általános korlát, és az alapértelmezett érték _Null_, amely nincs időkorlát.

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

[Http-eseményindítók és-kötések](functions-bindings-http-webhook.md)konfigurációs beállításai.

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|dynamicThrottlesEnabled|hamis|Ha engedélyezve van, ez a beállítás hatására a kérelmek feldolgozási folyamata rendszeres időközönként ellenőrzi a rendszerteljesítmény-számlálókat, például a kapcsolatok/szálak/folyamatok/memória/CPU/etc értéket. ha ezek a számlálók egy beépített magas küszöbértéken (80%) vannak, a rendszer a kérelmeket a 429 "túl elfoglalt" válaszként utasítja el, amíg a számláló (k) be nem fejeződik a|
|maxConcurrentRequests|nem kötött ( `-1` )|A párhuzamosan végrehajtandó HTTP-függvények maximális száma. Ez lehetővé teszi a párhuzamosság szabályozását, ami segíthet az erőforrások kihasználtságának kezelésében. Előfordulhat például, hogy olyan HTTP-függvénnyel rendelkezik, amely sok rendszererőforrást (memóriát/processzort/szoftvercsatornát) használ, így problémát okoz, ha a párhuzamosság túl magas. Vagy lehet, hogy olyan függvényt használ, amely a kimenő kéréseket egy harmadik féltől származó szolgáltatásnak teszi elérhetővé, és a hívásoknak korlátozott arányban kell lenniük. Ezekben az esetekben a szabályozás alkalmazása segíthet.|
|maxOutstandingRequests|nem kötött ( `-1` )|A függőben lévő kérések maximális száma, amelyek egy adott időpontban vannak tárolva. Ez a korlát olyan kérelmeket tartalmaz, amelyek várólistára kerülnek, de nem indult el, valamint folyamatban van a végrehajtás. Az ezen a korláton túli bejövő kérelmek elutasítása egy 429 "túl elfoglalt" választ tartalmaz. Ez lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segítséget nyújt a kérelmek maximális késésének szabályozásához is. Ez csak a parancsfájl-gazdagép végrehajtási útvonalán belüli üzenetsor-kezelőt vezérli. Más várólisták, például a ASP.NET kérelmek várólistája továbbra is érvényben marad, és ezt a beállítást nem érinti.|
|routePrefix|api|Az útvonal előtagja, amely az összes útvonalra vonatkozik. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |

## <a name="id"></a>id

A feladatok gazdagépének egyedi azonosítója. A kötőjelekkel ellátott kisbetűs GUID lehet. Helyi futtatáskor szükséges. Ha az Azure-ban fut, javasoljuk, hogy ne állítson be azonosító értéket. Az Azure-ban automatikusan létrejön egy azonosító, ha `id` a szolgáltatás nincs megadva. 

Ha több Function-alkalmazás között oszt meg egy Storage-fiókot, győződjön meg arról, hogy az egyes functions-alkalmazások eltérőek `id` . Kihagyhatja a `id` tulajdonságot, vagy manuálisan is beállíthatja az egyes függvények alkalmazásait `id` egy másik értékre. Az időzítő-trigger egy tárolási zárolást használ annak biztosítására, hogy csak egy időzítő példány legyen, ha egy függvény alkalmazás több példányra is méretezhető. Ha két Function-alkalmazás azonos `id` , és mindegyik időzítő-triggert használ, csak egy időzítő fog futni.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Tuskózó

A [ILogger](functions-dotnet-class-library.md#ilogger) objektum vagy a [Context. log](functions-reference-node.md#contextlog-method)által írt naplók szűrését vezérli.

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|categoryFilter|n.a.|Meghatározza a kategória szerinti szűrést| 
|defaultLevel|Tájékoztatás|A tömbben nem megadott kategóriákhoz `categoryLevels` küldje el a naplókat ezen a szinten és Application Insights.| 
|categoryLevels|n.a.|Kategóriákból álló tömb, amely meghatározza, hogy az egyes kategóriákhoz Application Insights milyen minimális naplózási szint legyen elküldve. Az itt megadott kategória az összes olyan kategóriát szabályozza, amely ugyanazzal az értékkel kezdődik, és a hosszabb értékek elsőbbséget élveznek. Az előző példában *host.js* fájlon az összes olyan kategória, amely a "host. aggregator" kifejezéssel kezdődik `Information` . Az összes többi olyan kategória, amely a "host" kifejezéssel kezdődik, például "Host.Executor", jelentkezzen be `Error` szinten.| 

## <a name="queues"></a>üzenetsorok

A [tárolási várólista-eseményindítók és-kötések](functions-bindings-storage-queue.md)konfigurációs beállításai.

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxPollingInterval|60000|A várólista-lekérdezések közötti maximális időköz ezredmásodpercben.| 
|visibilityTimeout|0|Az újrapróbálkozások között eltelt idő az üzenet feldolgozásakor.| 
|batchSize|16|Azoknak a üzenetsor-üzeneteknek a száma, amelyeket a függvények futtatókörnyezete egyszerre kér le, és párhuzamosan dolgozza fel a folyamatokat. A feldolgozás alatt álló szám lekérése után `newBatchThreshold` a futtatókörnyezet egy másik köteget kap, és elindítja az üzenetek feldolgozását. Így a függvények által feldolgozott egyidejű üzenetek maximális száma `batchSize` plusz `newBatchThreshold` . Ez a korlát külön vonatkozik az egyes üzenetsor-vezérelt függvényekre. <br><br>Ha el szeretné kerülni az egy várólistán fogadott üzenetek párhuzamos végrehajtását, beállíthatja `batchSize` az 1 értékre. Ez a beállítás azonban csak akkor teszi feleslegessé a párhuzamosságot, ha a Function alkalmazás egyetlen virtuális gépen fut (VM). Ha a Function alkalmazás több virtuális gépre is kiterjed, minden egyes virtuális gép futtathatja az egyes üzenetsor által aktivált függvények egy példányát.<br><br>A maximális `batchSize` érték 32. | 
|maxDequeueCount|5|Azon alkalmak száma, amelyekkel az üzenetek feldolgozására kerül sor, mielőtt a rendszer áthelyezi azt a Megmérgező várólistára.| 
|newBatchThreshold|batchSize/2|Ha az egyidejűleg feldolgozható üzenetek száma leállítja ezt a számot, a futtatókörnyezet egy másik köteget kérdez le.| 

## <a name="sendgrid"></a>SendGrid

A [SendGrind kimeneti kötésének](functions-bindings-sendgrid.md) konfigurációs beállítása

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|a|n.a.|A küldő e-mail-címe az összes függvényen belül.| 

## <a name="servicebus"></a>serviceBus

[Service Bus eseményindítók és kötések](functions-bindings-service-bus.md)konfigurációs beállítása.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxConcurrentCalls|16|Az üzenet-szivattyú által kezdeményezett visszahívás egyidejű hívásának maximális száma. Alapértelmezés szerint a függvények futtatókörnyezete egyszerre több üzenetet dolgoz fel. Ha úgy szeretné irányítani a futtatókörnyezetet, hogy egyszerre csak egyetlen üzenetsor vagy témakörbeli üzenetet dolgozza fel, állítsa `maxConcurrentCalls` az 1 értékre. | 
|prefetchCount|n.a.|Az alapul szolgáló MessageReceiver által használt alapértelmezett PrefetchCount.| 
|autoRenewTimeout|00:05:00|Az a maximális időtartam, amelyen belül az üzenet zárolása automatikusan meg lesz újítva.|
|Automatikus kiegészítés|true|Igaz értéke esetén az trigger automatikusan végrehajtja az üzenet feldolgozását a művelet sikeres végrehajtásakor. Hamis érték esetén a függvény feladata az üzenet befejezése a visszatérés előtt.|

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

## <a name="tracing"></a>nyomkövetés

*1. x verzió*

Egy objektum használatával létrehozott naplók konfigurációs beállításai `TraceWriter` . További információt a [C# naplózás] című témakörben talál.

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|consoleLevel|információ|A konzol naplózásának nyomkövetési szintje. A lehetőségek a következők:,,, `off` `error` `warning` `info` és `verbose` .|
|fileLoggingMode|debugOnly|A fájlok naplózásának nyomkövetési szintje. A lehetőségek a következők:, `never` `always` `debugOnly` .| 

## <a name="watchdirectories"></a>watchDirectories

A módosításokat figyelő [megosztott kód-címtárak](functions-reference-csharp.md#watched-directories) készlete.  Gondoskodik arról, hogy a könyvtárakban lévő kódok változásakor a függvények a módosításokat is felveszik.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató a host.jsfájl frissítéséhez](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Lásd: globális beállítások a környezeti változókban](functions-app-settings.md)
