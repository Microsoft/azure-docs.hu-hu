---
title: Ismerkedés az Azure IoT Hub-eszközkezelés (node) szolgáltatással | Microsoft Docs
description: Távoli eszköz újraindításának kezdeményezése IoT Hub-eszközkezelés használatával. Az Azure IoT SDK for Node.js egy olyan szimulált eszköz alkalmazásának megvalósítására szolgál, amely közvetlen metódust és egy közvetlen metódust meghívó szolgáltatási alkalmazást tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91252017"
---
# <a name="get-started-with-device-management-nodejs"></a>Ismerkedés az Eszközkezelővel (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A [Azure Portal](https://portal.azure.com) használatával hozzon létre egy IoT hub, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy Node.js Console-alkalmazást, amely meghívja a IoT hub-n keresztül a szimulált eszközön lévő újraindítási közvetlen metódust.

Az oktatóanyag végén két Node.js Console-alkalmazás áll rendelkezésére:

* **dmpatterns_getstarted_device.js**, amely a korábban létrehozott eszköz identitásával csatlakozik az IoT hubhoz, a rendszer újraindítási közvetlen módszert kap, szimulálja a fizikai újraindítást, és az utolsó újraindítás időpontját jelenti.

* **dmpatterns_getstarted_service.js**, amely közvetlen metódust hív meg a szimulált eszköz alkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0. x vagy újabb verzió. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol

* Szimulált eszköz újraindításának elindítása

* A jelentett tulajdonságok használatával engedélyezheti az eszköz kettős lekérdezéseit az eszközök azonosításához és a legutóbbi újraindításkor

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A **manageddevice** mappában a parancssorban futtassa a következő parancsot az **Azure-IOT-Device** eszközoldali SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.js** fájlt a **manageddevice** mappában.

4. Adja hozzá a következő "require" utasítást a **dmpatterns_getstarted_device.js** fájl elejéhez:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le a `{yourdeviceconnectionstring}` helyőrző értékét a korábban a [IoT hub új eszközének regisztrálása](#register-a-new-device-in-the-iot-hub)során átmásolt eszköz-csatlakoztatási karakterláncra.  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő függvényt a közvetlen metódus megvalósításához az eszközön

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Nyissa meg a IoT hub-beli kapcsolódást, és indítsa el a közvetlen metódus-figyelőt:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Mentse és zárjuk be a **dmpatterns_getstarted_device.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy Node.js Console-alkalmazást hoz létre, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz kettős lekérdezéseit használva észleli az adott eszköz utolsó újraindításának idejét.

1. Hozzon létre egy **triggerrebootondevice** nevű üres mappát. A **triggerrebootondevice** mappában hozzon létre egy package.jsfájlt a következő parancs futtatásával a parancssorban. Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A **triggerrebootondevice** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** Device SDK csomag és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerrebootondevice** mappában.

4. Adja hozzá a következő "require" utasítást a **dmpatterns_getstarted_service.js** fájl elejéhez:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adja hozzá a következő változó deklarációkat, és cserélje le a `{iothubconnectionstring}` helyőrző értékét a korábban átmásolt IoT hub-karakterláncra a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adja hozzá a következő függvényt az eszköz metódusának meghívásához a cél eszköz újraindításához:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adja hozzá a következő függvényt az eszköz lekérdezéséhez és az utolsó újraindítás időpontjának lekéréséhez:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Adja hozzá a következő kódot a reboot Direct metódust indító függvények meghívásához, valamint az utolsó újraindítás időpontjának lekérdezéséhez:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Mentse és zárjuk be a **dmpatterns_getstarted_service.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **manageddevice** mappában a parancssorban futtassa a következő parancsot, hogy megkezdje az újraindítási közvetlen metódus figyelését.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. A **triggerrebootondevice** mappában található parancssorban futtassa a következő parancsot a Távoli újraindítás és a lekérdezés elindításához a Twin eszközre, és keresse meg az utolsó újraindítási időt.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Megjelenik az eszköz válasza a Direct reboot metódusra és az újraindítás állapotra a konzolon.

   Az alábbi ábrán látható az eszköz válasza a szolgáltatás által küldött újraindítási közvetlen metódusra:

   ![manageddevice-alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/device.png)

   Az alábbi táblázatban látható, hogy a szolgáltatás aktiválja az újraindítást, és lekérdezi az eszköz ikerét az utolsó újraindítás időpontjában:

   ![triggerrebootondevice-alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
