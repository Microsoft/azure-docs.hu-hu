---
title: Első lépések az Azure IoT Hub modul Identity & modul twin (Node.js)
description: Megtudhatja, hogyan hozhat létre modul-identitást és-frissítési modult a Node.js IoT SDK-k használatával.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 9de3f45a9d62a9d131583c133440ac8a311a468d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993275"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Ismerkedés a IoT Hub modul identitásával és moduljával (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Noha az Azure IoT Hub eszköz-identitás és az eszköz kettős, lehetővé teszi a háttér-alkalmazás számára az eszköz konfigurálását és az eszköz feltételeinek láthatóságát A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.

Az oktatóanyag végén két Node.js alkalmazást használhat:

* A **CreateIdentities** egy eszközidentitást, egy modulidentitást valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközhöz és a modulügyfelekhez.

* Az **UpdateModuleTwinReportedProperties** a moduliker jelentett tulajdonságainak frissítését továbbítja az IoT Hub részére.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0. x vagy újabb verzió. [A fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Eszköz identitásának és modul-identitásának létrehozása IoT Hub

Ebben a szakaszban egy Node.js alkalmazást hoz létre, amely létrehoz egy eszköz-identitást és egy modul-identitást az IoT hub identitás-beállításjegyzékében. Egy eszköz vagy modul csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért tekintse meg a [IoT hub fejlesztői útmutató](iot-hub-devguide-identity-registry.md)"Identity Registry" című szakaszát. A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. Hozzon létre egy könyvtárat a kód tárolásához.

2. A könyvtár belsejében először futtassa a **NPM init-y** parancsot, hogy üres package.jshozzon létre alapértelmezett értékekkel. Ez a kód projektfájl.

3. A **NPM install-S Azure-iothub \@ modulok futtatása – előzetes** verzió a Service SDK telepítéséhez a **node_modules** alkönyvtáron belül.

    > [!NOTE]
    > Az alkönyvtár neve node_modules a Word modult használja a "csomópont-függvénytár" kifejezésre. Az itt található kifejezés IoT Hub modulokkal nem rendelkezik.

4. Hozza létre a következő. js fájlt a címtárában. Hívja meg **add.js**. Másolja és illessze be a hub-kapcsolatok sztringjét és a hub nevét.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Ez az alkalmazás létrehoz egy **MYFIRSTDEVICE** azonosítóval és egy **myFirstModule** azonosítójú modul-identitással az eszköz **myFirstDevice** alatt. (Ha a modul azonosítója már létezik az Identity registryben, a kód egyszerűen lekéri a meglévő modul adatait.) Az alkalmazás ezután megjeleníti az adott identitás elsődleges kulcsát. Ezt a kulcsot a szimulált modulalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

Futtassa ezt a csomópont add.js használatával. Megadja az eszköz identitásához tartozó kapcsolatok karakterláncát, és egy másikat a modul identitásához.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [IoT hub fejlesztői útmutató](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>A Twin modul frissítése Node.js Device SDK használatával

Ebben a szakaszban egy Node.js alkalmazást hoz létre a szimulált eszközön, amely frissíti a modul Twin jelentett tulajdonságait.

1. **A modul kapcsolódási karakterláncának beszerzése** – bejelentkezés a [Azure Portalba](https://portal.azure.com/). Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresse meg a myFirstDevice, nyissa meg, és láthatja, hogy a myFirstModule létrehozása sikeresen megtörtént. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

   ![Az Azure Portal moduladatai](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Hasonlóan a fenti lépéshez, hozzon létre egy könyvtárat az eszköz kódjához, és a NPM használatával inicializálja és telepítse az eszköz SDK-t (**NPM install-S Azure-IOT-Device-amqp \@ modulok – előzetes** verzió).

   > [!NOTE]
   > Előfordulhat, hogy a NPM telepítési parancs lassú. Legyen türelmes, sok kódot húz le a csomag adattárból.

   > [!NOTE]
   > Ha olyan hibaüzenetet lát, amely szerint a NPM ERR! beállításjegyzék-hiba a JSON-elemzés során, ez a biztonságos figyelmen kívül hagyása. Ha olyan hibaüzenetet lát, amely szerint a NPM ERR! beállításjegyzék-hiba a JSON-elemzés során, ez a biztonságos figyelmen kívül hagyása.

3. Hozzon létre egy twin.js nevű fájlt. Másolja és illessze be a modul Identity sztringjét.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Ezután futtassa ezt a parancsot a **twin.jsparancssori csomópont** használatával.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Ekkor megjelenik a következő:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Következő lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)