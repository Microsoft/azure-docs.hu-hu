---
title: Gyors útmutató – kommunikáció az Node.js az Azure IoT Hub-eszközök Streams szolgáltatásával
description: Ebben a rövid útmutatóban egy Node.js szolgáltatás-oldali alkalmazást fog futtatni, amely egy IoT-eszközzel kommunikál egy eszköz-adatfolyamon keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624388"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Node.js eszközön keresztüli kommunikáció IoT Hub eszköz streamen keresztül (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Ebben a rövid útmutatóban egy kiszolgálóoldali alkalmazást futtat, és az eszköz és a szolgáltatás közötti kommunikációt állítja be az eszköz streamek használatával. Az Azure IoT Hub-eszközök lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon keresztüli kommunikációt. A nyilvános előzetes verzióban a Node.js SDK csak a szolgáltatási oldalon található eszköz-adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató csak a szolgáltatási oldali alkalmazások futtatására vonatkozó utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* A [C eszköz alkalmazásaival való kommunikáció elvégzése IoT hub eszközön keresztül](./quickstart-device-streams-echo-c.md) , vagy a [C# eszközön lévő alkalmazásokkal való kommunikáció IoT hub eszköz streameken keresztül](./quickstart-device-streams-echo-csharp.md).

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    node --version
    ```

* [Példa Node.js projektre](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

A Microsoft Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök adatfolyamait.

> [!IMPORTANT]
> Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:
>
> * Az USA középső régiója
> * USA középső – EUAP
> * Észak-Európa
> * Délkelet-Ázsia

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyDevice**: Ez a név a regisztrálni kívánt eszközhöz. Javasoljuk, hogy a **MyDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Szüksége van egy *szolgáltatáskapcsolati sztringre* is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Jegyezze fel a visszaadott szolgáltatás-kapcsolatok karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszköz streamen keresztül

Ebben a szakaszban az eszköz-és a kiszolgálóoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszköz oldali alkalmazás futtatása

Ahogy korábban említettük, a IoT Hub Node.js SDK csak a szolgáltatási oldalon található eszköz-adatfolyamokat támogatja. Az eszközökön futó alkalmazásokhoz használja az alábbi útmutatókban elérhető egyik kapcsolódó eszközt:

* [Kommunikáció a C eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-c.md)

* [Kommunikáció a C# eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-csharp.md)

Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy az eszközön futó alkalmazás fut.

### <a name="run-the-service-side-application"></a>A szolgáltatás-oldali alkalmazás futtatása

Az ebben a rövid útmutatóban található szolgáltatás-oldali Node.js alkalmazás a következő funkciókat nyújtja:

* Egy eszköz streamet hoz létre egy IoT-eszközhöz.
* Beolvassa a bemenetet a parancssorból, és elküldi az eszköz alkalmazásának, amely visszaadja a visszalépést.

A kód bemutatja egy eszköz adatfolyamának kezdeményezési folyamatát, valamint azt, hogy hogyan használható az adatküldés és az Adatfogadás.

Feltételezve, hogy az eszközön futó alkalmazás fut, kövesse az alábbi lépéseket egy helyi terminál ablakban a szolgáltatás oldali alkalmazás futtatásához Node.jsban:

* Adja meg a szolgáltatás hitelesítő adatait és az eszköz AZONOSÍTÓját környezeti változókként.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Módosítsa a ServiceConnectionString helyőrzőjét úgy, hogy az megfeleljen a szolgáltatás kapcsolódási karakterláncának, és **MyDevice** , ha más nevet adott a felhasználónak.

* Navigáljon a `Quickstarts/device-streams-service` kibontott projekt mappájába, és futtassa a mintát a csomópont használatával.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Az utolsó lépés végén a szolgáltatási oldali program elindít egy streamet az eszközön, és a létrehozás után karakterlánc-puffert küld a szolgáltatásnak az adatfolyamon keresztül. Ebben a példában a szolgáltatás-oldali program egyszerűen beolvassa a `stdin` terminálon, és elküldi azt az eszköznek, amely ezt követően visszhangot ad vissza. Ez a két alkalmazás közötti sikeres kétirányú kommunikációt mutatja be.

![Szolgáltatás oldali konzol kimenete](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Ezután leállíthatja a programot az ENTER billentyű lenyomásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, az eszköz és a szolgáltatás oldalán található alkalmazások közötti adatfolyamot állított be, és a stream használatával visszaküldheti az adatátvitelt az alkalmazások között.

Az alábbi hivatkozásokat követve további információkat tudhat meg az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
