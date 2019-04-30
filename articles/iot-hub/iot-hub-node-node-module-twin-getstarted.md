---
title: Ismerkedés az Azure IoT Hub identitás- és a modul (Node.js) ikermodulja |} A Microsoft Docs
description: Learn how to create module identity and update module twin using IoT SDKs for Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 312d3abad2ee2c9e668f8b354aaba96f8a652698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626202"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Ismerkedés az IoT Hub identitás- és modul ikermodul Node.js háttérrendszer és a Node.js-eszköz használata

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Amíg az Azure IoT Hub eszközidentitása és eszközikre lehetővé teszi a háttéralkalmazás számára az eszköz konfigurálását, és rálátást nyújt az eszköz feltételeire, a modulidentitás és a moduliker az eszköz egyes összetevőihez biztosítja ezeket a lehetőségeket. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.

Ez az oktatóanyag végén van két Node.js-alkalmazások:

* A **CreateIdentities** egy eszközidentitást, egy modulidentitást valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközhöz és a modulügyfelekhez.

* Az **UpdateModuleTwinReportedProperties** a moduliker jelentett tulajdonságainak frissítését továbbítja az IoT Hub részére.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához, kapcsolatos információkért lásd: [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)
* An IoT Hub.
* Telepítse a legújabb [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node).

Létrehozta az IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati sztringgel.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Az IoT Hub eszközidentitás és a egy modul identitás létrehozása

Ebben a szakaszban egy Node.js-alkalmazást, amely egy eszközidentitást, valamint egy modul identitás létrehozza az IoT hub eszközidentitás-jegyzékben lévő hoz létre. Egy eszköz vagy modul csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. "Identitásjegyzék" című szakaszában talál további információt a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide-identity-registry.md). A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. Hozzon létre egy könyvtárat, amely tárolja a kódot.

2. Belül a könyvtárhoz, először futtassa **npm init -y** hozhat létre egy üres package.json alapértelmezett értékekkel. Ez a következő projektfájlban: a kódhoz.

3. Futtatás **npm telepítése -S azure-iothub\@modulok – előzetes verzió** SDK-szolgáltatás telepítése belül a **node_modules** alkönyvtárat.

    > [!NOTE]
    > A alkönyvtár neve node_modules a word modult használja a "csomópontja library" jelenti. Az előfizetési időszak itt nem az IoT Hub-modulokkal.

4. Hozzon létre a következő .js fájl a könyvtárban. Meghívására **add.js**. Másolja és illessze be a kapcsolati karakterlánccal és a hub nevét.

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

Ez az alkalmazás egy új eszközidentitást hoz létre azonosító **myFirstDevice** és a egy modul identitás azonosítójú **myFirstModule** az eszköz **myFirstDevice**. (Ha ez a modulazonosító már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő modulinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált modulalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

Csomópont add.js használatával futtassa. Akkor kap egy kapcsolati karakterláncot az eszközidentitás és a egy másik, a modul identitás.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információkért lásd: [IoT Hub fejlesztői útmutatójának](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Frissítse az ikermodul Node.js eszközoldali SDK-val

Ebben a szakaszban egy Node.js létrehozása a szimulált eszköz, amely frissíti az ikermodul jelentett tulajdonságokként.

1. **A modul kapcsolati sztring lekérése** – jelentkezzen be a [az Azure portal](https://portal.azure.com/). Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresés myFirstDevice, nyissa meg azt, és tekintse meg a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

   ![Az Azure Portal moduladatai](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Hasonló, a fenti lépésben tette, hozzon létre egy könyvtárat a kód és az NPM használatával inicializálja, és telepítse az eszközoldali SDK (**npm telepítése -S azure-iot-device-amqp\@modulok – előzetes verzió**).

   > [!NOTE]
   > Az npm install parancs úgy lassú. Türelemmel, akkor van lehetőség le nagy mennyiségű kódot a csomag tárházból.

   > [!NOTE]
   > Ha hibaüzenet jelenik meg, amely szerint a npm hiba! beállításjegyzék hiba json-elemzés, ez a biztonsággal figyelmen kívül hagyhatja. Ha hibaüzenet jelenik meg, amely szerint a npm hiba! beállításjegyzék hiba json-elemzés, ez a biztonsággal figyelmen kívül hagyhatja.

3. Hozzon létre egy twin.js nevű fájlt. Másolja és illessze be a modul azonosító karakterláncot.

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

4. Most futtassa ezt a parancsot **csomópont twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az eszközfelügyelettel](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)