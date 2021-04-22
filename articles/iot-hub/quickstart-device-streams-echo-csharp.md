---
title: Rövid útmutató – Kommunikáció az eszközalkalmazással C# használatával Azure IoT Hub eszközstreamekkel
description: Ebben a rövid útmutatóban két C#-mintaalkalmazást fog futtatni, amelyek egy olyan eszközstreamen keresztül kommunikálnak, amely a IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c4f6e06524412c76661623cb5ef2985a41f2d7fc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864079"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rövid útmutató: Eszközstreamek (előzetes verzió) IoT Hub kommunikáció egy eszközalkalmazással C#-ban

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub jelenleg előzetes verziójú funkcióként támogatja az [eszközstreameket.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub az eszközstreamek lehetővé](./iot-hub-device-streams-overview.md) teszik, hogy a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát módon kommunikáljanak. Ez a rövid útmutató két olyan C#-alkalmazást tartalmaz, amelyek az eszközstreamek előnyeit kihasználva küldenek adatokat oda-vissza (echo).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok esetében támogatott:
  * Az USA középső régiója
  * USA középső régiója – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

* A rövid útmutatóban futtatott két mintaalkalmazás C# nyelven íródott. A fejlesztői .NET Core SDK 2.1.0-s vagy újabb szükséges.

    Töltse le [a .NET Core SDK több platformra a .NET-ről.](https://dotnet.microsoft.com/download)

    Ellenőrizze a C# aktuális verzióját a fejlesztői gépen a következő paranccsal:

    ```
    dotnet --version
    ```

* [Töltse le az Azure IoT C#-mintákat,](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) és bontsa ki a ZIP-archívumot. Az eszköz és a szolgáltatás oldalán is szüksége lesz rá.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell egy szimulált eszközt fog regisztrálni.

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

3. Szüksége lesz  az IoT Hub szolgáltatáskapcsolati sztringjére is, hogy a szolgáltatásoldali alkalmazás csatlakozik az IoT Hubhoz, és létrehoz egy eszközstreamet. A következő parancs lekéri ezt az értéket az IoT Hubhoz:

   > [!NOTE]
   > Cserélje le *a YourIoTHubName* helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Jegyezze fel a visszaadott szolgáltatáskapcsolati sztringet a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Az eszköz és a szolgáltatás közötti kommunikáció eszközstreamek útján

Ebben a szakaszban az eszközoldali alkalmazást és a szolgáltatásoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-service-side-application"></a>A szolgáltatásoldali alkalmazás futtatása

Egy helyi terminálablakban keresse meg a kicsomagolt projektmappában `iot-hub/Quickstarts/device-streams-echo/service` található könyvtárat. Tartsa kéznél az alábbi információkat:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Az IoT Hub szolgáltatáskapcsolati sztringjét. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |

Fordítsa le és futtassa a kódot a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Az alkalmazás megvárja, amíg az eszközalkalmazás elérhetővé válik.

> [!NOTE]
> Időtúllépés történik, ha az eszközoldali alkalmazás nem válaszol időben.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Egy másik helyi terminálablakban keresse meg a kicsomagolt projektmappában `iot-hub/Quickstarts/device-streams-echo/device` található könyvtárat. Tartsa kéznél az alábbi információkat:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A számítógép eszközkapcsolati sztring IoT Hub. |

Fordítsa le és futtassa a kódot a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Az utolsó lépés végén a szolgáltatásoldali alkalmazás elindít egy streamet az eszközre. A stream létrejötte után az alkalmazás egy sztringpuffert küld a szolgáltatásnak a streamen keresztül. Ebben a példában a szolgáltatásoldali alkalmazás egyszerűen megismétli ugyanezeket az adatokat az eszközre, ami a két alkalmazás közötti sikeres kétirányú kommunikációt mutatja be.

Konzolkimenet az eszközoldalon:

![Konzolkimenet az eszközoldalon](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konzolkimenet a szolgáltatásoldalon:

![Konzolkimenet a szolgáltatás oldalán](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

A streamen keresztül küldött forgalom nem közvetlenül, hanem az IoT Hubon keresztül van bújtatva. A biztosított előnyöket az [Eszközstreamek előnyei között részletezi.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, létrehozott egy eszközstreamet az eszköz és a szolgáltatás oldalán található C#-alkalmazások között, és a stream segítségével adatokat küldött az alkalmazások között.

További információ az eszközstreamekkel kapcsolatban:

> [!div class="nextstepaction"]
> [Eszközstreamek áttekintése](./iot-hub-device-streams-overview.md)
