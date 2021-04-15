---
title: Az eszközkezelési Azure IoT Hub (Node) első | Microsoft Docs
description: Az eszközfelügyelet IoT Hub távoli eszköz-újraindítás kezdeményezéséhez. Az Azure IoT SDK for Node.js egy közvetlen metódust és a közvetlen metódust meghívó szolgáltatásalkalmazást tartalmazó szimulált eszközalkalmazást implementálja.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 557cdc5fb71f998dc0b69e52148cc93da7cc8ea7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477978"
---
# <a name="get-started-with-device-management-nodejs"></a>Első lépések az eszközkezelésben (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A [Azure Portal](https://portal.azure.com) hozzon létre egy IoT Hub és hozzon létre egy eszközidentitást az IoT Hubban.

* Hozzon létre egy szimulált eszközalkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok a felhőből vannak meghívva.

* Hozzon létre Node.js konzolalkalmazást, amely a szimulált eszközalkalmazás közvetlen újraindítási metódusát hívja meg az IoT Hubon keresztül.

Az oktatóanyag végén két új konzolalkalmazással Node.js fog:

* **dmpatterns_getstarted_device.js,** amely csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, egy közvetlen újraindítási metódust fogad, fizikai újraindítást szimulál, és jelenti az utolsó újraindítás idejét.

* **dmpatterns_getstarted_service.js** egy közvetlen metódust hív meg a szimulálteszköz-alkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verziót. [A fejlesztési környezet előkészítését ismertető](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) cikk ismerteti, hogyan telepítheti Node.js az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetekben ez a port le van tiltva. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT Hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol

* Szimulált eszköz újraindításának aktiválása

* A jelentett tulajdonságok használatával engedélyezheti az ikereszköz-lekérdezések számára az eszközök azonosítását, valamint az utolsó újraindításukkor

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A **manageddevice** mappában a parancssorban futtassa a következő parancsot az **azure-iot-device** eszközoldali SDK-csomag és **az azure-iot-device-mqtt csomag telepítéséhez:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre **egydmpatterns_getstarted_device.js** fájlt a **manageddevice mappában.**

4. Adja hozzá a következő "require" utasításokat admpatterns_getstarted_device.js **elején:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le a helyőrző értékét a korábban az Új eszköz regisztrálása az IoT Hubban helyről korábban `{yourdeviceconnectionstring}` [kimásott eszközkapcsolati sztringre.](#register-a-new-device-in-the-iot-hub)  

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

7. Nyissa meg az IoT Hub kapcsolatát, és indítsa el a közvetlen metódus listenerét:

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

8. Mentse és zárja be **admpatterns_getstarted_device.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási szabályzatokat (például exponenciális le- és leépítést) kell implementálni az átmeneti hibák kezelésével kapcsolatos cikkben [javasolt módon.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati sztring lekérte

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban egy Node.js konzolalkalmazást hoz létre, amely távoli újraindítást kezdeményez egy eszközön egy közvetlen módszerrel. Az alkalmazás ikereszköz-lekérdezésekkel deríti fel az eszköz legutóbbi újraindítási idejét.

1. Hozzon létre egy **triggerrebootondevice nevű üres mappát.** A **triggerrebootondevice** mappában hozzon létre egy package.jsa fájlban a következő paranccsal a parancssorban. Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A **triggerrebootondevice** mappában a parancssorban futtassa a következő parancsot az **azure-iothub** eszközoldali SDK-csomag és az **azure-iot-device-mqtt csomag telepítéséhez:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre **egydmpatterns_getstarted_service.js** fájlt a **triggerrebootondevice mappában.**

4. Adja hozzá a következő "require" utasításokat admpatterns_getstarted_service.js **elején:**

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adja hozzá a következő változódeklarációkat, és cserélje le a helyőrző értékét a korábban az IoT Hub kapcsolati sztring letöltőjében `{iothubconnectionstring}` [kimásott IoT Hub kapcsolati sztringre:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adja hozzá a következő függvényt az eszköz metódusának meghívásával a céleszköz újraindítása érdekében:

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

7. Adja hozzá a következő függvényt az eszköz lekérdezéséhez és az utolsó újraindítási időpont lekérdezéséhez:

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

8. Adja hozzá a következő kódot a közvetlen újraindítási metódust aktiváló függvények hívásához és az utolsó újraindítási időpont lekérdezéséhez:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Mentse és zárja be **admpatterns_getstarted_service.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **manageddevice** mappában a parancssorban futtassa a következő parancsot, hogy elkezdje figyelni a közvetlen újraindítási metódust.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. A **triggerrebootondevice** mappában a parancssorban futtassa a következő parancsot a távoli újraindítás aktiválására és az ikereszköz lekérdezésére az utolsó újraindítási időpont megkereséseként.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Az eszköz válasza a közvetlen újraindítási metódusra és az újraindítási állapotra a konzolon látható.

   Az alábbiakban látható az eszköz válasza a szolgáltatás által küldött közvetlen újraindítási metódusra:

   ![manageddevice alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/device.png)

   Az alábbiakban látható, hogy a szolgáltatás elindítja az újraindítást, és lekérdezi az ikereszközt az utolsó újraindítási időpontra:

   ![triggerrebootondevice alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
