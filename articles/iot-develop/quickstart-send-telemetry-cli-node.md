---
title: Eszköz telemetria küldése az Azure IoT Hub rövid útmutatóba (Node.js)
description: Ebben a rövid útmutatóban az Azure IoT Hub eszközoldali SDK for Node.js használatával küld telemetria az eszközről egy IOT hubhoz.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102197809"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Gyors útmutató: telemetria küldése az eszközről egy IoT hubhoz (Node.js)

**A** következőkre vonatkozik: [eszköz-alkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban egy alapszintű IoT-fejlesztési munkafolyamatot sajátíthat el. Az Azure CLI-vel létrehozhat egy Azure IoT hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK használatával elérheti az eszközt, és elküldheti a telemetria a központba.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben futtatott interaktív CLI-rendszerhéj Azure Cloud Shellával. Ha a Cloud Shell használja, semmit nem kell telepítenie. Ha a parancssori felület helyi használatát szeretné használni, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Ha a Azure Cloud Shell használja, ne frissítse Node.js telepített verzióját. A Azure Cloud Shell már rendelkezik a legújabb Node.js-verzióval.

    A következő parancs használatával ellenőrizze a Node.js aktuális verzióját a fejlesztői gépen:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Üzenetek küldése a Node.js SDK-val
Ebben a szakaszban a Node.js SDK használatával küld üzeneteket a szimulált eszközről az IoT hubhoz. 

1. Nyisson meg egy új terminálablakot. Ezt a terminált fogja használni a Node.js SDK telepítéséhez és Node.js mintakód használatához. Ekkor két terminált kell megnyitnia: az imént megnyitotta, hogy működjön a Node.jskal, és hogy az előző részekben használt CLI-rendszerhéj beírja az Azure CLI-parancsokat. 

1. Másolja az [Azure IoT Node.js SDK-eszközök mintáit](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navigáljon az *Azure-IOT-SDK-Node/Device/Samples* könyvtárba:

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Telepítse az Azure IoT Node.js SDK-t és a szükséges függőségeket:

    ```console
    npm install
    ```
    Ez a parancs telepíti a megfelelő függőségeket, amelyeket az eszköz Samples könyvtáránakpackage.jsfájljában *meg* kell adni.

1. Állítsa be az eszközhöz tartozó kapcsolatok karakterláncát a nevű környezeti változóként `DEVICE_CONNECTION_STRING` . A használni kívánt karakterlánc a szimulált Node.js eszköz létrehozása után az előző szakaszban beszerzett karakterlánc. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > A Windows CMD esetében nincsenek a kapcsolatok sztringjét körülvevő idézőjelek.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. A megnyitott CLI-rendszerhéjban futtassa az az [IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) parancsot a szimulált IOT-eszközön lévő események figyelésének megkezdéséhez.  Az események a terminálon lesznek kinyomtatva, ahogy érkeznek.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. A Node.js-terminálban futtassa a *simple_sample_device.js* telepített sablonfájl kódját. Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT hubhoz.

    A Node.js minta futtatása a terminálról:
    ```console
    node ./simple_sample_device.js
    ```

    Igény szerint futtathatja a Node.js kódot a JavaScript IDE-ből származó mintából:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Mivel a Node.js kód egy szimulált telemetria üzenetet küld az eszközről az IoT hub-ra, az üzenet a CLI-rendszerhéjban jelenik meg, amely figyeli az eseményeket:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Az eszköz mostantól biztonságosan csatlakozik, és telemetria küld az Azure IoT Hubnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Erőforráscsoport törlése név alapján:
1. Futtassa az az [Group delete](/cli/azure/group#az-group-delete) parancsot. Ezzel a paranccsal eltávolítható az erőforráscsoport, a IoT Hub és a létrehozott eszköz regisztrálása.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa az az [Group List](/cli/azure/group#az-group-list) parancsot az erőforráscsoport törlésének megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás munkafolyamatát ismertette az eszköz felhőbe való biztonságos csatlakoztatásához és az eszközről a felhőbe irányuló telemetria küldéséhez. Az Azure CLI-vel létrehozta az IoT hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK használatával fér hozzá az eszközhöz, és elküldheti a telemetria a hubhoz. 

A következő lépésként Fedezze fel az Azure IoT Node.js SDK-t az alkalmazási mintákon keresztül.

- [További Node.js minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Ez a könyvtár több mintát tartalmaz az Node.js SDK-tárházból a IoT hub forgatókönyvek bemutatásához.