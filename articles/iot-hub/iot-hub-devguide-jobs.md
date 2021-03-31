---
title: Az Azure IoT Hub-feladatok ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az IoT hub-hoz csatlakoztatott több eszközön futtatandó feladatok ütemezése. A feladatok frissíthetik a címkéket és a kívánt tulajdonságokat, és több eszközön is meghívhatnak közvetlen metódusokat.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730108"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Feladatok ütemezése több eszközön

Az Azure IoT Hub több építőelemet is lehetővé tesz, például az [eszközök Twin tulajdonságait, a címkéket](iot-hub-devguide-device-twins.md) és a [közvetlen metódusokat](iot-hub-devguide-direct-methods.md). A háttérbeli alkalmazások általában lehetővé teszik az eszközök rendszergazdái és üzemeltetői számára, hogy tömegesen és ütemezett időpontban frissítsenek és használhassák a IoT-eszközöket. A feladatok egy adott időpontban futtatják az eszköz kettős frissítéseit és közvetlen metódusait. Egy operátor például olyan háttér-alkalmazást használ, amely a 43-es és a 3. emeleten található eszközök egy készletének újraindítását kezdeményezi és nyomon követi egy olyan időpontban, amely nem zavarja az épület műveleteit.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Érdemes lehet feladatokat használni, ha a következő tevékenységek valamelyikét kell ütemeznie és nyomon követnie egy adott eszközön:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

## <a name="job-lifecycle"></a>Feladatok életciklusa

A feladatokat a megoldás háttérbe állításával és IoT Hub tartja karban. Egy szolgáltatáshoz kapcsolódó URI-n () keresztül kezdeményezheti a feladatokat `PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30` , és egy szolgáltatáshoz kapcsolódó URI-n () keresztül végezheti el a végrehajtást egy végrehajtási feladatokon `GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30` . Ha egy feladat elindítása után szeretné frissíteni a futó feladatok állapotát, futtassa a feladat lekérdezését.

> [!NOTE]
> Ha elindít egy feladatot, a tulajdonságok nevei és értékei csak az US-ASCII nyomtatható alfanumerikus karaktereket tartalmazhatják, kivéve a következő készletben lévőket: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Közvetlen metódusok végrehajtásához szükséges feladatok

A következő kódrészlet a HTTPS 1,1-kérelem részleteit mutatja be, amelyekkel egy adott eszközhöz tartozó [közvetlen metódust](iot-hub-devguide-direct-methods.md) hajthat végre egy adott feladattal:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
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

A lekérdezési feltétel a következő példákban látható módon egyetlen eszköz AZONOSÍTÓján vagy az eszközök azonosítóinak listájánál is megadható:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md) további részleteket tartalmaz IoT hub lekérdezési nyelvre.

Az alábbi kódrészlet egy testMethod nevű közvetlen metódus meghívására, valamint a contoso-hub-1 összes eszközén egy ütemezett feladatokra vonatkozó kérést és választ jeleníti meg:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Az eszköz kettős tulajdonságainak frissítésére szolgáló feladatok

A következő kódrészlet a HTTPS 1,1-kérelem részleteit mutatja be az eszköz Twin tulajdonságainak a feladattal való frissítéséhez:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> A *updateTwin* tulajdonsághoz érvényes ETAG-egyezés szükséges; például: `etag="*"` .

Az alábbi kódrészlet egy ütemezett feladatokra vonatkozó kérést és választ jelenít meg a test-Device kettős tulajdonságainak frissítéséhez a contoso-hub-1 eszközön:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Feladatok végrehajtásának lekérdezése

A következő kódrészlet a HTTPS 1,1-kérelem részleteit mutatja a feladatok lekérdezéséhez:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

A válaszból a Continuationtoken argumentumot használja van megadva.

Minden eszközön lekérdezheti a feladat-végrehajtási állapotot az [eszközökhöz készült ikrek, feladatok és üzenet-útválasztás IoT hub lekérdezési nyelvének](iot-hub-devguide-query-language.md)használatával.

## <a name="jobs-properties"></a>Feladatok tulajdonságai

A következő lista a tulajdonságokat és a megfelelő leírásokat tartalmazza, amelyek felhasználhatók feladatok vagy feladatok eredményeinek lekérdezéséhez.

| Tulajdonság | Leírás |
| --- | --- |
| **jobId** |A feladatokhoz megadott alkalmazás azonosítója. |
| **startTime** |Az alkalmazás megadott kezdési időpontja (ISO-8601) a feladatokhoz. |
| **endTime** |IoT Hub megadott dátum (ISO-8601) a feladathoz tartozó művelet befejezésekor. Csak akkor érvényes, ha a feladattípus elérte a "befejezett" állapotot. |
| **típusa** |A feladatok típusai: |
| | **scheduleUpdateTwin**: a kívánt tulajdonságok vagy címkék egy csoportjának frissítésére szolgáló feladatok. |
| | **scheduleDeviceMethod**: az eszköz metódusának meghívására használt feladatok az eszközök egy adott készletén. |
| **állapota** |A feladatokhoz tartozó aktuális állapot. Állapot lehetséges értékei: |
| | **függőben**: ütemezett és várakozás, hogy a feladatütemezés felvegye a szolgáltatást. |
| | **ütemezve**: egy jövőbeli időszakra ütemezve. |
| | **Futtatás**: jelenleg aktív feladatok. |
| | **megszakítva**: a feladat meg lett szakítva. |
| | **sikertelen**: a feladatok sikertelenek voltak. |
| | **befejezett**: a feladatok befejeződtek. |
| **deviceJobStatistics** |A feladatok végrehajtásával kapcsolatos statisztikák. |
| | **deviceJobStatistics** tulajdonságai: |
| | **deviceJobStatistics. eszközök száma**: a feladatokban lévő eszközök száma. |
| | **deviceJobStatistics. failedCount**: azon eszközök száma, amelyeken a feladatok sikertelenek voltak. |
| | **deviceJobStatistics. succeededCount**: azon eszközök száma, amelyeken a feladatok sikeresek voltak. |
| | **deviceJobStatistics. runningCount**: a feladatot jelenleg futtató eszközök száma. |
| | **deviceJobStatistics. pendingCount**: a feladatok futtatására váró eszközök száma. |

### <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* [IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző végpontokat, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és a kvóták](iot-hub-devguide-quotas-throttling.md) a IoT hub szolgáltatásra vonatkozó kvótákat és a szolgáltatás használatakor várható szabályozási viselkedést ismertetik.

* Az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md) -k felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* Az [ikrek, a feladatok és az üzenet-útválasztás IoT hub lekérdezési nyelve](iot-hub-devguide-query-language.md) a IoT hub lekérdezési nyelvet ismerteti. Ezzel a lekérdezési nyelvvel adatokat kérhet le a IoT Hub az eszköz ikrekről és feladatokról.

* [IOT hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagot:

* [Feladatok ütemezése és szórása](iot-hub-node-node-schedule-jobs.md)