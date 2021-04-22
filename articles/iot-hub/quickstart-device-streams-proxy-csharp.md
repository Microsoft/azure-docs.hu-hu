---
title: Rövid útmutató – eszközstreamek Azure IoT Hub C# rövid útmutatója SSH-hez és RDP-hez
description: Ebben a rövid útmutatóban két C#-mintaalkalmazást fog futtatni, amelyek SSH- és RDP-forgatókönyveket engedélyeznek egy IoT Hub eszközstreamen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 5bd33a2da6b2f1ae775f088950f14ac4df465fbf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863946"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése IoT Hub eszközstreamen C#-proxyalkalmazással (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub jelenleg előzetes verziójú funkcióként támogatja az [eszközstreameket.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub az eszközstreamek lehetővé](iot-hub-device-streams-overview.md) teszik, hogy a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát módon kommunikáljanak. Ez a rövid útmutató két olyan C#-alkalmazást tartalmaz, amelyek lehetővé teszik az ügyfél–kiszolgáló alkalmazás forgalmát (például Secure Shell [SSH] és RDP protokoll [RDP] egy IoT Hubon keresztül létrehozott eszközstreamen keresztül. A telepítés áttekintését lásd: Helyi [proxyalkalmazás-minta SSH-hez vagy RDP-hez.](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Ez a cikk először az SSH beállítását ismerteti (a 22-es port használatával), majd bemutatja, hogyan kell módosítani a telepítő RDP-portját. Mivel az eszközstreamek alkalmazás- és protokolltól függetlenek, ugyanez a minta módosítható más típusú alkalmazásforgalomhoz is. Ez a módosítás általában csak azt a kommunikációs portot érinti, amelyet a kívánt alkalmazás használ.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok esetében támogatott:

  * Az USA középső régiója
  * USA középső régiója – EUAP
  * Délkelet-Ázsia
  * Észak-Európa

* A rövid útmutatóban futtatott két mintaalkalmazás C# nyelven íródott. A fejlesztői .NET Core SDK 2.1.0-s vagy újabb szükséges.

    A több [platformra .NET Core SDK .NET-ről töltheti le az alkalmazásokat.](https://dotnet.microsoft.com/download)

    Ellenőrizze a C# aktuális verzióját a fejlesztői gépen a következő paranccsal:

    ```
    dotnet --version
    ```

* [Töltse le az Azure IoT C#-mintákat,](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)és bontsa ki a ZIP-archívumot.

* Érvényes felhasználói fiók és hitelesítő adat a felhasználó hitelesítéséhez használt eszközön (Windows vagy Linux).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Működés

Az alábbi ábra bemutatja, hogyan teszik lehetővé a mintában az eszköz helyi és a szolgáltatás helyi proxyalkalmazásai az SSH-ügyfél és az SSH-démonfolyamatok közötti teljes kapcsolódást. Itt feltételezzük, hogy a démon ugyanazon az eszközön fut, mint az eszköz helyi proxyalkalmazása.

![Helyi proxyalkalmazás beállítása](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. A szolgáltatás helyi proxyalkalmazása csatlakozik az IoT Hubhoz, és eszközstreamet kezdeményez a céleszközhöz.

1. Az eszköz helyi proxyalkalmazása befejezi a streamelési kezdeményezési kézfogást, és végpontok között streamelési alagutat hoz létre az IoT Hub streamvégpontjának a szolgáltatási oldal felé.

1. Az eszköz helyi proxyalkalmazása csatlakozik az eszköz 22-es portját figyelő SSH-démonhoz. Ez a beállítás konfigurálható az "Eszköz helyi proxyalkalmazás futtatása" szakaszban leírtak szerint.

1. A szolgáltatás helyi proxyalkalmazása új SSH-kapcsolatokat vár egy felhasználótól egy kijelölt port figyelése által, amely ebben az esetben a 2222-es port. Ez a beállítás konfigurálható a "Szolgáltatás helyi proxyalkalmazás futtatása" című szakaszban leírtak szerint. Amikor a felhasználó az SSH-ügyfélen keresztül csatlakozik, az alagút lehetővé teszi az SSH-alkalmazás forgalmának átvitelét az SSH-ügyfél és a kiszolgálóalkalmazás között.

> [!NOTE]
> Az eszközstreamen keresztül küldött SSH-forgalom nem közvetlenül a szolgáltatás és az eszköz között, hanem az IoT Hub streamvégpontjának bújtatásán keresztül halad át. További információ: Az Iot Hub-eszközstreamek [használatának előnyei.](iot-hub-device-streams-overview.md#benefits)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban a Azure Cloud Shell egy szimulált eszközt fog regisztrálni.

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

    Jegyezze fel a visszaadott eszközkapcsolati sztringet a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Az IoT Hubhoz való csatlakozáshoz és egy  eszközstream létrehozására az IoT Hub szolgáltatáskapcsolati sztringjére is szüksége lesz a szolgáltatásoldali alkalmazás engedélyezéséhez. A következő parancs lekéri ezt az értéket az IoT Hubhoz:

   > [!NOTE]
   > Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Jegyezze fel a visszaadott szolgáltatáskapcsolati sztringet a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-kapcsolat egy eszközhöz eszközstreamek segítségével

Ebben a szakaszban egy végpontok között található streamet hoz létre az SSH-forgalom bújtatása érdekében.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxyalkalmazásának futtatása

Egy helyi terminálablakban keresse meg a kicsomagolt projektmappában `device-streams-proxy/device` található könyvtárat. Tartsa kéznél az alábbi információkat:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz eszközkapcsolati sztringe. |
| `targetServiceHostName` | Az AZ IP-cím, ahol az SSH-kiszolgáló figyel. A cím akkor lenne, ha ugyanaz az IP-cím lenne, ahol az eszköz helyi `localhost` proxyalkalmazása fut. |
| `targetServicePort` | Az alkalmazás protokollja által használt port (SSH esetén alapértelmezés szerint ez a 22-es port).  |

Fordítsa le és futtassa a kódot a következő parancsokkal:

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

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxyalkalmazásának futtatása

Egy másik helyi terminálablakban lépjen a következőre `iot-hub/quickstarts/device-streams-proxy/service` a kicsomagolt projektmappában: . Tartsa kéznél az alábbi információkat:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | A szolgáltatás kapcsolati sztring IoT Hub. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Egy helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a példában a 2222-es portot használjuk, de más tetszőleges számokat is használhat. |

Fordítsa le és futtassa a kódot a következő parancsokkal:

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

Most használja az SSH-ügyfélalkalmazást, és csatlakozzon a szolgáltatás helyi proxyalkalmazásához a 2222-es porton (közvetlenül az SSH-démon helyett).

```
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablaka kéri a hitelesítő adatok megadását.

Konzolkimenet a szolgáltatásoldalon (a szolgáltatás helyi proxyalkalmazása a 2222-es porton figyel):

![A szolgáltatás helyi proxyalkalmazásának kimenete](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konzolkimenet az eszköz helyi proxyalkalmazásán, amely az SSH-démonhoz csatlakozik *a következő IP_address:22:*

![Az eszköz helyi proxyalkalmazásának kimenete](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Az SSH-ügyfélalkalmazás konzolkimenete. Az SSH-ügyfél a 22-es porthoz csatlakozva kommunikál az SSH-démonnal, amelyet a szolgáltatás helyi proxyalkalmazása figyel:

![SSH-ügyfélalkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP-kapcsolat egy eszközre eszközstreamek segítségével

Az RDP beállítása hasonló az SSH beállításához (lásd fent). Ehelyett az RDP-cél IP-címét és a 3389-es portot használja, az RDP-ügyfelet (az SSH-ügyfél helyett).

### <a name="run-the-device-local-proxy-application-rdp"></a>Az eszköz helyi proxyalkalmazásának (RDP) futtatása

Egy helyi terminálablakban keresse meg a kicsomagolt projektmappában `device-streams-proxy/device` található könyvtárat. Tartsa kéznél a következő információkat:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz eszközkapcsolati sztringe. |
| `targetServiceHostName` | Az az állomásnév vagy IP-cím, ahol az RDP-kiszolgáló fut. A cím akkor lenne, ha ugyanaz az IP-cím lenne, ahol az eszköz helyi `localhost` proxyalkalmazása fut. |
| `targetServicePort` | Az alkalmazás protokollja által használt port (RDP esetén alapértelmezés szerint ez a 3389-es port).  |

Fordítsa le és futtassa a kódot a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>A szolgáltatás helyi proxyalkalmazásának (RDP) futtatása

Egy másik helyi terminálablakban lépjen a következőre `device-streams-proxy/service` a kicsomagolt projektmappában: . Tartsa kéznél a következő információkat:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | A szolgáltatás kapcsolati sztring IoT Hub. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Egy helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a példában a 2222-es portot használjuk, de ezt más tetszőleges számokra is módosíthatja. |

Fordítsa le és futtassa a kódot a következő parancsokkal:

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

Most használja az RDP-ügyfélalkalmazást, és csatlakozzon a szolgáltatás helyi proxyalkalmazásához a 2222-es porton (ez egy tetszőlegesen elérhető port volt, amit korábban választott).

![Az RDP csatlakozik a szolgáltatás helyi proxyalkalmazásához](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállít egy IoT Hubot, regisztrált egy eszközt, üzembe helyezett egy eszköz helyi és egy szolgáltatás helyi proxyalkalmazást az ioT Hubon keresztüli eszközstream létrehozására, és a proxyalkalmazások használatával bújtatta az SSH- vagy RDP-forgalmat. Ugyanez a paradigma más ügyfél-kiszolgáló protokollokat is képes fogadni, amelyekben a kiszolgáló az eszközön fut (például az SSH-démon).

További információ az eszközstreamekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Eszközstreamek áttekintése](./iot-hub-device-streams-overview.md)
