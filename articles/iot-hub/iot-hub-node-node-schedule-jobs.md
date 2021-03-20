---
title: Feladatok ütemezhetnek az Azure IoT Hub (node) segítségével | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beütemezés közvetlen metódus több eszközön való meghívásához. A Node.js Azure IoT SDK-k segítségével implementálhatja a szimulált eszköz alkalmazásait és egy szolgáltatási alkalmazást a feladatok futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: e1992c806619154fa7b3c33500b2e54fbc919f20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151430"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Feladatok ütemezett és szórása (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a háttérbeli alkalmazások számára, hogy több millió eszközt ütemezzen és frissítsen feladatok létrehozásához és nyomon követéséhez.  A feladatokat a következő műveletekhez használhatja:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Elméletileg a feladatok az alábbi műveletek egyikét betakarják, és nyomon követik a végrehajtás előrehaladását egy adott eszközön, amelyet az eszközök kettős lekérdezése határoz meg.  Egy háttérbeli alkalmazás például felhasználhat egy feladatot egy újraindítási módszer meghívására 10 000-eszközökön, egy eszköz kettős lekérdezésével és egy későbbi időpontban ütemezve. Az alkalmazás ezután nyomon követheti az előrehaladást, mivel az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

További információk a következő cikkekben felsorolt lehetőségekről:

* Eszközök Twin és Properties: Ismerkedés [az eszközök ikrekkel](iot-hub-node-node-twin-getstarted.md) és [oktatóanyaggal: az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok és [oktatóanyag: közvetlen metódusok](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Node.js szimulált eszköz alkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi a **lockDoor**, amelyet a megoldás hátterében hívhat meg.

* Hozzon létre egy Node.js Console-alkalmazást, amely egy feladatot használva meghívja a **lockDoor** Direct metódust a szimulált eszköz alkalmazásban, és frissíti a kívánt tulajdonságokat egy eszköz feladataival.

Az oktatóanyag végén két Node.js alkalmazást használhat:

* **simDevice.js**, amely az eszköz identitásával csatlakozik az IoT hubhoz, és egy **lockDoor** Direct metódust kap.

* **scheduleJobService.js**, amely közvetlen metódust hív meg a szimulált eszköz alkalmazásban, és feladatokkal frissíti az eszköz két kívánt tulajdonságát.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0. x vagy újabb verzió. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan Node.js Console-alkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol, amely egy szimulált **lockDoor** metódust indít el.

1. Hozzon létre egy új, **simDevice** nevű üres mappát.  A **simDevice** mappában hozzon létre egy package.jsfájlt a következő parancs futtatásával a parancssorban.  Fogadja el az összes alapértelmezett beállítást:

   ```console
   npm init
   ```

2. A **simDevice** mappában a parancssorban futtassa a következő parancsot az **Azure-IOT-Device** eszközoldali SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Egy szövegszerkesztővel hozzon létre egy új **simDevice.js** fájlt a **simDevice** mappában.

4. Adja hozzá a következő "require" utasítást a **simDevice.js** fájl elejéhez:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. Cserélje le a `{yourDeviceConnectionString}` helyőrző értékét a korábban átmásolt eszköz-összekapcsolási sztringre.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt a **lockDoor** metódus kezeléséhez.

    ```javascript
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

7. Adja hozzá a következő kódot a kezelő **lockDoor** metódushoz való regisztrálásához.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Mentse és zárjuk be a **simDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezhetnek közvetlen metódus hívásához és az eszközök Twin tulajdonságainak frissítéséhez

Ebben a szakaszban egy olyan Node.js Console-alkalmazást hoz létre, amely egy közvetlen metódus használatával kezdeményez egy távoli **lockDoor** az eszközön, és frissíti az eszköz Twin tulajdonságait.

1. Hozzon létre egy új, **scheduleJobService** nevű üres mappát.  A **scheduleJobService** mappában hozzon létre egy package.jsfájlt a következő parancs futtatásával a parancssorban.  Fogadja el az összes alapértelmezett beállítást:

    ```console
    npm init
    ```

2. A **scheduleJobService** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** Device SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.js** fájlt a **scheduleJobService** mappában.

4. Adja hozzá a következő "require" utasítást a **scheduleJobService.js** fájl elejéhez:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adja hozzá a következő változó deklarációkat. A helyőrző értékét cserélje le az `{iothubconnectionstring}` [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)elemre. Ha a **myDeviceId** eltérő eszközt regisztrált, ne felejtse el módosítani a lekérdezési feltételben.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt, amely a feladat végrehajtásának figyelésére szolgál:

    ```javascript
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

7. Adja hozzá a következő kódot az eszköz metódusát meghívó feladatokhoz:
  
    ```javascript
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

8. Adja hozzá a következő kódot a feladatok ütemezett frissítéséhez az eszköz kettős frissítése céljából:

    ```javascript
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

9. Mentse és zárjuk be a **scheduleJobService.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simDevice** mappában a parancssorban futtassa a következő parancsot, hogy megkezdje az újraindítási közvetlen metódus figyelését.

    ```console
    node simDevice.js
    ```

2. A **scheduleJobService** mappában található parancssorban futtassa a következő parancsot a feladatok elindításához az ajtó zárolásához és a Twin frissítéséhez.

    ```console
    node scheduleJobService.js
    ```

3. Megjelenik az eszköz válasza a Direct metódusra és a feladatok állapotára a-konzolon.

   Az alábbi ábrán látható az eszköz válasza a közvetlen metódusra:

   ![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   A következő táblázat a közvetlen metódus és az eszköz kettős frissítésének szolgáltatás-ütemezési feladatait, valamint a befejezésre futó feladatokat mutatja be:

   ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Ha továbbra is szeretné megkezdeni a IoT Hub és az eszközkezelés mintáit, például a távoli belső vezérlőprogram frissítését, tekintse meg az [oktatóanyag: a belső vezérlőprogram frissítését](tutorial-firmware-update.md)ismertető témakört.

A IoT Hub első lépéseinek folytatásához tekintse meg a [Azure IoT Edge első lépéseivel](../iot-edge/quickstart-linux.md)foglalkozó témakört.