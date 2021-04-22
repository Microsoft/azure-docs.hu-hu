---
title: Rövid útmutató – Telemetria küldése Azure IoT Hub (C#) | Microsoft Docs
description: Ebben a rövid útmutatóban két C#-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
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
ms.date: 06/01/2020
ms.openlocfilehash: ec26e4a42cdeb7a693c8078c0f065241f32da949
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863982"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, majd olvasása szolgáltatásalkalmazással (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát fog küldeni egy szimulálteszköz-alkalmazásból a IoT Hub keresztül egy szolgáltatásalkalmazásnak feldolgozásra.

Ez a cikk két előre megírt C#-alkalmazást használ a telemetria küldésére: egyet a telemetria elküldésére, egyet pedig a telemetria olvasásához a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői .NET Core SDK a 3.1-es vagy annál nagyobb virtuális gépre lesz szüksége.

    A .NET Core SDK-t többféle platformra a [.NET](https://dotnet.microsoft.com/download) oldaláról töltheti le.

    A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK a 3.1-es vagy nagyobb kódot javasoljuk, hogy lefordítsa Event Hubs rövid útmutatóban a telemetria olvasására használt szolgáltatáskódot.


* Töltse le az Azure IoT C#-mintákat a címről, [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) és bontsa ki a ZIP-archívumot.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

   **MyDotnetDevice:** Ez a regisztrált eszköz neve. Javasoljuk, hogy a **MyDotnetDevice-et** használja az itt látható módon. Ha másik nevet választ az eszköznek, akkor a cikk során is ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Futtassa a következő parancsot Azure Cloud Shell a regisztrált eszköz _eszközkapcsolati_ sztringjéhez:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket a rövid útmutató későbbi, későbbi, későbbiekben fogja használni.

3. Szüksége lesz továbbá az _Event Hubs-kompatibilis_ végpontra, _Event Hubs-kompatibilis_ elérési  útra és szolgáltatás elsődleges kulcsára az IoT Hubról, hogy a szolgáltatásalkalmazás csatlakozik az IoT Hubhoz és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet a rövid útmutató későbbi része fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\SimulatedDevice mappára.**

2. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminálablakban futtassa a következő parancsot a szimulálteszköz-alkalmazás felépítéséhez és futtatásához a korábban feljegyelt eszközkapcsolati sztring használatával:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

A szolgáltatásalkalmazás a szolgáltatásoldali események végponthoz **csatlakozik** a IoT Hub. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. A IoT Hub szolgáltatásalkalmazások általában a felhőben futnak az eszközről a felhőbe küldött üzenetek fogadásához és feldolgozásához.

1. Egy másik helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\ReadD2cMessages mappába.**

2. A helyi terminálablakban futtassa a következő parancsot az alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminálablakban futtassa a következő parancsot a paraméterbeállításokat.

    ```cmd/sh
    dotnet run
    ```

4. A helyi terminálablakban futtassa az alábbi parancsok egyikét az alkalmazás a paranccsal történő buildálhoz és futtatásához:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    vagy

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    Az alábbi képernyőképen az a kimenet látható, amikor a szolgáltatásalkalmazás fogadja a szimulált eszköz által a hubnak küldött telemetriát:

    ![A szolgáltatásalkalmazás futtatása](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra egy C#-alkalmazással, és beolvasta a telemetriát a hubról egy egyszerű szolgáltatásalkalmazással.

Ha meg szeretne ismerkedni a szimulált eszköz szolgáltatásalkalmazásból való vezérlésével, folytassa a következő rövid útmutatóval.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-dotnet.md)
