---
title: 'Rövid útmutató: Eszköz vezérlése az Azure IoT Hubról (Node.js) | Microsoft Docs'
description: Ebben a rövid útmutatóban két Node.js mintaalkalmazást fog futtatni. Az egyik egy háttéralkalmazás, amely a hubhoz csatlakoztatott eszközök távoli vezérlését teszi lehetővé. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 462b485b5dc5106ab18959ab2cdbfe1bfc0f94bf
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330561"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-nodejs"></a>Gyors útmutató: A vezérlőelem egy eszköz csatlakozik az IoT hub (Node.js)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését az IoT-eszközökről a felhőbe, valamint az eszközök kezelését a felhőből. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt Node.js-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely válaszol a háttéralkalmazásokból meghívott közvetlen metódusokra. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy háttéralkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközre. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Node.js használatával készült. Node.js v10.x.x, vagy később a fejlesztői gépén van szüksége.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ha még nem tette meg, töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és csomagolja ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben, hozza létre az eszközidentitást.

   **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

   **MyNodeDevice**: Ön regisztrálja az eszköz neve. Használat **MyNodeDevice** látható módon. Ha úgy dönt, hogy az eszköz egy másik nevet, meg kell során ez a cikk ezt a nevet használja, és az eszköz neve a mintaalkalmazások őket futtatása előtt.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

    **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyNodeDevice \
      --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

3. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName --policy-name service --output table
    ```

    Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében. A szolgáltatáskapcsolati sztring nem azonos az eszközkapcsolati sztringgel.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtázást küld vissza a hubra a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device-2** mappába.

2. Nyissa meg a **SimulatedDevice.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `connectionString` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.js** fájl módosításait.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-node/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A háttéralkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik. Az alkalmazás közvetlen metódusokat hív meg egy eszközre az IoT Hubon keresztül, és figyeli a nyugtázásokat. Az IoT Hub-háttéralkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\back-end-application** mappába.

2. Nyissa meg a **BackEndApplication.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le az `connectionString` változó értéket a szolgáltatás korábban lejegyzett kapcsolati sztringjére. Mentse a **BackEndApplication.js** fájl módosításait.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás futtatásához:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    A következő képernyőképen az a kimenet látható, amelyben az alkalmazás közvetlen metódust hív meg az eszközre, és megkapja a nyugtázást:

    ![A háttéralkalmazás futtatása](./media/quickstart-control-device-node/BackEndApplication.png)

    A háttéralkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és megváltozik az üzenetküldések gyakorisága:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-node/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy közvetlen metódust hívott az eszközön egy háttér-alkalmazásból, és a közvetlen metódus meghívása egy szimulált eszközalkalmazástól az válaszolt.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Útvonal telemetriai adatokat a feldolgozáshoz különböző végpontok](tutorial-routing.md)