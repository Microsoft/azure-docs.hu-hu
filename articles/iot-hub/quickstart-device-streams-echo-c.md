---
title: Rövid útmutató – Kommunikáció eszközalkalmazással C-ben Azure IoT Hub eszközstreamekkel
description: Ebben a rövid útmutatóban egy eszközoldali C-alkalmazást fog futtatni, amely egy eszközstreamen keresztül kommunikál az IoT-eszközzel.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: ce0d0f9df4862d3c8ac8417e257fbf340e41961f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864093"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rövid útmutató: Kommunikáció egy C-eszközalkalmazással IoT Hub eszközstreamekkel (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub jelenleg előzetes verziójú funkcióként támogatja az [eszközstreameket.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub az eszközstreamek](iot-hub-device-streams-overview.md) lehetővé teszik, hogy a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát módon kommunikáljanak. A nyilvános előzetes verzióban a C SDK csak az eszközoldalon támogatja az eszközstreameket. Ennek eredményeképpen ez a rövid útmutató csak az eszközoldali alkalmazás futtatására vonatkozó utasításokat tartalmazza. A megfelelő szolgáltatásoldali alkalmazás futtatásához tekintse meg az alábbi cikkeket:

* [Kommunikáció eszközalkalmazásokkal C#-ban IoT Hub eszközstreamek segítségével](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció az eszközalkalmazásokkal Node.js eszközstreamek IoT Hub keresztül](./quickstart-device-streams-echo-nodejs.md)

Ebben a rövid útmutatóban az eszközoldali C alkalmazás a következő funkciókkal rendelkezik:

* Eszközstream létrehozása IoT-eszközre.

* Fogadja a szolgáltatásoldali alkalmazásból küldött adatokat, és megismétli őket.

A kód bemutatja az eszközstream kezdeményezési folyamatát, valamint azt, hogyan használható adatok küldására és fogadására.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételekre van szüksége:

* Telepítse [Visual Studio 2019-es](https://www.visualstudio.com/vs/) verzióra úgy, hogy engedélyezve van az asztali fejlesztés **A C++ számítási** feladat.

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső régiója – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a C-hez készült [Azure IoT eszközoldali SDK-t használja.](iot-hub-device-sdk-c-intro.md) Előkészít egy fejlesztési környezetet, amely az Azure IoT C SDK GitHubról való [klónozásához](https://github.com/Azure/azure-iot-sdk-c) és felépítéséhez használható. A GitHubon elérhető SDK tartalmazza a rövid útmutatóban használt mintakódot.

   > [!NOTE]
   > Az eljárás megkezdése előtt győződjön meg arról, hogy Visual Studio telepítve van az asztali fejlesztés **C++ alkalmazással számítási feladathoz.**

1. Telepítse a [CMake buildrendszert a](https://cmake.org/download/) letöltési oldalon leírtak szerint.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattár klónozásához:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet eltarthat néhány percig.

1. Hozzon *létre egy cmake* alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a következő parancsokat az *azure-iot-sdk-c könyvtárból:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a *cmake* könyvtárból az SDK a fejlesztési ügyfélplatformra jellemző verziójának buildjének felépítéséhez.

   * Linux rendszeren:

      ```bash
      cmake ..
      make -j
      ```

   * A Windowsban nyisson meg egy [fejlesztői parancssort](/dotnet/framework/tools/developer-command-prompt-for-vs)a Visual Studio. Futtassa az parancsot a Visual Studio. Ez a rövid útmutató a 2019 Visual Studio t használja. Ezek a parancsok Visual Studio megoldást hoznak létre a szimulált eszköz számára a *cmake könyvtárban.*

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

A csatlakozáshoz regisztrálnia kell egy eszközt az IoT Hubon. Ebben a szakaszban egy szimulált [](/cli/azure/iot) Azure Cloud Shell fog regisztrálni az IoT-bővítvével.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell:

   > [!NOTE]
   > * Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.
   > * A regisztráló eszköz neveként javasoljuk, hogy használja a *MyDevice* eszközt az itt látható módon. Ha másik nevet választ az eszközhöz, használja ezt a nevet a cikkben, és a futtatás előtt frissítse az eszköz nevét a mintaalkalmazásban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az előbb *regisztrált* eszköz eszközkapcsolati sztringjéhez futtassa a következő parancsot a Cloud Shell:

   > [!NOTE]
   > Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Jegyezze fel a visszaadott eszközkapcsolati sztringet a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Az eszköz és a szolgáltatás közötti kommunikáció eszközstreamek útján

Ebben a szakaszban az eszközoldali alkalmazást és a szolgáltatásoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Az eszközoldali alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Az eszköz hitelesítő adatainak megadásához szerkesszen **egy iothub_client_c2d_streaming_sample.c** forrásfájlt a mappában, és adja hozzá `iothub_client/samples/iothub_client_c2d_streaming_sample` az eszköz kapcsolati sztringet.

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

1. Futtassa a lefordított programot:

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

### <a name="run-the-service-side-application"></a>A szolgáltatásoldali alkalmazás futtatása

Ahogy korábban említettük, a IoT Hub C SDK csak az eszközoldalon támogatja az eszközstreameket. A kapcsolódó szolgáltatásoldali alkalmazás felépítéséhez és futtatásához kövesse az alábbi rövid útmutatók egyikét:

* [Kommunikáció eszközalkalmazással C# használatával IoT Hub eszközstreamek használatával](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció eszközalkalmazásokkal a Node.js eszközstreamek IoT Hub keresztül](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállít egy IoT Hubot, regisztrált egy eszközt, létrehozott egy eszközstreamet egy C-alkalmazás és egy másik alkalmazás között a szolgáltatási oldalon, és a stream segítségével adatokat küldött az alkalmazások között.

További információ az eszközstreamekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Eszközstreamek áttekintése](./iot-hub-device-streams-overview.md)
