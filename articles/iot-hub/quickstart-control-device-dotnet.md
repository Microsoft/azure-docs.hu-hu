---
title: Rövid útmutató – Eszköz vezérlése Azure IoT Hub gyorsútmutatóból (.NET) | Microsoft Docs
description: Ebben a rövid útmutatóban két C# mintaalkalmazást fog futtatni. Az egyik alkalmazás egy olyan szolgáltatásalkalmazás, amely távolról tudja vezérelni a hubhoz csatlakoztatott eszközöket. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
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
ms.openlocfilehash: 92f27c16b5dd45352a3a6cb65dd4bf78e88188f0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868550"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub azure-szolgáltatás lehetővé teszi az IoT-eszközök felhőből történő kezelését, valamint nagy mennyiségű eszköz-telemetria felhőbe való feldolgozását. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt .NET-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely egy szolgáltatásalkalmazásból hívott közvetlen metódusra válaszol. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy szolgáltatásalkalmazás, amely a közvetlen metódusokat hívja meg a szimulált eszközön. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői .NET Core SDK 3.1-es vagy annál nagyobb fejlesztésére lesz szüksége.

    A .NET Core SDK-t többféle platformra a [.NET](https://dotnet.microsoft.com/download) oldaláról töltheti le.

    A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    dotnet --version
    ```
* Ha még nem tette meg, töltse le az Azure IoT C#-mintákat a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetekben ez a port le van tiltva. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

   **MyDotnetDevice:** Ez a regisztrált eszköz neve. Javasoljuk, hogy a **MyDotnetDevice-et** használja az itt látható módon. Ha másik nevet választ az eszköz számára, akkor a cikk során is ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

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

Szüksége lesz az IoT Hub szolgáltatáskapcsolati _sztringre_ is, hogy a szolgáltatásalkalmazás csatlakozik a központhoz, és lekérni az üzeneteket. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatáskapcsolati sztring eltér az előző lépésben feljegyzett eszközkapcsolati sztringtől.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtát küld a hubnak a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\SimulatedDeviceWithCommand mappába.**

2. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminálablakban futtassa a következő parancsot a szimulálteszköz-alkalmazás felépítéséhez és futtatásához, és cserélje le a adatokat a korábban feljegyzett eszközkapcsolati `{DeviceConnectionString}` sztringre:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A szolgáltatásalkalmazás a szolgáltatásoldali végponthoz csatlakozik a IoT Hub. Az alkalmazás közvetlen metódushívásokat kezdeményez egy eszközre az IoT Hubon keresztül, és figyel a nyugtázásra. A IoT Hub szolgáltatásalkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\InvokeDeviceMethod mappára.**

2. A helyi terminálablakban futtassa a következő parancsokat a szolgáltatásalkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminálablakban futtassa a következő parancsokat a szolgáltatásalkalmazás felépítéséhez és futtatásához, és cserélje le a adatokat a korábban feljegyzett szolgáltatáskapcsolati `{ServiceConnectionString}` sztringre:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    Az alábbi képernyőképen az a kimenet látható, amikor az alkalmazás közvetlen metódushívást hoz létre az eszközre, és nyugtázást kap:

    ![A szolgáltatásalkalmazás futtatása](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    A szolgáltatásalkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és az üzenetek küldésének sebessége megváltozik:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy szolgáltatásalkalmazásból hívott meg egy közvetlen metódust egy eszközön, és válaszolt a közvetlen metódus hívására egy szimulálteszköz-alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
