---
title: Gyors útmutató – Azure IoT Hub-eszköz streamek C gyors SSH és RDP esetén
description: Ebben a rövid útmutatóban egy olyan minta C-alkalmazást futtat, amely proxyként szolgál az SSH-és RDP-forgatókönyvek engedélyezéséhez IoT Hub eszköz streamen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: a6e3407d9ddb799ab0ee5846acc149048f593563
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202008"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Gyors útmutató: SSH és RDP engedélyezése IoT Hub-adatfolyamon C alkalmazásproxy-alkalmazással (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Az Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök streamjét.

[IoT hub az eszközökön elérhető streamek](./iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. A telepítés áttekintését [a helyi proxy minta oldalán](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)tekintheti meg.

Ez a rövid útmutató ismerteti a Secure Shell-(SSH-) forgalom (a 22-es port használatával) adatfolyamokon keresztüli bújtatásának beállítását. A RDP protokoll-(RDP-) forgalom beállítása hasonló, és egy egyszerű konfigurációs módosításra van szükség. Mivel az eszközök adatfolyamai az alkalmazás-és protokoll-függetlenek, a rövid útmutató módosításával más típusú alkalmazások forgalmát is elvégezheti.

## <a name="prerequisites"></a>Előfeltételek

* Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

* A [Visual Studio 2019](https://www.visualstudio.com/vs/) -es verziójának telepítése a C++ munkaterhelést használó [asztali fejlesztéssel](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Működés

Az alábbi ábra azt szemlélteti, hogy az eszköz és a szolgáltatás helyi proxybeállításait hogyan lehet végpontok közötti kapcsolatot engedélyezni az SSH-ügyfél és az SSH démon folyamatai között. A nyilvános előzetes verzióban a C SDK csak az eszközön található adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató csak az eszközön belüli alkalmazásproxy futtatására vonatkozó utasításokat tartalmazza. A kapcsolódó kiszolgálóoldali alkalmazás létrehozásához és futtatásához kövesse az alábbi rövid útmutatók valamelyikét:

* [SSH/RDP IoT Hub-eszközökön a C# proxy használatával](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP IoT hub eszköz streamen keresztül a NodeJS proxy használatával](./quickstart-device-streams-proxy-nodejs.md).

![Helyi proxy beállítása](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. A szolgáltatás helyi proxyja csatlakozik az IoT hubhoz, és elindítja az eszköz streamjét a céleszköz felé.

2. Az eszköz helyi proxyja befejezi az adatfolyam-kezdeményező kézfogást, és egy végpontok közötti folyamatos átviteli alagutat hoz létre az IoT hub streaming végpontján keresztül a szolgáltatás felé.

3. Az eszköz helyi proxyja csatlakozik ahhoz az SSH-démonhoz, amely a 22-es portot figyeli az eszközön. Ez a beállítás konfigurálható az "eszköz helyi proxy alkalmazás futtatása" szakaszban leírtak szerint.

4. A szolgáltatás helyi proxyja egy megadott port figyelésével vár a felhasználó új SSH-kapcsolataira, ami ebben az esetben a 2222-es port. Ez a beállítás konfigurálható az "eszköz helyi proxy alkalmazás futtatása" szakaszban leírtak szerint. Ha a felhasználó SSH-ügyféllel csatlakozik, az alagút lehetővé teszi az SSH-alkalmazások forgalmának átvitelét az SSH-ügyfél és a kiszolgálói programok között.

> [!NOTE]
> Az adatfolyamként küldött SSH-forgalmat az IoT hub streaming-végpontja továbbítja, nem közvetlenül a szolgáltatás és az eszköz között. További információ: az [IOT hub-adatfolyamok használatának előnyei](iot-hub-device-streams-overview.md#benefits). Az ábrán az is látható, hogy az ugyanazon az eszközön (vagy gépen) futó SSH démon az eszköz helyi proxyja. Ebben a rövid útmutatóban az SSH démon IP-címének megadása lehetővé teszi, hogy az eszköz helyi proxyja és a démon a különböző gépeken is fusson.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [C Azure IoT Device SDK](iot-hub-device-sdk-c-intro.md)-t használja. Készítse elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -nak a githubról történő klónozásához és létrehozásához használt fejlesztési környezetet. A GitHubon található SDK tartalmazza az ebben a rövid útmutatóban használt mintakód-kódot.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos megjegyezni, hogy a Visual Studio előfeltételei (a Visual Studio és az *asztali fejlesztés C++* számítási feladattal) telepítve vannak a gépen a CMAK telepítésének megkezdése *előtt* . Az előfeltételek érvénybe helyezése és a letöltés ellenőrzése után telepítheti a CMak-Build rendszerét.

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

   * A Windows rendszerben futtassa a következő parancsokat a Visual Studio 2015-es vagy a 2017-es verziójának fejlesztői parancssorában. A rendszer létrehoz egy Visual Studio-megoldást a szimulált eszközhöz a *CMAK* -címtárban.

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

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell és a [IoT bővítmény](/cli/azure/ext/azure-iot/iot) használatával regisztrálja a szimulált eszközt.

1. Az eszköz identitásának létrehozásához futtassa a következő parancsot Cloud Shellban:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.
   > * Annak az eszköznek a nevére, amelyet regisztrál, javasolt a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszköznek, ezt a nevet használja ebben a cikkben, és a futtatásuk előtt frissítse az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszközhöz tartozó *eszköz-kapcsolódási karakterlánc* lekéréséhez futtassa a következő parancsokat a Cloud Shellban:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Jegyezze fel a visszaadott eszköz csatlakoztatási karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-eszköz az eszközön keresztül streamek használatával

Ebben a szakaszban egy végpontok közötti streamet hoz létre az SSH-forgalom bújtatásához.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy alkalmazásának futtatása

1. Szerkessze a (z **) iothub_client_c2d_streaming_proxy_sample. c** forrásfájlt a mappában `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` , és adja meg az eszköz csatlakoztatási karakterláncát, a célként megadott eszköz IP-címét/állomásnevét, valamint a 22-es SSH-portot:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. A minta fordítása:

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

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy alkalmazásának futtatása

Ahogy azt a "hogyan működik" című szakaszban tárgyaljuk, az SSH-forgalom bújtatásához szükséges teljes stream létrehozásához helyi proxyra van szükség (a szolgáltatás és az eszköz oldalain egyaránt). A nyilvános előzetes verzióban a IoT Hub C SDK csak az eszközön található adatfolyamokat támogatja. A szolgáltatás helyi proxyjának létrehozásához és futtatásához kövesse az alábbi rövid útmutatók valamelyikét:

   * [SSH/RDP IoT Hub-eszközökön keresztül, C# proxy alkalmazások használatával](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP IoT Hub-eszközökön Node.js proxy alkalmazások használatával](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH-munkamenet létrehozása

Miután az eszköz és a szolgáltatás helyi proxyi is futnak, használja az SSH-ügyfélprogramot, és kapcsolódjon a Service-local proxyhoz a 2222-as porton (közvetlenül az SSH démon helyett).

```cmd/sh
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablaka kéri a hitelesítő adatok megadását.

Az alábbi képen látható a konzol kimenete az eszköz helyi proxyján, amely az SSH démonhoz csatlakozik a következő helyen `IP_address:22` :

![Eszköz – helyi proxy kimenete](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Az alábbi képen az SSH-ügyfélprogram konzoljának kimenete látható. Az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonsal, amely a szolgáltatás helyi proxyját figyeli:

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, egy eszköz-és egy szolgáltatás-helyi proxy program üzembe helyezésével hozhat létre adatfolyamot IoT Hubon keresztül, és a proxykat az SSH-forgalom bújtatásához használta.

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
