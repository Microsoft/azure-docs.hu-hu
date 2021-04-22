---
title: Rövid útmutató – eszközstreamek Azure IoT Hub C rövid útmutató az SSH-hez és az RDP-hez
description: Ebben a rövid útmutatóban egy C-mintaalkalmazást fog futtatni, amely proxyként működik az SSH- és RDP-forgatókönyvek engedélyezéséhez IoT Hub eszközstreamek esetében.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 8a74b5e463045e92e703ef6932f92b84d4709f36
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864088"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése IoT Hub eszközstreamen C proxyalkalmazással (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub jelenleg előzetes verziójú funkcióként támogatja az [eszközstreameket.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub az eszközstreamek lehetővé](./iot-hub-device-streams-overview.md) teszik, hogy a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát módon kommunikáljanak. A beállítás áttekintését a Helyi [proxyminta oldalon olvashatja el.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Ez a rövid útmutató a Secure Shell-forgalom (a 22-es porton keresztül) eszközstreamek keresztüli bújtatásának beállítását ismerteti. Az RDP RDP protokoll forgalom beállítása hasonló, és egyszerű konfigurációs változtatást igényel. Mivel az eszközstreamek alkalmazás- és protokolltól függetlenek, ezt a rövid útmutatót módosíthatja más típusú alkalmazásforgalomhoz.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső régiója – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

* Telepítse [Visual Studio 2019-es](https://www.visualstudio.com/vs/) verzióra, ha engedélyezve van a [C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat asztali fejlesztése.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Működés

Az alábbi ábra bemutatja, hogyan teszik lehetővé az eszköz- és a szolgáltatás helyi proxyprogramja a végpontok közötti kapcsolatot az SSH-ügyfél és az SSH-démonfolyamatok között. A nyilvános előzetes verzióban a C SDK csak az eszközoldalon támogatja az eszközstreameket. Ennek eredményeképpen ez a rövid útmutató csak az eszköz helyi proxyalkalmazásának futtatására vonatkozó utasításokat tartalmazza. A kapcsolódó szolgáltatásoldali alkalmazás felépítéséhez és futtatásához kövesse az alábbi rövid útmutatók egyikének utasításait:

* [SSH/RDP eszközstreamek IoT Hub C#-proxy használatával](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP eszközstreamek IoT Hub NodeJS-proxy használatával.](./quickstart-device-streams-proxy-nodejs.md)

![Helyi proxy beállítása](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. A szolgáltatás helyi proxyja csatlakozik az IoT Hubhoz, és elindít egy eszközstreamet a céleszközre.

2. Az eszköz helyi proxyja befejezi a streamelési kezdeményezési kézfogást, és végpontok között streamelési alagutat hoz létre az IoT Hub szolgáltatásoldali streamvégpontjának használatával.

3. Az eszköz helyi proxyja csatlakozik az eszköz 22-es portját figyelő SSH-démonhoz. Ez a beállítás konfigurálható az "Eszköz helyi proxyalkalmazás futtatása" című szakaszban leírtak szerint.

4. A szolgáltatás helyi proxyja új SSH-kapcsolatokat vár a felhasználótól úgy, hogy figyel egy kijelölt portot, amely ebben az esetben a 2222-es port. Ez a beállítás konfigurálható az "Eszköz helyi proxyalkalmazás futtatása" című szakaszban leírtak szerint. Amikor a felhasználó SSH-ügyfélen keresztül csatlakozik, az alagút lehetővé teszi az SSH-alkalmazás forgalmának átvitelét az SSH-ügyfél és a kiszolgálóprogram között.

> [!NOTE]
> Az eszközstreamen keresztül küldött SSH-forgalom nem közvetlenül a szolgáltatás és az eszköz között, hanem az IoT Hub streamvégpontjának bújtatásán keresztül halad át. További információ: Az Iot Hub-eszközstreamek [használatának előnyei.](iot-hub-device-streams-overview.md#benefits) Az ábra emellett azt az SSH-démont is bemutatja, amely ugyanazon az eszközön (vagy gépen) fut, mint az eszköz helyi proxyja. Ebben a rövid útmutatóban az SSH-démon IP-címe lehetővé teszi, hogy az eszköz helyi proxyja és a démon különböző gépeken is fusson.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a C-hez készült [Azure IoT eszközoldali SDK-t használja.](iot-hub-device-sdk-c-intro.md) Előkészít egy fejlesztési környezetet, amely az Azure IoT C SDK GitHubról való [klónozásához](https://github.com/Azure/azure-iot-sdk-c) és felépítéséhez használható. A GitHubon elérhető SDK tartalmazza a rövid útmutatóban használt mintakódot.

1. Töltse le a [CMake buildrendszert.](https://cmake.org/download/)

    A CMake telepítésének Visual Studio előtt fontos, hogy a Visual Studio előfeltételei (Visual Studio és az asztali  fejlesztés *C++* alkalmazással számítási feladat) telepítve vannak a gépen. Az előfeltételek ellenőrzése és a letöltés ellenőrzése után telepítheti a CMake buildrendszert.

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

   * Windows rendszeren futtassa a következő parancsokat a fejlesztői parancssorban a Visual Studio 2015 vagy 2017 futtatásához. A Visual Studio eszközhöz egy új megoldás jön létre a *cmake könyvtárban.*

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

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban egy szimulált [](/cli/azure/iot) Azure Cloud Shell fog regisztrálni az IoT-bővítvével.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell:

   > [!NOTE]
   > * Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.
   > * A regisztráló eszköz neveként javasoljuk, hogy használja a *MyDevice* eszközt az itt látható módon. Ha másik nevet választ az eszközhöz, használja ezt a nevet a cikkben, és a futtatás előtt frissítse az eszköz nevét a mintaalkalmazásban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az előbb *regisztrált* eszköz eszközkapcsolati sztringjéhez futtassa a következő parancsokat a Cloud Shell:

   > [!NOTE]
   > Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Jegyezze fel a visszaadott eszközkapcsolati sztringet későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-kapcsolat egy eszközzel eszközstreamek segítségével

Ebben a szakaszban egy végpontok között streamet hoz létre az SSH-forgalom bújtatása érdekében.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxyalkalmazásának futtatása

1. Szerkessze a **iothub_client_c2d_streaming_proxy_sample.c** fájlt a mappában, és adja meg az eszköz kapcsolati sztringje, a céleszköz `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` IP-címe/állomásneve és a 22-es SSH-portot:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Fordítsa le a mintát:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Futtassa a lefordított programot az eszközön:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxyalkalmazásának futtatása

A "Hogyan működik" című szakaszban már volt szó arról, hogy az SSH-forgalom bújtatásához egy végpontok közötti stream létrehozásához mindegyik végén helyi proxyra van szükség (a szolgáltatás és az eszköz oldalán egyaránt). A nyilvános előzetes verzióban a IoT Hub C SDK csak az eszközoldalon támogatja az eszközstreameket. A szolgáltatás helyi proxyja felépítéséhez és futtatásához kövesse az alábbi rövid útmutatókban található utasításokat:

   * [SSH/RDP IoT Hub keresztül C#-proxyalkalmazások használatával](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP IoT Hub-streamek használatával Node.js proxyalkalmazások használatával](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH-munkamenet létrehozása

Az eszköz- és szolgáltatás helyi proxyk futtatása után használja az SSH-ügyfélprogramot, és csatlakozzon a szolgáltatás helyi proxyjához a 2222-es porton (közvetlenül az SSH-démon helyett).

```cmd/sh
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablaka kéri a hitelesítő adatok megadását.

Az alábbi képen az eszköz helyi proxy konzolkimenete látható, amely a következő oldalon csatlakozik az SSH-démonhoz: `IP_address:22`

![Eszköz helyi proxykimenete](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Az alábbi képen az SSH-ügyfélprogram konzolkimenete látható. Az SSH-ügyfél a 22-es porthoz csatlakozva kommunikál az SSH-démonnal, amelyet a szolgáltatás helyi proxyja figyel:

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállít egy IoT Hubot, regisztrált egy eszközt, üzembe helyezett egy eszközt és egy szolgáltatás helyi proxyprogramját az IoT Hub-kapcsolaton keresztüli eszközstream létrehozására, és a proxyk használatával bújtatta az SSH-forgalmat.

További információ az eszközstreamekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Eszközstreamek áttekintése](./iot-hub-device-streams-overview.md)
