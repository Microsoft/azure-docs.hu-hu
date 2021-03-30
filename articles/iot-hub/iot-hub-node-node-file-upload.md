---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubba a Node használatával | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe a Node.js Azure IoT eszközoldali SDK használatával. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: db4f78e14696c421adaedd16b0b3f8d598f12846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251898"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag a [felhőből az eszközre küldött üzenetek küldésére](iot-hub-node-node-c2d.md) szolgáló kódot a IoT hub oktatóanyag segítségével mutatja be, amelyből megtudhatja, hogyan tölthet fel fájlokat az [Azure Blob Storage](../storage/index.yml)-ba a [IoT hub fájlfeltöltés funkciójának](iot-hub-devguide-file-upload.md) használatával. Ez az oktatóanyag a következőket mutatja be:

* A fájlok feltöltéséhez biztonságosan biztosítson egy Azure Blob URI-t tartalmazó eszközt.

* A fájl feltöltésével kapcsolatos értesítéseket használva aktiválja a fájlt az alkalmazás hátterében lévő IoT Hub.

Az [eszközről az IoT hub-ra való telemetria küldése](quickstart-send-telemetry-node.md) az eszközről a felhőbe irányuló alapszintű üzenetküldési funkciókat mutatja be IoT hub. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Például:

* Képeket tartalmazó nagyméretű fájlok
* Videók
* Nagy gyakorisággal vett vibrációs adatelemzés
* Az előre feldolgozott adatmennyiségek valamilyen formája.

Ezeket a fájlokat a rendszer általában a felhőben dolgozza fel a felhőben olyan eszközökkel, mint a [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről szeretne fájlokat felvenni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két Node.js Console-alkalmazást futtat:

* **SimulatedDevice.js**, amely feltölt egy fájlt a Storage-ba az IoT hub által biztosított sas URI használatával.

* **ReadFileUploadNotification.js**, amely befogadja a fájlfeltöltés-értesítéseket az IoT hub-ból.

> [!NOTE]
> IoT Hub számos eszköz-platformot és nyelvet támogat (beleértve a C, .NET, JavaScript, Python és Java rendszereket) az Azure IoT Device SDK-k használatával. Tekintse meg az [Azure IoT fejlesztői központ] című témakört, amely részletesen ismerteti, hogyan csatlakoztatható az eszköz az Azure IoT Hubhoz.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0. x vagy újabb verzió. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban létrehozza az eszköz alkalmazást, hogy feltöltse a fájlt az IoT hub-ba.

1. Hozzon létre egy ```simulateddevice``` nevű üres mappát.  A(z) ```simulateddevice``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-mqtt** csomagot. Ehhez futtassa egy parancssorból a következő parancsot a(z) ```simulateddevice``` mappában:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.js** fájlt a(z) ```simulateddevice``` mappában.

4. Adja hozzá a következő ```require``` utasításokat a **SimulatedDevice.js** fájl elejéhez:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Adjon hozzá egy `deviceconnectionstring` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  A helyére írja `{deviceconnectionstring}` be az *IoT hub létrehozása* szakaszban létrehozott eszköz nevét:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kód tartalmazza a kapcsolódási karakterláncot: ez nem ajánlott eljárás, és a használati esettől és az architektúrától függően érdemes lehet megfontolni a titok tárolásának biztonságosabb módjait.

6. Adja hozzá a következő kódot az ügyfél összekapcsolásához:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Hozzon létre egy visszahívást, és használja a **uploadToBlob** függvényt a fájl feltöltéséhez.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Mentse és zárja be a **SimulatedDevice.js** fájlt.

9. Másolja a mappába egy lemezképfájlt, `simulateddevice` és nevezze át `myimage.png` .

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely a fájlfeltöltés-értesítési üzeneteket fogadja a IoT hub-ból, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-node.md)hozott létre. A fájlfeltöltés-értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltés-értesítés fogadása

Ebben a szakaszban egy Node.js Console-alkalmazást hoz létre, amely befogadja a fájlfeltöltés-értesítési üzeneteket a IoT Hubból.

Ennek a szakasznak a végrehajtásához használhatja a IoT Hub **iothubowner** -kapcsolódási karakterláncát. A kapcsolati karakterláncot a [Azure Portal](https://portal.azure.com/) a **közös hozzáférési szabályzat** panelen találja.

1. Hozzon létre egy ```fileuploadnotification``` nevű üres mappát.  A(z) ```fileuploadnotification``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. Az ```fileuploadnotification``` **Azure-iothub SDK-** csomag telepítéséhez futtassa a következő parancsot a parancssorban a mappában:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **FileUploadNotification.js** fájlt a `fileuploadnotification` mappában.

4. Adja hozzá a következő `require` utasításokat a **FileUploadNotification.js** fájl elejéhez:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adjon hozzá egy `iothubconnectionstring` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  A `{iothubconnectionstring}` helyőrző értékét cserélje le a IoT hub-beli, a korábban a [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)során átmásolt karakterláncra:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kód tartalmazza a kapcsolódási karakterláncot: ez nem ajánlott eljárás, és a használati esettől és az architektúrától függően érdemes lehet megfontolni a titok tárolásának biztonságosabb módjait.

6. Adja hozzá a következő kódot az ügyfél összekapcsolásához:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Nyissa meg az ügyfelet, és használja az **getFileNotificationReceiver** függvényt az állapot-frissítések fogadásához.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Mentse és zárjuk be a **FileUploadNotification.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A mappában a parancssorban `fileuploadnotification` futtassa a következő parancsot:

```cmd/sh
node FileUploadNotification.js
```

A mappában a parancssorban `simulateddevice` futtassa a következő parancsot:

```cmd/sh
node SimulatedDevice.js
```

Az alábbi képernyőképen a **SimulatedDevice** alkalmazás kimenete látható:

![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-node-node-file-upload/simulated-device.png)

Az alábbi képernyőképen a **FileUploadNotification** alkalmazás kimenete látható:

![A Read-file-upload-Notification alkalmazás kimenete](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

A portál használatával megtekintheti a feltöltött fájlt a konfigurált tárolóban:

![Feltöltött fájl](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. A IoT hub funkcióit és forgatókönyveit továbbra is megismerheti a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)
