---
title: Gyors útmutató – kommunikáció az eszköz alkalmazásával C-ben az Azure IoT Hub eszköz streamekkel
description: Ebben a rövid útmutatóban egy olyan C-eszközön futó alkalmazást futtat, amely egy IoT-eszközzel kommunikál egy eszköz streamen keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: fd179bf17f481ae84f768d619c6ddc0b9afab280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202552"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: a C eszközön lévő alkalmazásokkal való kommunikáció IoT Hub eszköz streamen keresztül (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök streamjét.

[IoT hub az eszközökön elérhető streamek](iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. A nyilvános előzetes verzióban a C SDK csak az eszközön található adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató csak az eszköz oldali alkalmazások futtatására vonatkozó utasításokat tartalmazza. A megfelelő kiszolgálóoldali alkalmazás futtatásához tekintse meg a következő cikkeket:

* [Kommunikáció a C# eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció a Node.js eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-nodejs.md)

Az ebben a rövid útmutatóban található eszköz C alkalmazás a következő funkciókkal rendelkezik:

* Hozzon létre egy eszköz streamet egy IoT-eszközhöz.

* Fogadja a szolgáltatás-oldali alkalmazásból elküldett adatok fogadását, és adja meg a visszavisszhangot.

A kód bemutatja egy eszköz adatfolyamának kezdeményezési folyamatát, valamint azt, hogyan lehet az adatküldésre és fogadásra használni.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

* A [Visual Studio 2019](https://www.visualstudio.com/vs/) -es verziójának telepítése a C++ munkaterhelést használó **asztali fejlesztéssel** .

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [C Azure IoT Device SDK](iot-hub-device-sdk-c-intro.md)-t használja. Készítse elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -nak a githubról történő klónozásához és létrehozásához használt fejlesztési környezetet. A GitHubon található SDK tartalmazza az ebben a rövid útmutatóban használt mintakód-kódot.

   > [!NOTE]
   > Az eljárás megkezdése előtt győződjön meg arról, hogy a Visual Studio telepítve van a C++ munkaterheléssel **rendelkező asztali fejlesztéssel** .

1. Telepítse a [CMAK-Build rendszerét](https://cmake.org/download/) a letöltési oldalon leírtak szerint.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház klónozásához:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    A művelet eltarthat néhány percig.

1. Hozzon létre egy *CMAK* -alkönyvtárat a git-tárház gyökérkönyvtárában, és navigáljon a mappához. Futtassa az alábbi parancsokat az *Azure-IOT-SDK-c* könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa az alábbi parancsokat a *CMAK* könyvtárból, hogy az SDK egy olyan verzióját hozza létre, amely a fejlesztői ügyfél platformra jellemző.

   * Linux rendszerben:

      ```bash
      cmake ..
      make -j
      ```

   * A Windows rendszerben nyisson meg egy [fejlesztői parancssort a Visual studióhoz](/dotnet/framework/tools/developer-command-prompt-for-vs). Futtassa a parancsot a Visual Studio-verzióhoz. Ez a rövid útmutató a Visual Studio 2019-et használja. Ezek a parancsok létrehoznak egy Visual Studio-megoldást a *CMAK* könyvtárában a szimulált eszközhöz.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

A csatlakoztatása előtt regisztrálnia kell egy eszközt az IoT hub használatával. Ebben a szakaszban a Azure Cloud Shell és a [IoT bővítmény](/cli/azure/ext/azure-iot/iot) használatával regisztrálja a szimulált eszközt.

1. Az eszköz identitásának létrehozásához futtassa a következő parancsot Cloud Shellban:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.
   > * Annak az eszköznek a nevére, amelyet regisztrál, javasolt a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszköznek, ezt a nevet használja ebben a cikkben, és a futtatásuk előtt frissítse az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszközhöz tartozó *eszköz-kapcsolódási karakterlánc* lekéréséhez futtassa a következő parancsot a Cloud Shellban:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Jegyezze fel a visszaadott eszköz csatlakoztatási karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszköz streamen keresztül

Ebben a szakaszban az eszköz-és a kiszolgálóoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszköz oldali alkalmazás futtatása

Az eszköz oldali alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Adja meg az eszköz hitelesítő adatait a mappában található **iothub_client_c2d_streaming_sample. c** forrásfájl szerkesztésével `iothub_client/samples/iothub_client_c2d_streaming_sample` , és adja hozzá az eszköz-csatlakoztatási karakterláncot.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Fordítsa le a kódot a következő parancsokkal:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. A lefordított program futtatása:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>A szolgáltatás-oldali alkalmazás futtatása

Ahogy azt korábban említettük, a IoT Hub C SDK csak az eszközön található adatfolyamokat támogatja. A kapcsolódó kiszolgálóoldali alkalmazás létrehozásához és futtatásához kövesse az alábbi rövid útmutatók valamelyikét:

* [Kommunikáció a C# eszköz alkalmazásával IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció Node.js eszközön IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, az eszközön lévő C-alkalmazás és a szolgáltatás oldalán egy másik alkalmazás között létrehozott egy adatfolyamot, és a stream használatával visszaküldheti az adatátvitelt az alkalmazások között.

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
