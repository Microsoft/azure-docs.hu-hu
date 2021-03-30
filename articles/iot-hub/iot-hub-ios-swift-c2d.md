---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (iOS) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről az iOS-hez készült Azure IoT SDK-k használatával.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 15c0df33b8f09ec71f2be913d72f0785dc766375
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027533"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Üzenetküldés a felhőből az eszközre IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-ios.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

Ez az oktatóanyag a következőket mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről a [IoT hub fejlesztői útmutató üzenetkezelés szakaszában](iot-hub-devguide-messaging.md)talál további információt.

A cikk végén két gyors iOS-projektet futtat:

* **minta – eszköz**, ugyanaz az alkalmazás, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-ios.md), amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* **minta-szolgáltatás**, amely egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

> [!NOTE]
> A IoT Hub számos eszköz platformjának és nyelvének (például C, Java, Python és JavaScript) támogatásával rendelkezik az Azure IoT Device SDK-k használatával. Az eszköznek az oktatóanyag kódjához való csatlakoztatásának részletes ismertetését, és általában az Azure IoT Hub az Azure [IoT fejlesztői központját](https://www.azure.com/develop/iot)tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Aktív IoT hub az Azure-ban.

* Az [Azure-mintákból](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)származó kód mintája.

* Az iOS SDK legújabb verzióját futtató [XCode](https://developer.apple.com/xcode/) legújabb verziója. A rövid útmutató tesztelése az XCode 9.3-as és az iOS 11.3-as verziójával történt.

* A [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) legújabb verziója.

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>IoT-eszköz szimulálása

Ebben a szakaszban egy gyors alkalmazást futtató iOS-eszközt szimulál egy felhőből az eszközre irányuló üzenetek fogadásához az IoT hub-ból. 

Ez a minta eszköz, amelyet a cikk a [telemetria küldése az eszközről az IoT-hubhoz](quickstart-send-telemetry-ios.md)című cikkben hozott létre. Ha már rendelkezik a futtatásával, kihagyhatja ezt a szakaszt.

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Egy terminálablakban lépjen az Azure-IoT-Samples-iOS mappához, amelyet az előfeltételek részben töltött le. Ezután lépjen a mintaprojekthez:

```sh
cd quickstart/sample-device
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára.

### <a name="run-the-sample-device-application"></a>A minta eszköz alkalmazás futtatása

1. Az eszközhöz tartozó kapcsolatok karakterláncának beolvasása. Ezt a karakterláncot a [Azure Portal](https://portal.azure.com) az eszköz részletei panelen másolhatja, vagy lekérheti az alábbi CLI-paranccsal:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Bontsa ki a **MQTT-ügyfél minta** projektet, majd az azonos nevű mappát.  

3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

4. Keresse meg a **ConnectionString** változót, és frissítse az értéket az első lépésben másolt eszköz kapcsolati karakterláncával.

5. Mentse a módosításokat. 

6. Futtassa a projektet az eszközemulátorban a **Létrehozás és futtatás** gombbal vagy a **command + r** billentyűkombinációval.

   ![Képernyőfelvétel: az eszköz-emulátorban megjelenik a létrehozás és Futtatás gomb.](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebből a cikkből megtudhatja, hogyan küldhet el egy háttér-szolgáltatást a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése az eszközről az IoT hubhoz](quickstart-send-telemetry-ios.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Szolgáltatás eszközének szimulálása

Ebben a szakaszban egy második iOS-eszközt szimulál egy Swift-alkalmazással, amely a felhőből az eszközre irányuló üzeneteket küld az IoT hub használatával. Ez a konfiguráció olyan IoT-forgatókönyvekhez hasznos, amelyekben egyetlen iPhone vagy iPad működik vezérlőként az IoT hub-hoz csatlakoztatott egyéb iOS-eszközökhöz.

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Navigáljon az előfeltételekben letöltött Azure IoT iOS-minták mappájához. Ezután navigáljon a minta szolgáltatás projekthez:

```sh
cd quickstart/sample-service
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára.

### <a name="run-the-sample-service-application"></a>A minta szolgáltatásalkalmazás futtatása

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Bontsa ki a **AzureIoTServiceSample** projektet, majd bontsa ki az azonos nevű mappát.  

3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 

4. Keresse meg a **ConnectionString** változót, és frissítse az értéket a korábban a [IoT hub kapcsolati karakterlánc lekérése](#get-the-iot-hub-connection-string)során másolt szolgáltatás kapcsolati karakterláncával.

5. Mentse a módosításokat.

6. A Xcode-ben módosítsa az emulátor beállításait egy másik iOS-eszközre, mint amennyit a IoT-eszköz futtatásához használt. A XCode nem tud több azonos típusú emulátort futtatni.

   ![Emulátor eszköz módosítása](media/iot-hub-ios-swift-c2d/change-device.png)

7. Futtassa a projektet az eszköz-emulátorban a **Build és a Futtatás** gombbal, vagy a Key combo **Command + r** billentyűkombinációval.

   ![A képernyőképen a létrehozás és Futtatás gomb látható.](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Most már készen áll a két alkalmazás használatára a felhőből az eszközre irányuló üzenetek küldéséhez és fogadásához.

1. A szimulált IoT eszközön futó **iOS app Sample** alkalmazásban kattintson a **Start** gombra. Az alkalmazás megkezdi az eszközről a felhőbe irányuló üzenetek küldését, de megkezdi a felhőből az eszközre irányuló üzenetek figyelését is.

   ![Példa IoT-eszköz alkalmazásának megtekintése](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. A szimulált eszközön futó **IoTHub szolgáltatás ügyféloldali** alkalmazásában adja meg annak az azonosítónak a IoT-eszközét, amelyhez üzenetet szeretne küldeni. 

3. Írjon egy egyszerű szöveges üzenetet, majd kattintson a **Küldés** gombra.

    A Send (küldés) gombra kattintás után több művelet is megtörténik. A szolgáltatás mintája elküldi az üzenetet az IoT hubhoz, amelyhez az alkalmazás hozzáfér a megadott szolgáltatási kapcsolati karakterlánc miatt. Az IoT hub ellenőrzi az eszköz AZONOSÍTÓját, elküldi az üzenetet a céleszköz felé, és visszaigazolást küld a forrás eszköznek. A szimulált IoT eszközön futó alkalmazás ellenőrzi, hogy az üzenetek IoT Hub-e, és kinyomtatja a képernyőn megjelenő legutóbb egy szöveget.

    A kimenetnek az alábbi példához hasonlóan kell kinéznie:

   ![Felhőből az eszközre irányuló üzenetek megtekintése](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hub használó teljes körű megoldásokat, tekintse meg az [Azure IoT megoldás-gyorsító](https://azure.microsoft.com/documentation/suites/iot-suite/) dokumentációját.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
