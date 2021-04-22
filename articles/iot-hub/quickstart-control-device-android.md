---
title: Rövid útmutató – Eszköz vezérlése Azure IoT Hub (Android) | Microsoft Docs
description: Ebben a rövid útmutatóban két Java-mintaalkalmazást fog futtatni. Az egyik alkalmazás egy olyan szolgáltatásalkalmazás, amely távolról tudja vezérelni a hubhoz csatlakoztatott eszközöket. A másik alkalmazás a hubhoz csatlakoztatott fizikai vagy szimulált eszközön fut, amely távolról vezérelhető.
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
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5946220a688206a35d0d68cd4efd4c356d27d7ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868538"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Rövid útmutató: IoT Hubhoz csatlakoztatott eszköz vezérlése (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Ebben a rövid útmutatóban egy közvetlen metódussal fogja vezérelni az eszközhöz csatlakoztatott szimulált Azure IoT Hub. IoT Hub azure-szolgáltatás lehetővé teszi az IoT-eszközök felhőből történő kezelését, és nagy mennyiségű eszköz-telemetria felhőbe való feldolgozását. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését. Ez a rövid útmutató két alkalmazást használ: egy szimulálteszköz-alkalmazást, amely egy háttérszolgáltatás-alkalmazásból hívott közvetlen metódusra válaszol, és egy szolgáltatásalkalmazást, amely a közvetlen metódust hívja meg az Android-eszközön.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio Android SDK 27-es verziójával.](https://developer.android.com/studio/) További információ: [Install Android Studio.](https://developer.android.com/studio/install)

* [Git](https://git-scm.com/download/): .

* [Eszközoldali SDK-minta Android-alkalmazás,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)amely az [Azure IoT-minták (Java) része.](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Service SDK minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), amely az Azure IoT-minták (Java) része.

* A 8883-as port nyitva van a tűzfalon. A rövid útmutatóban található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetek blokkolják ezt a portot. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha befejezte az előző rövid [útmutatót:](quickstart-send-telemetry-android.md)Telemetria küldése egy eszközről egy IoT Hubra, kihagyhatja ezt a lépést, és használhatja a már létrehozott IoT Hubot.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte az előző rövid [útmutatót:](quickstart-send-telemetry-android.md)Telemetria küldése egy eszközről egy IoT Hubra, kihagyhatja ezt a lépést, és használhatja az előző rövid útmutatóban regisztrált eszközt.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot a Azure Cloud Shell az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

   **MyAndroidDevice:** Ez a regisztrált eszköz neve. Javasoljuk, hogy a **MyAndroidDevice-et használja** az itt látható módon. Ha másik nevet választ az eszköz számára, akkor a cikk során is ezt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell az eszköz nevét.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Szüksége lesz egy szolgáltatáskapcsolati sztringre is, amely lehetővé _teszi,_ hogy a háttérszolgáltatás-alkalmazások kapcsolódhatnak az IoT Hubhoz a metódusok végrehajtásához és az üzenetek lekéréséhez. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

**YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatáskapcsolati sztring eltér az előző lépésben feljegyzett eszközkapcsolati sztringtől.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A rövid útmutató mindkét mintája az azure-iot-samples-java adattár része a GitHubon. Töltse le vagy klónozza az [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) adattárat.

Az eszközOLDALI SDK mintaalkalmazás fizikai Android-eszközön vagy Android-emulátoron futtatható. A minta egy eszközspecifikus végponthoz csatlakozik az IoT Hubon, szimulált telemetriát küld, és a hubról származó közvetlen metódushívásokat figyel. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtát küld a hubnak a közvetlen metódus végrehajtása után.

1. Nyissa meg a GitHub-minta Android-projektet a Android Studio. A projekt az [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár klónozott vagy letöltött másolatának alábbi könyvtárában található: *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. A Android Studio nyissa meg a *mintaprojekt gradle.properties* fájlját, és cserélje le a **Device_Connection_String** helyőrzőt a korábban feljegyző eszközkapcsolati sztringre.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. A Android Studio kattintson a **File** Sync Project with Gradle Files (Fájlszinkronizálási projekt  >  **Gradle-fájlokkal) elemre.** Ellenőrizze, hogy a build elkészült-e.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, az a következő okok valamelyike miatt lehet:
   >
   > * A projektben hivatkozott Android Gradle beépülő modul és a Gradle verziója elavult a Android Studio. Kövesse [ezeket az utasításokat](https://developer.android.com/studio/releases/gradle-plugin) a beépülő modul és a Gradle megfelelő verzióinak a telepítéshez való hivatkozáshoz és telepítéséhez.
   > * Az Android SDK licencszerződése még nincs aláírva. A licencszerződés aláírásához és az SDK letöltéséhez kövesse a Build output (Buildkimenet) utasításait.

4. A build elkészülte után kattintson a **Run**  >  **'app' (Alkalmazás futtatása) parancsra.** Konfigurálja az alkalmazást úgy, hogy fizikai Android-eszközön vagy Android-emulátoron fusson. További információ az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásáról: [Az alkalmazás futtatása.](https://developer.android.com/training/basics/firstapp/running-app)

5. Miután az alkalmazás betöltődik, kattintson a **Start** gombra a telemetriai adatok küldésének IoT Hub:

    ![Minta képernyőkép az ügyféleszköz Android-alkalmazásról](media/quickstart-control-device-android/sample-screenshot.png)

Az alkalmazást futnia kell egy fizikai eszközön vagy emulátoron, miközben Ön végrehajtja a szolgáltatásoldali SDK-mintát a telemetriai időköz frissítéséhez a futásidő alatt.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a Azure Cloud Shell [Az IoT-bővítmény](/cli/azure/iot) használatával fogja figyelni az Android-eszköz által küldött üzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Az alábbi képernyőképen a kimenet látható, amikor az IoT Hub fogadja az Android-eszköz által küldött telemetriát:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-control-device-android/read-data.png)

Alapértelmezés szerint a telemetriai alkalmazás öt másodpercenként telemetriát küld az Android-eszközről. A következő szakaszban egy közvetlen metódushívással frissítheti az Android IoT-eszköz telemetriai időközét.

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A szolgáltatásalkalmazás a szolgáltatásoldali végponthoz csatlakozik a IoT Hub. Az alkalmazás közvetlen metódushívásokat kezdeményez egy eszközre az IoT Hubon keresztül, és figyel a nyugtázásra.

Futtassa ezt az alkalmazást egy külön fizikai Android-eszközön vagy Android-emulátoron.

A IoT Hub háttérszolgáltatás-alkalmazás általában a felhőben fut, ahol egyszerűbb mérsékelni a bizalmas kapcsolati sztringhez kapcsolódó kockázatokat, amely a felhőben futó összes eszközt IoT Hub. Ebben a példában csak bemutató céllal futtatjuk Android-alkalmazásként. A rövid útmutató más nyelvű verziói olyan példákat tartalmaznak, amelyek jobban illeszkednek egy tipikus háttérszolgáltatás-alkalmazáshoz.

1. Nyissa meg a GitHub szolgáltatás androidos mintaprojektjét a Android Studio. A projekt az [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár klónozott vagy letöltött másolatának alábbi könyvtárában található: *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample*.

2. A Android Studio nyissa meg a *mintaprojekt gradle.properties* fájlját. Frissítse a **ConnectionString** és **a DeviceId** tulajdonság értékeit a korábban feljegyzett szolgáltatáskapcsolati sztringre és a regisztrált Android-eszközazonosítóra.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. A Android Studio kattintson a **File** Sync Project with Gradle Files (Fájlszinkronizálási projekt  >  **Gradle-fájlokkal) elemre.** Ellenőrizze, hogy a build elkészült-e.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok valamelyike lehet:
   >
   > * A projektben hivatkozott Android Gradle beépülő modul és Gradle verziója elavult a Android Studio. Kövesse [ezeket az utasításokat](https://developer.android.com/studio/releases/gradle-plugin) a beépülő modul és a Gradle megfelelő verziójának a telepítéshez való hivatkozáshoz és telepítéséhez.
   > * Az Android SDK licencszerződése még nincs aláírva. A licencszerződés aláírásához és az SDK letöltéséhez kövesse a Build output (Buildkimenet) utasításait.

4. A build befejezése után kattintson a **Run**  >  **'app' (Alkalmazás futtatása) elemre.** Konfigurálja az alkalmazást úgy, hogy külön fizikai Android-eszközön vagy Android-emulátoron fusson. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásával kapcsolatos további információkért lásd: [Az alkalmazás futtatása.](https://developer.android.com/training/basics/firstapp/running-app)

5. Az alkalmazás betöltése után frissítse az **Üzenetkezelési időköz beállítása** értékét **1000-re,** majd kattintson a **Meghívás gombra.**

    A telemetriai üzenetkezelés időköze ezredmásodpercben van meg. Az eszközminta alapértelmezett telemetria-időköze 5 másodpercre van beállítva. Ez a módosítás frissíti az Android IoT-eszközt, hogy a rendszer másodpercenként telemetriát küld.

    ![Adja meg a telemetria időközét](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Az alkalmazás egy nyugtát kap, amely jelzi, hogy a metódus sikeresen végre lett-e hajtva.

    ![Közvetlen metódus nyugtázása](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy háttéralkalmazásból hívott meg egy közvetlen metódust egy eszközön, és válaszolt a közvetlen metódushívásra egy szimulálteszköz-alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
