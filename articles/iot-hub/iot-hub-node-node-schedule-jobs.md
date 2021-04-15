---
title: Feladatok ütemezése Azure IoT Hub (Node) | Microsoft Docs
description: Hogyan ütemezhet egy Azure IoT Hub, hogy több eszközön meghívjon egy közvetlen metódust. Az Azure IoT SDK-Node.js implementálja a szimulált eszközalkalmazásokat és egy szolgáltatásalkalmazást a feladat futtatásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc0ea9817b9cbc27816354c48abbe26d79a83f04
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477894"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Feladatok ütemezése és szórása (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a háttéralkalmazás eszközök millióit ütemező és frissítő feladatokat hozzon létre és kövessen nyomon.  A feladatok a következő műveletekhez használhatók:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Elméleti szinten a feladat becsomagolja ezen műveletek valamelyikét, és nyomon követi a végrehajtás előrehaladását egy ikereszköz-lekérdezés által meghatározott eszközkészleten.  A háttéralkalmazások például egy feladat használatával meghívhatnak egy újraindítási metódust 10 000 eszközön, ikereszköz-lekérdezéssel megadva és egy későbbi időpontra ütemezve. Az alkalmazás ezután nyomon tudja követni a folyamat előrehaladását, ahogy az egyes eszközök megkapják és végrehajtják az újraindítási metódust.

Ezekről a képességekről a következő cikkekben talál további információt:

* Ikereszköz és tulajdonságok: Első lépések az [ikereszközekkel és](iot-hub-node-node-twin-getstarted.md) oktatóanyag: [Az ikertulajdonságok használata](tutorial-device-twins.md)

* Közvetlen módszerek: [IoT Hub útmutató – közvetlen metódusok](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen metódusok](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Node.js eszközalkalmazást, amely közvetlen metódussal rendelkezik, amely engedélyezi a **lockDoort,** amelyet a megoldás háttéralkalmazása hív meg.

* Hozzon létre egy Node.js konzolalkalmazást, amely egy feladat használatával hívja meg a **lockDoor** közvetlen metódust a szimulált eszközalkalmazásban, és egy eszköz feladatával frissíti a kívánt tulajdonságokat.

Az oktatóanyag végén két alkalmazással fog Node.js:

* **simDevice.js,** amely csatlakozik az IoT Hubhoz az eszköz identitásával, és egy **lockDoor közvetlen** metódust kap.

* **scheduleJobService.js** egy közvetlen metódust hív meg a szimulált eszközalkalmazásban, és egy feladat használatával frissíti az ikereszköz kívánt tulajdonságait.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verziót. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepíthet Node.js ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT Hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy Node.js konzolalkalmazást hoz létre, amely válaszol a felhő által hívott közvetlen metódusra, amely aktivál egy szimulált **lockDoor metódust.**

1. Hozzon létre egy **új, simDevice nevű üres mappát.**  A **simDevice mappában** hozzon létre egy package.jsa fájlban a következő paranccsal a parancssorban.  Fogadja el az összes alapértelmezett beállítást:

   ```console
   npm init
   ```

2. A **simDevice** mappában a parancssorban futtassa a következő parancsot az **azure-iot-device** eszközoldali SDK-csomag és **az azure-iot-device-mqtt csomag telepítéséhez:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Egy szövegszerkesztővel hozzon létre egy új **simDevice.js** a **simDevice mappában.**

4. Adja hozzá a következő "require" utasításokat asimDevice.js **elején:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. Cserélje le `{yourDeviceConnectionString}` a helyőrző értékét a korábban kimásott eszközkapcsolati sztringre.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt a **lockDoor metódus kezeléshez.**

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

7. Adja hozzá a következő kódot a **lockDoor** metódus kezelője regisztráláshoz.

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

8. Mentse és zárja be **asimDevice.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási szabályzatokat (például exponenciális le- és leépítést) kell implementálni az átmeneti hibák kezelésével kapcsolatos cikkben [javasolt módon.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati sztring lekérte

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezése közvetlen metódusok hívása és az ikereszköz tulajdonságainak frissítése érdekében

Ebben a szakaszban egy Node.js konzolalkalmazást hoz létre, amely távoli **zárolást** kezdeményez egy eszközön egy közvetlen metódussal, és frissíti az ikereszköz tulajdonságait.

1. Hozzon létre egy **új, scheduleJobService nevű üres mappát.**  A **scheduleJobService mappában** hozzon létre egy package.jsa fájlban a következő paranccsal a parancssorban.  Fogadja el az összes alapértelmezett beállítást:

    ```console
    npm init
    ```

2. A **scheduleJobService** mappában a parancssorban futtassa a következő parancsot az **azure-iothub** eszközoldali SDK-csomag és **az azure-iot-device-mqtt csomag** telepítéséhez:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.js** a **scheduleJobService mappában.**

4. Adja hozzá a következő "require" utasításokat ascheduleJobService.js **elején:**

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adja hozzá a következő változódeklarációkat. Cserélje le a helyőrző értékét a Get the IoT Hub connection string (IoT Hub kapcsolati sztring `{iothubconnectionstring}` [letöltője) helyére.](#get-the-iot-hub-connection-string) Ha a **myDeviceId-től** eltérő eszközt regisztrált, mindenképpen módosítsa a lekérdezési feltételben.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt, amely a feladat végrehajtásának figyelése során használatos:

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

7. Adja hozzá a következő kódot az eszköz metódusát behívó feladat ütemezéséhez:
  
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

8. Adja hozzá a következő kódot az ikereszköz frissítésének ütemezéséhez:

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

9. Mentse és zárja be **ascheduleJobService.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **simDevice** mappában a parancssorban futtassa a következő parancsot, hogy elkezdje figyelni a közvetlen újraindítási metódust.

    ```console
    node simDevice.js
    ```

2. A **scheduleJobService** mappában a parancssorban futtassa a következő parancsot az ajtó zárolására és az iker frissítésére vonatkozó feladatok aktiválására

    ```console
    node scheduleJobService.js
    ```

3. Az eszköz válasza a közvetlen metódusra, a feladat állapota pedig a konzolon látható.

   Az alábbiakban látható az eszköz válasza a közvetlen metódusra:

   ![Szimulálteszköz-alkalmazás kimenete](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Az alábbiakban a közvetlen metódus és az ikereszköz frissítéséhez szükséges szolgáltatásütemezési feladatok, valamint a befejezésig futó feladatok láthatóak:

   ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy feladatot használt egy közvetlen metódus egy eszközre való ütemezéséhez és az ikereszköz tulajdonságainak frissítéséhez.

A belső vezérlőprogram és IoT Hub mintákkal, például a belső vezérlőprogram frissítésével kapcsolatos további első lépésekért lásd: Oktatóanyag: Belső vezérlőprogram [frissítésének útmutatója.](tutorial-firmware-update.md)

Az első lépések IoT Hub [lásd: Ismerkedés a Azure IoT Edge.](../iot-edge/quickstart-linux.md)
