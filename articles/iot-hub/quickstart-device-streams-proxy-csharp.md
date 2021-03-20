---
title: Rövid útmutató – Azure IoT Hub Device Streams C# gyors útmutató SSH-hoz és RDP-hez
description: Ebben a rövid útmutatóban két minta C#-alkalmazást futtat, amelyek lehetővé teszik az SSH-és RDP-forgatókönyvek IoT Hub-adatfolyamon keresztüli elérését.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 12e26818f86fc4abdc1873d031182fd994c04687
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624371"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Gyors útmutató: az SSH és az RDP engedélyezése IoT Hub-adatfolyamon C#-alkalmazásproxy alkalmazásával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök adatfolyamait.

[IoT hub az eszközökön elérhető streamek](iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. Ez a rövid útmutató két C# alkalmazást tartalmaz, amelyek lehetővé teszik az ügyfél-kiszolgáló alkalmazások forgalmát (például a Secure Shell [SSH] és a RDP protokoll [RDP] küldését egy IoT hub használatával létrehozott eszköz-adatfolyamon. A telepítés áttekintését lásd: [helyi proxy Application Sample for SSH vagy RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Ez a cikk először az SSH beállítását ismerteti (a 22-es port használatával), majd leírja, hogyan lehet módosítani a telepítő portját az RDP-hez. Mivel az eszköz-adatfolyamok alkalmazás-és protokoll-függetlenek, ugyanez a minta módosítható más típusú alkalmazások forgalmának kielégítése érdekében is. Ez a módosítás általában csak úgy módosítja a kommunikációs portot, amelyet a kívánt alkalmazás használ.

## <a name="prerequisites"></a>Előfeltételek

* Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső – EUAP
  * Délkelet-Ázsia
  * Észak-Európa

* Az ebben a rövid útmutatóban futtatott két minta alkalmazás C# nyelven íródott. A fejlesztői gépen szükség van a .NET Core SDK 2.1.0 vagy újabb verzióra.

    A [.net-ről több platformra](https://www.microsoft.com/net/download/all)is letöltheti a .net Core SDK.

    A következő parancs használatával ellenőrizze a C# aktuális verzióját a fejlesztői gépen:

    ```
    dotnet --version
    ```

* [Töltse le az Azure IoT C#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip), és bontsa ki a zip-archívumot.

* A felhasználó hitelesítéséhez használt eszközön (Windows vagy Linux) érvényes felhasználói fiók és hitelesítő adat.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Működés

Az alábbi ábra azt szemlélteti, hogy az eszköz helyi és helyi proxy alkalmazásai hogyan teszik lehetővé az SSH-ügyfél és az SSH-démon folyamatai közötti végpontok közötti kapcsolatot. Itt feltételezzük, hogy a démon ugyanazon az eszközön fut, mint az eszközön belüli proxy alkalmazás.

![Helyi alkalmazásproxy-alkalmazás beállítása](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. A szolgáltatás helyi proxy alkalmazása csatlakozik az IoT hubhoz, és elindítja az eszköz streamjét a céleszköz számára.

1. Az eszközön helyi alkalmazásproxy befejezi az adatfolyam-kezdeményező kézfogást, és egy végpontok közötti folyamatos átviteli alagutat hoz létre az IoT hub streaming végpontján keresztül a szolgáltatás felé.

1. Az eszközön helyi alkalmazásproxy csatlakozik az eszközön a 22-es portot figyelő SSH-démonhoz. Ez a beállítás konfigurálható az "eszköz helyi proxy alkalmazás futtatása" szakaszban leírtak szerint.

1. A szolgáltatás helyi proxy alkalmazása egy megadott port figyelésével vár a felhasználó új SSH-kapcsolataira, ami ebben az esetben a 2222-es port. Ez a beállítás konfigurálható a "szolgáltatás helyi proxy alkalmazás futtatása" című szakaszban leírtak szerint. Amikor a felhasználó az SSH-ügyféllel csatlakozik, az alagút lehetővé teszi az SSH-alkalmazások forgalmának átvitelét az SSH-ügyfél és a kiszolgálói alkalmazás között.

> [!NOTE]
> Az adatfolyamként küldött SSH-forgalmat az IoT hub streaming-végpontja továbbítja, nem közvetlenül a szolgáltatás és az eszköz között. További információ: az [IOT hub-adatfolyamok használatának előnyei](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban a Azure Cloud Shell használatával regisztrálhat egy szimulált eszközt.

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

1. Ha csatlakozni szeretne az IoT hubhoz, és létre szeretne hozni egy adatfolyamot, az IoT hub *szolgáltatás kapcsolati karakterláncára* is szüksége lesz a kiszolgálóoldali alkalmazás engedélyezéséhez. A következő parancs lekérdezi ezt az értéket az IoT hub esetében:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Jegyezze fel a visszaadott szolgáltatás-kapcsolatok karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-eszköz az eszközön keresztül streamek használatával

Ebben a szakaszban egy végpontok közötti streamet hoz létre az SSH-forgalom bújtatásához.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy alkalmazásának futtatása

A helyi terminál ablakban navigáljon a `device-streams-proxy/device` kibontott projekt mappájában található könyvtárhoz. A következő információk hasznosak maradnak:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz csatlakoztatási karakterlánca. |
| `targetServiceHostName` | Az az IP-cím, amelyen az SSH-kiszolgáló figyel. A cím akkor lenne `localhost` , ha ugyanazt az IP-címet adta meg, ahol az eszközön helyi alkalmazásproxy fut. |
| `targetServicePort` | Az alkalmazás protokollja által használt port (az SSH esetében alapértelmezés szerint a 22-es port).  |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy alkalmazásának futtatása

Egy másik helyi terminál ablakban navigáljon a `iot-hub/quickstarts/device-streams-proxy/service` kibontott projekt mappájához. A következő információk hasznosak maradnak:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | A IoT Hub szolgáltatási kapcsolatok karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a példában a 2222-es portot használjuk, de más tetszőleges számú számot is használhat. |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Az SSH-ügyfél futtatása

Most használja az SSH-ügyfélalkalmazás alkalmazást, és kapcsolódjon a Service-local proxy alkalmazáshoz a 2222-as porton (az SSH démon helyett közvetlenül).

```
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablaka kéri a hitelesítő adatok megadását.

Konzol kimenete a szolgáltatás oldalán (a szolgáltatás helyi proxy alkalmazás figyeli a 2222-es portot):

![Szolgáltatás – helyi proxy alkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konzol kimenete az eszköz helyi alkalmazásproxy-alkalmazásához, amely az SSH démonhoz csatlakozik *IP_address: 22*:

![Eszköz helyi proxy alkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Az SSH-ügyfélalkalmazás konzoljának kimenete. Az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonsal, amelyet a szolgáltatás helyi proxy alkalmazás figyel:

![SSH-ügyfélalkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Eszköz RDP-je eszközön keresztül

Az RDP-beállítás hasonló az SSH beállításához (lásd fent). Az RDP cél IP-címét és a 3389-es portot használja helyette, és az RDP-ügyfelet használja (az SSH-ügyfél helyett).

### <a name="run-the-device-local-proxy-application-rdp"></a>Az eszköz helyi proxy alkalmazásának (RDP) futtatása

A helyi terminál ablakban navigáljon a `device-streams-proxy/device` kibontott projekt mappájában található könyvtárhoz. A következő információk hasznosak maradnak:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz csatlakoztatási karakterlánca. |
| `targetServiceHostName` | Az a állomásnév vagy IP-cím, amelyben az RDP-kiszolgáló fut. A cím akkor lenne `localhost` , ha ugyanazt az IP-címet adta meg, ahol az eszközön helyi alkalmazásproxy fut. |
| `targetServicePort` | Az alkalmazási protokoll által használt port (az RDP esetében alapértelmezés szerint ez a 3389-as port).  |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>A szolgáltatás helyi proxy alkalmazásának (RDP) futtatása

Egy másik helyi terminál ablakban navigáljon a `device-streams-proxy/service` kibontott projekt mappájához. A következő információk hasznosak maradnak:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | A IoT Hub szolgáltatási kapcsolatok karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a példában a 2222-es portot használjuk, de ezt más tetszőleges számokra is módosíthatja. |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-ügyfél futtatása

Most használja az RDP-ügyfélalkalmazás használatát, és kapcsolódjon a Service-local proxy alkalmazáshoz a 2222-as porton (ez egy tetszőlegesen elérhető, korábban kiválasztott port volt).

![Az RDP csatlakozik a Service-local alkalmazásproxy-alkalmazáshoz](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, egy telepített eszköz-helyi és szolgáltatás-helyi proxy alkalmazások segítségével hoz létre egy eszközt az IoT hub segítségével, és az SSH-vagy RDP-forgalom bújtatására használta a proxy alkalmazásokat. Ugyanez a paradigma más ügyfél-kiszolgáló protokollokhoz is alkalmazkodik, ahol a kiszolgáló az eszközön fut (például az SSH démon).

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
