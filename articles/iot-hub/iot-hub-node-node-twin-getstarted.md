---
title: Iker Azure IoT Hub (Node) – első lépések | Microsoft Docs
description: Ikereszköz Azure IoT Hub címkék hozzáadásához, majd lekérdezés IoT Hub használata. Az Azure IoT Node.js használatával implementálja a szimulált eszközalkalmazást és egy szolgáltatásalkalmazást, amely hozzáadja a címkéket, és futtatja a IoT Hub lekérdezést.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2047de2c1e61554ba96af8471905a22291861184
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484574"
---
# <a name="get-started-with-device-twins-nodejs"></a>Ikereszköz –első lépések (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két új konzolalkalmazással Node.js fog:

* **AddTagsAndQuery.js** egy Node.js, amely címkéket és lekérdezéseket ad hozzá az ikereszközhöz.

* **TwinSimulatedDevice.js,** egy Node.js, amely egy olyan eszközt szimulál, amely a korábban létrehozott eszközidentitációval csatlakozik az IoT Hubhoz, és jelenti annak kapcsolati állapotát.

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) az eszköz- és a háttéralkalmazások építéséhez használható Azure IoT SDK-król tartalmaz információkat.
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Node.js 10.0.x vagy újabb verzió.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT Hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati sztring lekérte

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy új konzolalkalmazást Node.js, amely hely metaadatokat ad hozzá a myDeviceId -hez társított **ikereszközhöz.** Ezután lekérdezi az IoT Hubban tárolt ikereszközöket, kiválasztva az USA-ban található eszközöket, majd azokat, amelyek mobilkapcsolatot jelentek.

1. Hozzon létre egy **új, addtagsandqueryapp nevű** üres mappát. Az **addtagsandqueryapp** mappában hozzon létre egy új package.jsa fájlban a következő paranccsal a parancssorban. A `--yes` paraméter az összes alapértelmezett beállítást elfogadja.

    ```cmd/sh
    npm init --yes
    ```

2. Az **addtagsandqueryapp** mappában a parancssorban futtassa a következő parancsot az **azure-iothub csomag telepítéséhez:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **újAddTagsAndQuery.js** az **addtagsandqueryapp mappában.**

4. Adja hozzá a következő kódot aAddTagsAndQuery.jsfájlhoz.  Cserélje le a helyére IoT Hub IoT Hub kapcsolati sztring le get the connection string (IoT Hub kapcsolati sztring `{iot hub connection string}` [lekért) helyére bemáselt kapcsolati sztringet.](#get-the-iot-hub-connection-string)

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    A **Registry** objektum elérhetővé teszi a szolgáltatásban található ikereszköz-műveletekhez szükséges összes metódust. Az előző kód először inicializálja a **Registry** objektumot, majd lekéri a **myDeviceId ikereszközét,** és végül frissíti a címkéket a kívánt helyadatokkal.

    A címkék frissítése után a **queryTwins függvényt hívja** meg.

5. Adja hozzá a következő  kódot aAddTagsAndQuery.jsa **queryTwins függvény megvalósításához:**

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Az előző kód két lekérdezést hajt végre: az első csak a **Redmond43** üzemben található eszközök ikereszközeit választja ki, a második pedig finomítja a lekérdezést, hogy csak azokat az eszközöket válassza ki, amelyek szintén mobilhálózaton keresztül csatlakoznak.

    Amikor a kód létrehozza a **lekérdezési** objektumot, meghatározza a második paraméterben visszaadott dokumentumok maximális számát. A **lekérdezési** objektum tartalmaz egy **hasMoreResults** logikai tulajdonságot, amely a **következőAsTwin** metódus többszöri meghívására használható az összes eredmény lekéréséhez. A next **nevű metódus** olyan eredményekhez érhető el, amelyek nem ikereszközök, például az aggregációs lekérdezések eredményei.

6. Futtassa az alkalmazást a következővel:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   A lekérdezés eredményében egy eszköznek kell látsza lennie, amely a **Redmond43-ban** található összes eszközt kéri, az eredményeket mobilhálózatot felhasználó eszközökre korlátozó lekérdezést nem.

   ![Az egyetlen eszköz a lekérdezési eredmények között](media/iot-hub-node-node-twin-getstarted/service1.png)

A következő szakaszban egy olyan eszközalkalmazást hoz létre, amely jelentést készít a kapcsolati adatokról, és módosítja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban létrehoz egy Node.js-konzolalkalmazást, amely **myDeviceId-ként** csatlakozik a központhoz, majd frissíti az ikereszköz jelentett tulajdonságait, hogy tartalmazzák a mobilhálózattal csatlakoztatott információkat.

1. Hozzon létre egy új, **reportconnectivity nevű üres mappát.** A **reportconnectivity** mappában hozzon létre egy új package.jsa fájlban a következő paranccsal a parancssorban. A `--yes` paraméter az összes alapértelmezett értéket elfogadja.

    ```cmd/sh
    npm init --yes
    ```

2. A **reportconnectivity** mappában a parancssorban futtassa a következő parancsot az **azure-iot-device** és **az azure-iot-device-mqtt csomagok telepítéséhez:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.js** a **reportconnectivity mappában.**

4. Adja hozzá a következő kódot **aReportConnectivity.js** fájlhoz. Cserélje le a helyére azt az eszközkapcsolati sztringet, amely a `{device connection string}` **myDeviceId** eszközidentitás létrehozásakor kimásolható az Új eszköz regisztrálása az [IoT Hubban.](#register-a-new-device-in-the-iot-hub)

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Az **Ügyfél** objektum az ikereszköz eszközről való kommunikációhoz szükséges összes metódust elérhetővé teszi. Az előző kód az **Ügyfél** objektum inicializálása után lekéri a **myDeviceId** ikereszközét, és frissíti a jelentett tulajdonságát a kapcsolati adatokkal.

5. Az eszközalkalmazás futtatása

    ```cmd/sh
        node ReportConnectivity.js
    ```

    A következő üzenetnek kell jelenik `twin state reported` meg: .

6. Most, hogy az eszköz jelentette a kapcsolati adatait, mindkét lekérdezésben meg kell jelenni. Vissza **az addtagsandqueryapp mappában,** és futtassa újra a lekérdezéseket:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Ezúttal a **myDeviceId** mindkét lekérdezési eredményben megjelenik.

    ![A myDeviceId megjelenítése mindkét lekérdezési eredményben](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adott hozzá egy háttéralkalmazásból, és megírt egy szimulált eszközalkalmazást az ikereszköz kapcsolati információinak jelentéséhez. Azt is megtanulta, hogyan lehet lekérdezni ezt az információt az SQL-hez hasonló IoT Hub használatával.

A következő forrásokból megtudhatja, hogyan használhatja a következőket:

* telemetria küldése eszközökről az Első [lépések a IoT Hub](quickstart-send-telemetry-node.md) oktatóanyag segítségével

* az ikereszköz kívánt tulajdonságainak használatával konfigurálja az eszközöket a Kívánt tulajdonságok [használata az eszközök konfigurálához oktatóanyagban.](tutorial-device-twins.md)

* az eszközök interaktív vezérlése (például egy felhasználó által vezérelt alkalmazás ventilátorának bekapcsolása) a [Közvetlen metódusok használata oktatóanyag](quickstart-control-device-node.md) segítségével.
