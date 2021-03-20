---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (node) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről az Node.js Azure IoT SDK-k használatával. Módosít egy szimulált eszközt a felhőből az eszközre irányuló üzenetek fogadására és a felhőből az eszközre irányuló üzenetek küldésére szolgáló háttérbeli alkalmazás módosítására.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91446873"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>A felhőből az eszközre irányuló üzenetek küldése IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-node.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md)épít. A következőket mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.
* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.
* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről a [IoT hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Node.js Console-alkalmazást futtat:

* A **SimulatedDevice** az alkalmazás egy olyan módosított verziója, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md), amely csatlakozik a IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* A **SendCloudToDeviceMessage**, amely egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz platformjának és nyelvének (például C, Java, Python és JavaScript) támogatásával rendelkezik az Azure IoT Device SDK-k használatával. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hub az Azure [IoT fejlesztői központját](https://azure.microsoft.com/develop/iot)tekintheti meg.
>

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0. x vagy újabb verzió. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban módosítania kell a szimulált eszközt, amelyet a [telemetria küldése eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md) hozott létre, hogy fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. Szövegszerkesztő használatával nyissa meg a **SimulatedDevice.js** fájlt. Ez a fájl a **iot-hub\Quickstarts\simulated-device** Node.js mappában található, a [telemetria küldése az eszközről egy IOT hub](quickstart-send-telemetry-node.md) gyors üzembe helyezési mintakód mappájából.

2. Regisztrálja a kezelőt az eszköz ügyfelével a IoT Hub küldött üzenetek fogadásához. Adja hozzá a hívást úgy, hogy `client.on` az az eszköz ügyfelét létrehozó sort az alábbi kódrészlettel hozza létre:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

Ebben a példában az eszköz meghívja a **Complete** függvényt, hogy értesítse IoT hub arról, hogy feldolgozta az üzenetet, és hogy biztonságosan el tudja távolítani az eszköz-várólistából. A **befejezési** hívás nem szükséges, ha MQTT-átvitelt használ, és nem lehet kihagyni. Ez a AMQP és a HTTPS esetében szükséges.

A AMQP és a HTTPS használatával, de nem MQTT, az eszköz a következőket is elvégezheti:

* Hagyjon ki egy üzenetet, amely IoT Hub az üzenet megtartását az eszköz várólistájában a későbbi felhasználás érdekében.
* Egy üzenet elutasítása, amely véglegesen eltávolítja az üzenetet az eszköz várólistáról.

Ha valami történik, amely megakadályozza, hogy az eszköz elvégezze, lemondsa vagy elutasítja az üzenetet, IoT Hub a rögzített időtúllépési időszak után az üzenetet újra kézbesíti. Emiatt az *idempotens* logikának kell lennie az eszköz alkalmazásában, hogy ugyanazt az üzenetet kapja többször is ugyanez az eredmény.

További információ arról, hogy a IoT Hub hogyan dolgozza fel a felhőből az eszközre irányuló üzeneteket, beleértve a felhőből az eszközre irányuló üzenetek életciklusának részleteit is: a [felhőből az eszközre irányuló üzenetek küldése az IoT hub-ból](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Ha MQTT vagy AMQP helyett HTTPS-t használ, akkor a **DeviceClient** -példányok ritkán keresnek IoT hub üzeneteket (legalább 25 percenként). További információ a MQTT, a AMQP és a HTTPS-támogatás közötti különbségekről: a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md) és [a kommunikációs protokoll kiválasztása](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy Node.js Console-alkalmazást hoz létre, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak. Szüksége lesz annak az eszköznek az eszköz-AZONOSÍTÓJÁRA, amelyet a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-node.md) -gyors üzembe helyezéshez adott meg. Szüksége lesz a korábban átmásolt IoT hub-kapcsolatok karakterláncára is a [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében.

1. Hozzon létre egy **sendcloudtodevicemessage** nevű üres mappát. A **sendcloudtodevicemessage** mappában hozzon létre egy package.jsfájlt a következő parancs futtatásával a parancssorban. Fogadja el az összes alapértelmezett beállítást:

    ```shell
    npm init
    ```

2. A **sendcloudtodevicemessage** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** csomag telepítéséhez:

    ```shell
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage.js** fájlt a **sendcloudtodevicemessage** mappában.

4. Adja hozzá a következő `require` utasításokat a **SendCloudToDeviceMessage.js** fájl elejéhez:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adja hozzá a következő kódot **SendCloudToDeviceMessage.js** fájlhoz. Cserélje le a "{IOT hub-beli kapcsolatok karakterlánc}" és "{Device id}" helyőrző értékeket az IoT hub-beli kapcsolatok karakterláncára és a korábban feljegyzett eszköz AZONOSÍTÓra:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adja hozzá a következő függvényt a művelet eredményeinek a konzolra való nyomtatásához:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adja hozzá a következő függvényt a kézbesítési visszajelzési üzenetek kinyomtatásához a konzolon:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adja hozzá a következő kódot, hogy üzenetet küldjön az eszköznek, és kezelje a visszajelzési üzenetet, amikor az eszköz tudomásul veszi a felhőből az eszközre irányuló üzenetet:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **SendCloudToDeviceMessage.js** fájl mentése és lezárása.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **szimulált eszköz** mappában a parancssorban futtassa a következő parancsot, hogy telemetria küldjön a IoT Hubnek, és figyelje a felhőből az eszközre irányuló üzeneteket:

    ```shell
    node SimulatedDevice.js
    ```

    ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-node-node-c2d/receivec2d.png)

2. A **sendcloudtodevicemessage** mappában egy parancssorban futtassa a következő parancsot egy felhőből az eszközre irányuló üzenet küldéséhez, és várjon a visszaigazolási visszajelzésre:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldéséhez.](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például az exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://azure.microsoft.com/documentation/suites/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
