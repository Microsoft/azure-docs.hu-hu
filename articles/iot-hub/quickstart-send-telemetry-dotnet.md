---
title: Gyors útmutató – telemetria küldése az Azure IoT Hub gyors útmutatóba (C#) | Microsoft Docs
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
ms.openlocfilehash: 1a681cd3f1c624268fdc9bc62cf9841e9ac28827
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057507"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Gyors útmutató: telemetria küldése egy eszközről egy IoT-hubhoz, és elolvasása egy szolgáltatásalkalmazás (.NET) használatával

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban a telemetria egy szimulált eszköz alkalmazásból, IoT Hubon keresztül küldi el a feldolgozásra.

Ez a cikk két előre megírt C#-alkalmazást használ a telemetria küldésére: egyet a telemetria elküldésére, egyet pedig a telemetria olvasásához a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen a .NET Core SDK 3,1-es vagy újabb szükséges.

    A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

    A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK 3,1 vagy újabb verzió használata ajánlott a telemetria olvasásához használt Event Hubs-szolgáltatási kód fordításához.


* Töltse le az Azure IoT C#-mintákat [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) , és bontsa ki a zip-archívumot.

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyDotnetDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyDotnetDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

3. A IoT hub _Event Hubs-kompatibilis végpontja_, _Event Hubs-kompatibilis útvonala_ és a _szolgáltatás elsődleges kulcsa_ is szükséges ahhoz, hogy a szolgáltatásalkalmazás csatlakozni tudjanak az IoT hubhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet később a rövid útmutatóban fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Quickstarts\SimulatedDevice** mappát.

2. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminál ablakban futtassa a következő parancsot a szimulált eszközhöz tartozó alkalmazás létrehozásához és futtatásához a korábban jegyzett eszköz-kapcsolódási karakterlánccal:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

A szolgáltatásalkalmazás csatlakozik a IoT Hub a szolgáltatás-oldali **események** végponthoz. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Egy IoT Hub szolgáltatásalkalmazás általában a felhőben fut az eszközről a felhőbe irányuló üzenetek fogadásához és feldolgozásához.

1. Egy másik helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Quickstarts\ReadD2cMessages** mappát.

2. A helyi terminál ablakban futtassa a következő parancsot az alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

3. A helyi terminál ablakban a következő parancs futtatásával tekintheti meg a paraméter beállításait.

    ```cmd/sh
    dotnet run
    ```

4. A helyi terminál ablakban futtassa az alábbi parancs egyikét az alkalmazás létrehozásához és futtatásához a következővel:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    vagy

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    A következő képernyőképen látható a kimenet, mivel a szolgáltatásalkalmazás a szimulált eszköz által a hubhoz küldött telemetria fogadja:

    ![Szolgáltatásalkalmazás futtatása](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldése a hubhoz egy C#-alkalmazás használatával, valamint egy egyszerű szolgáltatásalkalmazás használatával olvassa be a telemetria.

Ha szeretné megtudni, hogyan vezérelheti a szimulált eszközt egy szolgáltatásalkalmazás használatával, folytassa a következő rövid útmutatóval.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-dotnet.md)
