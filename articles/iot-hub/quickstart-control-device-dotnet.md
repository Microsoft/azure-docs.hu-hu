---
title: Gyors útmutató – eszköz vezérlése az Azure IoT Hub rövid útmutatóból (.NET) | Microsoft Docs
description: Ebben a rövid útmutatóban két C# mintaalkalmazást fog futtatni. Az egyik alkalmazás olyan szolgáltatásalkalmazás, amely távolról vezérelheti az Ön hubhoz csatlakoztatott eszközöket. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 03/04/2020
ms.openlocfilehash: 28d80a20c50f846146aed069028303e86426df27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072085"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

A IoT Hub egy olyan Azure-szolgáltatás, amely lehetővé teszi a IoT-eszközök Felhőbeli kezelését, valamint a felhőbe irányuló nagy mennyiségű eszköz telemetria történő tárolását és feldolgozását. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt .NET-alkalmazást használ:

* Olyan szimulált eszköz alkalmazás, amely egy szolgáltatásalkalmazás által hívott közvetlen metódusokra válaszol. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy szolgáltatásalkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközön. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen a .NET Core SDK 3,1-es vagy újabb szükséges.

    A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

    A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    dotnet --version
    ```
* Ha még nem tette meg, töltse le az Azure IoT C#-mintákat, https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip és bontsa ki a zip-archívumot.

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyDotnetDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyDotnetDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Az IoT hub _szolgáltatás kapcsolati karakterláncára_ is szükség van ahhoz, hogy a szolgáltatásalkalmazás csatlakozhasson a hubhoz, és lekérje az üzeneteket. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatási kapcsolatok karakterlánca különbözik az előző lépésben feljegyzett eszköz-összekapcsolási karakterlánctól.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz visszaigazolást küld a hubhoz a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Quickstarts\SimulatedDeviceWithCommand** mappát.

2. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminál ablakban futtassa a következő parancsot a szimulált eszköz alkalmazásának létrehozásához és futtatásához, és cserélje `{DeviceConnectionString}` le a kifejezést a korábban feljegyzett eszköz-kapcsolódási sztringre:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A szolgáltatásalkalmazás egy szolgáltatás-oldali végponthoz csatlakozik a IoT Hub. Az alkalmazás lehetővé teszi, hogy a közvetlen metódus hívásokat hajtson végre egy eszközön az IoT hub segítségével, és figyelje a nyugtákat. IoT Hub szolgáltatásalkalmazás általában a felhőben fut.

1. Egy másik helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Quickstarts\InvokeDeviceMethod** mappát.

2. A helyi terminál ablakban futtassa a következő parancsokat a szolgáltatásalkalmazás szükséges kódtárainak telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminál ablakban futtassa a következő parancsokat a szolgáltatásalkalmazás létrehozásához és futtatásához, és cserélje `{ServiceConnectionString}` le a kifejezést a korábban feljegyzett szolgáltatás-kapcsolódási sztringre:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Az alábbi képernyőfelvételen a kimenet látható, mivel az alkalmazás közvetlen metódust hív meg az eszköznek, és nyugtát kap:

    ![Szolgáltatásalkalmazás futtatása](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    A szolgáltatásalkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzol ablakban, valamint az üzenetek módosításának gyakorisága:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban közvetlen metódust kapott egy szolgáltatásalkalmazás egyik eszközén, és egy szimulált eszköz alkalmazásban válaszolt a közvetlen metódus hívására.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
