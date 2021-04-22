---
title: Rövid útmutató – Telemetria küldése Azure IoT Hub (Android) | Microsoft Docs
description: Ebben a rövid útmutatóban egy Android-mintaalkalmazás futtatásával szimulált telemetriát küld egy IoT Hubra, és beolvassa a telemetriát az IoT Hubról a felhőben történő feldolgozáshoz.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: 4bc8dfb879f362463153819cdf99869522c55c1b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863820"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Rövid útmutató: IoT-telemetria küldése Android-eszközről

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban telemetriát küld egy Azure IoT Hub egy fizikai vagy szimulált eszközön futó Android-alkalmazásból. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató egy előre megírt Android-alkalmazást használ a telemetria elküldését. A telemetria a IoT Hub a Azure Cloud Shell. Az alkalmazás futtatása előtt létre kell hoznia egy IoT Hubot, és regisztrálnia kell egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio Android SDK 27-es verziójával.](https://developer.android.com/studio/) További információ: [android-installation](https://developer.android.com/studio/install). Az ebben a cikkben használt minta az Android SDK 27-et használja.

* [Minta Android-alkalmazás.](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Ez a minta az [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár része.

* A 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Ez a port blokkolható egyes vállalati és oktatási hálózati környezetekben. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

   **MyAndroidDevice:** Ez a regisztrált eszköz neve. Javasoljuk, hogy a **MyAndroidDevice-et használja** az itt látható módon. Ha egy másik nevet választ az eszközhöz, akkor a teljes cikkben ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa a következő parancsot Azure Cloud Shell a regisztrált eszköz _eszközkapcsolati_ sztringjéhez:

    **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket a rövid útmutató későbbi, telemetriai adatainak elküldését fogja használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

1. Nyissa meg a GitHub-minta Android-projektet a Android Studio. A projekt az [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár klónozott vagy letöltött másolatának alábbi könyvtárában található: *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. A Android Studio nyissa meg a *mintaprojekt gradle.properties* fájlját, és cserélje le a **Device_Connection_String** helyőrzőt a korábban feljegyző eszközkapcsolati sztringre.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. A Android Studio kattintson a **File** Sync Project with Gradle Files (Fájlszinkronizálási projekt  >  **Gradle-fájlokkal) elemre.** Ellenőrizze, hogy a build elkészült-e.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok valamelyike lehet:
   >
   > * A projektben hivatkozott Android Gradle beépülő modul és Gradle verziója elavult a Android Studio. Kövesse [ezeket az utasításokat](https://developer.android.com/studio/releases/gradle-plugin) a beépülő modul és a Gradle megfelelő verziójának a telepítéshez való hivatkozáshoz és telepítéséhez.
   > * Az Android SDK licencszerződése még nincs aláírva. A licencszerződés aláírásához és az SDK letöltéséhez kövesse a Build output (Buildkimenet) utasításait.

4. A build befejezése után kattintson a **Run**  >  **'app' (Alkalmazás futtatása) elemre.** Konfigurálja az alkalmazást fizikai Android-eszközön vagy Android-emulátoron való futtatásra. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásával kapcsolatos további információkért lásd: [Az alkalmazás futtatása.](https://developer.android.com/training/basics/firstapp/running-app)

5. Miután az alkalmazás betöltődik, kattintson a **Start** gombra a telemetria küldésének elkezdéséhez a IoT Hub:

    ![Alkalmazás](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a Azure Cloud Shell [Az IoT-bővítmény](/cli/azure/iot) használatával fogja figyelni az Android-eszköz által küldött eszközüzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Az alábbi képernyőképen a kimenet látható, amikor az IoT Hub fogadja az Android-eszköz által küldött telemetriát:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra egy Android-alkalmazással, és beolvassa a telemetriát a hubról a Azure Cloud Shell.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-android.md)
