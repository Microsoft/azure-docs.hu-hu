---
title: Az Azure IoT Hub (Node) szolgáltatással feladatok ütemezéséhez |} A Microsoft Docs
description: Hogyan lehet a több eszközre közvetlen metódus meghívása egy Azure IoT Hub-feladat ütemezése. Az Azure IoT SDK for Node.js használatával valósítható meg a szimulált eszközalkalmazások és a egy service-alkalmazás a feladat futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: af35a84c299544e43988547771ddce75fd71bd90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065786"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Feladatok ütemezése és kiküldése (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely lehetővé teszi egy háttér-alkalmazást hozhat létre, és nyomon követheti a feladatok ütemezése és több millió eszköz frissítéséhez.  Feladatok is használható a következő műveleteket:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

Elméleti szinten feladat burkolja az alábbi műveletek egyikét, és nyomon követi a folyamatot a végrehajtás egy eszköz ikereszköz-lekérdezés által definiált eszközök készlete alapján.  Például egy háttér-alkalmazás használatával egy feladat újraindítás metódus meghívása az 10 000 eszköz, egy eszköz ikereszköz-lekérdezés által megadott és a egy későbbi időpontra ütemezve. Az alkalmazás nyomon követésével folyamat ezeknek az eszközöknek mindegyike kap, és hajtsa végre az újraindítási módszert.

További információ az egyes képességek a következő cikkeket:

* Ikereszköz és tulajdonságok: [Ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md) és [oktatóanyag: Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)

* Közvetlen metódusok: [Az IoT Hub fejlesztői útmutató – közvetlen metódusok](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen metódusok](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Egy Node.js szimulált eszközalkalmazás létrehozása, amely rendelkezik, amely lehetővé teszi a közvetlen metódus **lockDoor**, amelyek meghívhatók a megoldás háttérrendszeréhez.

* Hozzon létre egy Node.js-konzolalkalmazást, amely meghívja a **lockDoor** a közvetlen metódus a szimulált eszközalkalmazásnak, feladatok és a frissítések használata a kívánt tulajdonságokkal eszköz feladat használatával.

Ez az oktatóanyag végén van két Node.js-alkalmazások:

* **simDevice.js**, amely az IoT hub az eszközidentitással csatlakozik, és megkapja a **lockDoor** közvetlen metódust.

* **scheduleJobService.js**, amely hívások közvetlen metódus a szimulált eszközalkalmazásnak, és frissíti az ikereszköz kívánt tulajdonságait egy feladat használatával.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 10.0.x vagy újabb [a fejlesztési környezet előkészítését](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) ismerteti, hogyan telepítse a Node.js ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást, amely a felhő, amely elindít egy szimulált által meghívott közvetlen metódusra válaszol létrehozása **lockDoor** metódust.

1. Hozzon létre egy új üres nevű **simDevice**.  Az a **simDevice** mappában hozzon létre egy package.json fájlt a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

   ```
   npm init
   ```

2. A parancssorban a **simDevice** mappában futtassa a következő paranccsal telepíthető a **azure-iot-device** eszközoldali SDK csomagot és **azure-iot-device-mqtt** csomag:
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Egy szövegszerkesztővel hozzon létre egy új **simDevice.js** fájlt a **simDevice** mappát.

4. Adja hozzá a következő "szükséges" elején található utasításokat a **simDevice.js** fájlt:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt kezelni a **lockDoor** metódust.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Adja a következő kódot a kezelő regisztrálni a **lockDoor** metódust.

   ```
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Mentse és zárja be a **simDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezése a közvetlen metódus meghívása és a egy ikereszköz tulajdonságainak frissítése

Ebben a szakaszban egy Node.js-konzolalkalmazást, amely kezdeményezi egy távoli létrehozása **lockDoor** egy eszközön a közvetlen metódus használatával, és az ikereszköz tulajdonságainak frissítése.

1. Hozzon létre egy új üres nevű **scheduleJobService**.  Az a **scheduleJobService** mappában hozzon létre egy package.json fájlt a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A parancssorban a **scheduleJobService** mappában futtassa a következő paranccsal telepíthető a **azure-iothub** eszközoldali SDK csomagot és **azure-iot-device-mqtt** csomag:
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.js** fájlt a **scheduleJobService** mappát.

4. Adja hozzá a következő "szükséges" elején található utasításokat a **dmpatterns_gscheduleJobServiceetstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adja hozzá a következő változódeklarációkat, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt, amely a feladat végrehajtásának figyelésére használható:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adja hozzá a következő kódot, amely meghívja ezt a metódust a feladat ütemezése:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Adja hozzá a következő kódot az ikereszköz frissíteni a feladat ütemezése:
   
    ```
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Mentse és zárja be a **scheduleJobService.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **simDevice** mappában futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyel.
   
    ```
    node simDevice.js
    ```

2. A parancssorban a **scheduleJobService** mappában futtassa a következő parancsot a ajtajának, és frissíti az ikereszköz a feladatok indításához
   
    ```
    node scheduleJobService.js
    ```

3. Láthatja, hogy az eszköz válasza a közvetlen metódus a konzolon.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat ütemezése és eszköz az ikereszköz tulajdonságok frissítése egy közvetlen metódus használt.

Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról keresztül a vezeték nélküli belső vezérlőprogram frissítését a folytatáshoz tekintse meg a [oktatóanyag: Belső vezérlőprogram frissítése annak](tutorial-firmware-update.md).

Ismerkedés az IoT Hub a folytatáshoz tekintse meg a [– első lépések az Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
