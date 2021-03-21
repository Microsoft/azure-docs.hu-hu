---
title: Gyors útmutató – eszköz vezérlése az Azure IoT Hub rövid útmutatóból (Android) | Microsoft Docs
description: Ebben a rövid útmutatóban két Java-mintaalkalmazást fog futtatni. Az egyik alkalmazás olyan szolgáltatásalkalmazás, amely távolról vezérelheti az Ön hubhoz csatlakoztatott eszközöket. A másik alkalmazás a központhoz csatlakoztatott fizikai vagy szimulált eszközön fut, amely távolról vezérelhető.
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
ms.openlocfilehash: 361317729ecf868465d498cf3fcedd45d8c979db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201005"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Gyors útmutató: IoT hub-hoz csatlakoztatott eszköz vezérlése (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Ebben a rövid útmutatóban egy közvetlen módszert használ az Azure IoT Hubhoz csatlakoztatott szimulált eszköz vezérlésére. A IoT Hub egy olyan Azure-szolgáltatás, amely lehetővé teszi a IoT-eszközök Felhőbeli kezelését, és a felhőbe irányuló nagy mennyiségű eszköz telemetria történő tárolását és feldolgozását. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését. Ez a rövid útmutató két alkalmazást használ: egy szimulált eszköz alkalmazást, amely egy háttérbeli szolgáltatásalkalmazás és egy olyan szolgáltatásalkalmazás által kezdeményezett közvetlen metódusra reagál, amely meghívja az Android-eszközön a közvetlen módszert.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio az Android SDK 27](https://developer.android.com/studio/)használatával. További információ: [Install Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* Az [Azure IoT-mintákban (Java)](https://github.com/Azure-Samples/azure-iot-samples-java)található [Device SDK-minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample).

* A [Service SDK minta Android-alkalmazás](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), amely az Azure IoT-mintákban (Java) található.

* A 8883-es port megnyitható a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha befejezte az előző rövid útmutatót [: küldjön telemetria egy eszközről egy IoT hubhoz](quickstart-send-telemetry-android.md), kihagyhatja ezt a lépést, és használhatja a már létrehozott IoT hubot.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte az előző rövid útmutatót [: küldjön telemetria egy eszközről egy IoT-hubhoz](quickstart-send-telemetry-android.md), kihagyhatja ezt a lépést, és használhatja ugyanazt az eszközt, amely az előző rövid útmutatóban regisztrálva van.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyAndroidDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyAndroidDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

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

Szükség van egy _szolgáltatás-kapcsolati karakterláncra_ is, amely lehetővé teszi a háttér-szolgáltatási alkalmazások számára a IoT hub-hoz való kapcsolódást a metódusok végrehajtása és az üzenetek lekérése érdekében. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

**YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatási kapcsolatok karakterlánca különbözik az előző lépésben feljegyzett eszköz-összekapcsolási karakterlánctól.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A rövid útmutató mindkét mintája az Azure-IOT-Samples-Java adattár részét képezi a GitHubon. Az [Azure-IOT-Samples-Java](https://github.com/Azure-Samples/azure-iot-samples-java) adattár letöltése vagy klónozása.

Az eszköz SDK-minta alkalmazás futtatható fizikai Android-eszközön vagy Android-emulátoron is. A minta egy adott eszközhöz tartozó végponthoz csatlakozik az IoT hub-on, szimulált telemetria küld, és figyeli a hub Direct metódusának hívásait. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz visszaigazolást küld a hubhoz a közvetlen metódus végrehajtása után.

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

    ![Példa az ügyfél-eszköz Android-alkalmazás képernyőképére](media/quickstart-control-device-android/sample-screenshot.png)

Ezt az alkalmazást fizikai eszközön vagy emulátoron kell futtatni, amikor a Service SDK-mintát futtatja, hogy a telemetria-intervallumot a Futtatás ideje alatt frissítse.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Ebben a szakaszban a Azure Cloud Shellt a [IoT bővítménnyel](/cli/azure/ext/azure-iot/iot) fogja használni az Android-eszköz által küldött üzenetek figyeléséhez.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Az alábbi képernyőfelvételen a kimenet látható, mivel az IoT hub az Android-eszköz által küldött telemetria fogadja:

      ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-control-device-android/read-data.png)

Alapértelmezés szerint a telemetria alkalmazás öt másodpercenként küld telemetria az Android-eszközről. A következő szakaszban egy közvetlen metódus hívásával frissítheti az Android IoT-eszköz telemetria-intervallumát.

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A szolgáltatásalkalmazás egy szolgáltatás-oldali végponthoz csatlakozik a IoT Hub. Az alkalmazás lehetővé teszi, hogy a közvetlen metódus hívásokat hajtson végre egy eszközön az IoT hub segítségével, és figyelje a nyugtákat.

Az alkalmazást külön fizikai Android-eszközön vagy Android-emulátoron futtathatja.

Egy IoT Hub háttérrendszer-alkalmazás általában a felhőben fut, így könnyebben enyhíthető a bizalmas kapcsolódási karakterlánchoz kapcsolódó kockázatok, amelyek egy IoT Hub összes eszközét vezérlik. Ebben a példában csak bemutató célokra futtatjuk Android-alkalmazásként. A rövid útmutató más nyelvű verziói olyan példákat mutatnak be, amelyek szorosabban illeszkednek egy tipikus háttérbeli szolgáltatásalkalmazás-alkalmazáshoz.

1. Nyissa meg a GitHub szolgáltatási minta Android-projektjét Android Studioban. A projekt az [Azure-IOT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) repository klónozott vagy letöltött példányának következő könyvtárában található: *\azure-IOT-Samples-java\iot-hub\Samples\service\AndroidSample*.

2. A Android Studioban nyissa meg a *gradle. properties tulajdonságot* a minta projekthez. Frissítse a **ConnectionString** és a **DeviceID** tulajdonságok értékeit a korábban feljegyzett szolgáltatási kapcsolati karakterlánccal és a regisztrált Android-eszköz azonosítójával.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio kattintson a **file**  >  **Sync Project Gradle-fájlokkal** elemre. Ellenőrizze, hogy befejeződött-e a létrehozás.

   > [!NOTE]
   > Ha a projekt szinkronizálása sikertelen, a következő okok egyike lehet:
   >
   > * A projektben hivatkozott Android Gradle beépülő modul és Gradle verziója elavult a Android Studio verziójára vonatkozóan. Kövesse [ezeket az utasításokat](https://developer.android.com/studio/releases/gradle-plugin) , és telepítse a beépülő modul és a Gradle megfelelő verzióit a telepítéshez.
   > * Az Android SDK licencszerződése nincs aláírva. A létrehozási kimenetben szereplő utasítások alapján írja alá a licencszerződést, és töltse le az SDK-t.

4. A Build befejezése **után kattintson az**  >  **alkalmazás futtatása** elemre. Az alkalmazás konfigurálása külön fizikai Android-eszközön vagy Android-emulátoron való futtatáshoz. Az Android-alkalmazások fizikai eszközön vagy emulátoron való futtatásával kapcsolatos további információkért lásd [az alkalmazás futtatása](https://developer.android.com/training/basics/firstapp/running-app)című témakört.

5. Az alkalmazás betöltése után frissítse az **üzenetküldési időköz** értékét a **1000** értékre, majd kattintson a **meghívás** gombra.

    A th telemetria üzenetkezelési időköze ezredmásodpercben van. Az eszköz alapértelmezett telemetria-intervalluma 5 másodpercig van beállítva. Ez a módosítás frissíti az Android rendszerű IoT eszközt, hogy a telemetria másodpercenként elküldjék.

    ![Telemetria intervallumának megadása](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Az alkalmazás egy nyugtát kap, amely jelzi, hogy a metódus végrehajtása sikeres volt-e.

    ![Közvetlen metódus-visszaigazolás](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban közvetlen metódust hívott egy eszközön egy háttérbeli alkalmazásból, és a közvetlen metódus hívására válaszolt egy szimulált eszköz alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
