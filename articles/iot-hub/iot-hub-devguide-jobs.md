---
title: Azure IoT Hub-feladatok ismertetése |} A Microsoft Docs
description: Az IoT hub fejlesztői útmutató – több eszközön futtatandó feladatok ütemezése csatlakoztatva. Feladatok frissítheti a címkéket és kívánt tulajdonságok és több eszközön közvetlen metódusok meghívása.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: dobett
ms.openlocfilehash: b9ad7a0e1947c9ca95b343a443688e976c306f95
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884224"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT Hub lehetővé teszi, hogy a építőelemek, például számos [eszköz-ikertulajdonságok és címkék](iot-hub-devguide-device-twins.md) és [közvetlen metódusok](iot-hub-devguide-direct-methods.md). Általában a háttér-alkalmazások engedélyezése eszközadminisztrátorok és üzemeltetői, módosíthatja és használhatja az IoT-eszközök tömeges és a egy megadott időpontban. Feladatok ütemezett időpontban ikereszköz-frissítések és az eszközök ellen közvetlen metódusok végrehajtása. Az operátor például egy háttér-alkalmazást, amely kezdeményezi és követi nyomon egy feladatot, amely az eszközök, amelyek nem lenne zavaró a az épület a műveletek egyszerre 43 és emelet 3 épületben újraindítás használja.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Vegye figyelembe, hogy bármelyikével feladatok ütemezését és nyomon követheti a szolgálatot a következő tevékenységeket meg az eszközök:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

## <a name="job-lifecycle"></a>Feladat életciklusa

Feladatok a megoldás háttérrendszere által kezdeményezett, és az IoT Hub által karbantartott. Egy feladat keresztül a szolgáltatás által használt URI-t is kezdeményezhető (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`), és folyamatban van egy végrehajtó feladaton keresztül a szolgáltatás által használt URI lekérdezése (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Feladat lekérdezés futtatása a futó feladatok, ha egy feladat állapotának frissítéséhez.

> [!NOTE]
> Ha olyan feladatot kezdeményez, nevét és értékeit tartalmazhat US-ASCII nyomtatható alfanumerikus, kivéve az alábbi: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen metódusok végrehajtása feladatok

Az alábbi kódrészlet bemutatja a HTTPS 1.1 a kérelem részletes adatainak végre egy [közvetlen metódus](iot-hub-devguide-direct-methods.md) meg az eszközök egy feladat használatával:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

A lekérdezési feltétel is lehet egyetlen Eszközazonosítót vagy eszköz a következő példákban szemléltetett módon azonosítók listáját:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) IoT Hub lekérdezési nyelv további részletesen ismerteti.

## <a name="jobs-to-update-device-twin-properties"></a>Eszköz-ikertulajdonságok frissíteni feladatok

A következő kódrészlet azt mutatja be, HTTPS 1.1 kérés részleteinek frissítése egy feladat használatával eszköz-ikertulajdonságok:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>A folyamatban lévő feladatok lekérdezése

Az alábbi kódrészlet bemutatja a feladatok lekérdezése HTTPS 1.1 kérelem részletei:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

A válaszból biztosítja a continuationtoken argumentumot használja.

A feladat végrehajtási állapotát minden egyes eszköz segítségével lekérdezhető a [az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Feladat tulajdonságai

Az alábbi lista a tulajdonságait, és a vonatkozó leírásokat, amely használható lekérdezésekor feladatok vagy a feladat eredményeinek tartalmazza.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |Alkalmazás azonosítója a feladatra vonatkozóan megadott. |
| **startTime** |Alkalmazás a megadott kezdési idő (ISO-8601) a feladathoz. |
| **endTime** |Az IoT Hub dátuma (ISO-8601) amikor a feladat befejeződött-e megadva. Csak azt követően a feladat eléri a "kész" állapot érvényes. |
| **type** |Feladatok típusai: |
| | **scheduledUpdateTwin**: egy feladatot, kívánt tulajdonságok vagy címkék frissítése. |
| | **scheduledDeviceMethod**: ikereszközök különböző eszköz metódus meghívásához használt feladat. |
| **Állapot** |A feladat jelenlegi állapota. Lehetséges értékek a állapota: |
| | **függőben lévő**: ütemezett és váró észlelnie kell a feladatokat végző szolgáltatás. |
| | **ütemezett**: ütemezi későbbi időpontra. |
| | **futó**: a jelenleg aktív feladat. |
| | **meg lett szakítva**: feladat meg lett szakítva. |
| | **nem sikerült**: a feladat nem sikerült. |
| | **Befejezett**: feladat befejeződött. |
| **deviceJobStatistics** |A feladat-végrehajtási statisztikája. |
| | **deviceJobStatistics** tulajdonságai: |
| | **deviceJobStatistics.deviceCount**: a feladat lévő eszközök száma. |
| | **deviceJobStatistics.failedCount**: Ha a feladat nem sikerült az eszközök száma. |
| | **deviceJobStatistics.succeededCount**: készülékek, ahol a feladat sikeresen befejeződött. |
| | **deviceJobStatistics.runningCount**: a feladat jelenleg futó eszközök számát. |
| | **deviceJobStatistics.pendingCount**: a feladat futtatása függőben lévő eszközök száma. |

### <a name="additional-reference-material"></a>További – referenciaanyag

Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.

* [Sávszélesség-szabályozási és kvóták](iot-hub-devguide-quotas-throttling.md) ismerteti a kvótákat, az IoT Hub szolgáltatás és a szabályozás működésék, számítson, ha a szolgáltatás használatához.

* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.

* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása](iot-hub-devguide-query-language.md) ismerteti az IoT Hub lekérdezési nyelv. A lekérdező nyelv használata az IoT Hub az ikereszközökhöz és feladatokhoz kapcsolatos információk lekérése.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub-oktatóanyag:

* [Feladatok ütemezése és szórása](iot-hub-node-node-schedule-jobs.md)