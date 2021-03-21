---
title: 'Gyors útmutató: telemetria küldése az Azure IoT (Node.js)'
description: Ebben a rövid útmutatóban két Node.js-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 7aa95e2117dc3bb2e837e62ef42e3a770f2266d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842124"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Gyors útmutató: telemetria küldése egy eszközről egy IoT-hubhoz, és olvasása háttérbeli alkalmazással (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Ebben a rövid útmutatóban egy szimulált eszközről küld telemetria egy Azure IoT Hubon keresztül a feldolgozásra szolgáló háttérbeli alkalmazáshoz. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Node.js alkalmazást használ: az egyiket, hogy elküldje a telemetria, és az egyiket, hogy beolvassa a telemetria a középpontból. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Node.js 10 +](https://nodejs.org). Ha a Azure Cloud Shell használja, ne frissítse Node.js telepített verzióját. A Azure Cloud Shell már rendelkezik a legújabb Node.js-verzióval.

    A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    node --version
    ```

* [Példa Node.js projektre](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* A 8883-es port megnyitható a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyNodeDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyNodeDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

1. A IoT hub _Event Hubs-kompatibilis végpontja_, _Event Hubs-kompatibilis útvonala_ és a _szolgáltatás elsődleges kulcsa_ is szükséges ahhoz, hogy a háttér-alkalmazás csatlakozhasson az IoT hubhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet később a rövid útmutatóban fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Nyissa meg a helyi terminálablakot, és keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a változó értékét `connectionString` a korábban megjegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat **SimulatedDevice.js**.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Nyisson meg a másik helyi terminálablakot, és keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

1. Nyissa meg a **ReadDeviceToCloudMessages.js** fájlt egy Ön által választott szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Érték |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Cserélje le a változó értékét a Event Hubs-kompatibilis végpontra, amelyet korábban jegyzett készített. |
    | `eventHubsCompatiblePath`     | Cserélje le a változó értékét arra a Event Hubs-kompatibilis elérési útra, amelyet korábban jegyzett készített. |
    | `iotHubSasKey`                | A változó értékét cserélje le a szolgáltatás elsődleges kulcsára, amelyet korábban jegyzett készített. |

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás futtatásához:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldve a hubhoz egy Node.js alkalmazás használatával, valamint egy egyszerű háttérbeli alkalmazás használatával olvassa be a telemetria.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-node.md)
