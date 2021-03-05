---
title: Gyors útmutató – telemetria küldése az Azure IoT Hub gyors útmutatóba (Android) | Microsoft Docs
description: Ebben a rövid útmutatóban egy minta Android-alkalmazást futtat, amely szimulált telemetria küld egy IoT hubhoz, és beolvassa a telemetria a IoT hub-ból a felhőben történő feldolgozásra.
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
ms.openlocfilehash: e1e44f5aa4250d63c2addafd2661b09bae2b38e3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199917"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Gyors útmutató: IoT telemetria küldése Android-eszközről

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban egy fizikai vagy szimulált eszközön futó Android-alkalmazásból küld telemetria egy Azure-IoT Hub. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató egy előre megírt Android-alkalmazást használ a telemetria elküldéséhez. A rendszer a Azure Cloud Shell használatával olvassa be a telemetria a IoT Hub. Az alkalmazás futtatása előtt létre kell hoznia egy IoT hubot, és regisztrálnia kell egy eszközt a hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio az Android SDK 27](https://developer.android.com/studio/)használatával. További információ: [Android – telepítés](https://developer.android.com/studio/install). Az Android SDK 27 ezt a cikket használja a mintában.

* [Egy minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Ez a minta az [Azure-IOT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár részét képezi.

* A 8883-es port megnyitható a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyAndroidDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyAndroidDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket a rövid útmutató későbbi részében fogja használni telemetria küldéséhez.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

1. Nyissa meg Android Studio a GitHub-minta Android-projektet. A projekt az [Azure-IOT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) repository klónozott vagy letöltött példányának következő könyvtárában található: *\azure-IOT-Samples-java\iot-hub\Samples\device\AndroidSample*.

2. A Android Studioban nyissa meg a *gradle. properties* karakterláncot, és cserélje le a **Device_Connection_String** helyőrzőt a korábban jegyzett eszköz-összekapcsolási sztringre.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio kattintson a **file**  >  **Sync Project Gradle-fájlokkal** elemre. Ellenőrizze, hogy befejeződött-e a létrehozás.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok egyike lehet:
   >
   > * A projektben hivatkozott Android Gradle beépülő modul és Gradle verziója elavult a Android Studio verziójára vonatkozóan. Kövesse [ezeket az utasításokat](https://developer.android.com/studio/releases/gradle-plugin) , és telepítse a beépülő modul és a Gradle megfelelő verzióit a telepítéshez.
   > * Az Android SDK licencszerződése nincs aláírva. A létrehozási kimenetben szereplő utasítások alapján írja alá a licencszerződést, és töltse le az SDK-t.

4. A Build befejezése **után kattintson az**  >  **alkalmazás futtatása** elemre. Beállíthatja, hogy az alkalmazás fizikai Android-eszközön vagy Android-emulátoron fusson. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásával kapcsolatos további információkért lásd [az alkalmazás futtatása](https://developer.android.com/training/basics/firstapp/running-app)című témakört.

5. Az alkalmazás betöltése után kattintson a **Start** gombra, hogy elindítsa a telemetria küldését a IoT hubba:

    ![Alkalmazás](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a Azure Cloud Shell és a [IoT bővítmény](/cli/azure/ext/azure-iot/iot) használatával figyelheti az Android-eszköz által küldött üzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Az alábbi képernyőfelvételen a kimenet látható, mivel az IoT hub az Android-eszköz által küldött telemetria fogadja:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldése az hubhoz egy Android-alkalmazás használatával, valamint a telemetria beolvasása a központban a Azure Cloud Shell használatával.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-android.md)
