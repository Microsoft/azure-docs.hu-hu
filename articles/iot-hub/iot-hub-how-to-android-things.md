---
title: Fejlesztés az Android-eszközökhöz az Azure IoT SDK-k használatával | Microsoft Docs
description: Fejlesztői útmutató – Ismerje meg, hogyan fejleszthet Android-alapú dolgokon az Azure IoT Hub SDK-k használatával.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94843757"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Fejlesztés az Android-eszközök platformon az Azure IoT SDK-k használatával

Az [Azure IoT hub SDK](./iot-hub-devguide-sdks.md) -k biztosítják az első szintű támogatást olyan népszerű platformokhoz, mint például a Windows, a Linux, az OSX, a MBED és a mobil platformok, például az Android és az iOS.  A IoT-alapú üzembe helyezések nagyobb választékának és rugalmasságának biztosítása érdekében a Java SDK is támogatja az [Android](https://developer.android.com/things/) -eszközök platformot.  A fejlesztők igénybe vehetik az Android-eszközök operációs rendszerének előnyeit az eszköz oldalán, miközben az [Azure IoT hub](about-iot-hub.md) -t használja a központi üzenetküldési központként, amely több millió egyidejű csatlakoztatott eszközre méretezhető.

Ez az oktatóanyag azt ismerteti, hogyan lehet az Azure IoT Java SDK használatával Android-eszközökön felépíteni egy eszköz oldali alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Az Android-eszközök által támogatott hardverek Android rendszerű operációs rendszereken.  Az Android-eszközökre vonatkozó [dokumentációt](https://developer.android.com/things/get-started/kits#flash-at) követve megtekintheti a Flash Android-eszközök operációs rendszerét.  Győződjön meg arról, hogy az Android-eszközök az internethez csatlakoznak az alapvető perifériákkal, például a billentyűzettel, a megjelenítéssel és az egérrel csatlakoztatva.  Ez az oktatóanyag a málna PI 3 szolgáltatást használja.

* A [Android Studio](https://developer.android.com/studio/) legújabb verziója

* A [git](https://git-scm.com/) legújabb verziója

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához.

   **YourIoTHubName** : az alábbi helyőrző helyére írja be az IoT Hubjához választott nevet.

   **MyAndroidThingsDevice** : Ez a regisztrált eszköz nevét adja meg. Használja a MyAndroidThingsDevice az ábrán látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Futtassa az alábbi parancsokat a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó *eszköz-kapcsolódási karakterlánc* beszerzéséhez. A `YourIoTHubName` lenti helyére írja be az IoT hub számára kiválasztott nevet.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="building-an-android-things-application"></a>Android Things-alkalmazások létrehozása

1. Az Android Things-alkalmazások létrehozásának első lépése az androidos eszközökhöz való csatlakozás. Az Android-eszközök csatlakoztatása egy kijelzőhöz és az internethez csatlakoztatása. Az Android-eszközök a Wi-Fi-hez való kapcsolódással kapcsolatos [dokumentációt](https://developer.android.com/things/get-started/kits) biztosítanak. Miután csatlakozott az internethez, jegyezze fel a hálózatok területen látható IP-címet.

2. Az [ADB](https://developer.android.com/studio/command-line/adb) eszköz használatával csatlakozhat az Android-eszközökhöz a fent említett IP-címmel. A terminálon a parancs használatával ellenőrizze a kapcsolatokat. Az eszközök "csatlakoztatottként" jelennek meg.

   ```
   adb devices
   ```

3. Töltse le a mintát Android/Android rendszerű dolgokra ebből a [tárházból](https://github.com/Azure-Samples/azure-iot-samples-java) , vagy használja a git-t.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. A Android Studioban nyissa meg az Android-projektet a "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample" helyen.

5. Nyissa meg a gradle. properties fájlt, és cserélje le a "Device_connection_string" kifejezést a korábban feljegyzett eszköz-csatlakoztatási karakterlánccal.
 
6. Kattintson a Run-debug elemre, és válassza ki az eszközét, hogy az Android-eszközökre telepítse ezt a kódot.

7. Az alkalmazás sikeres elindításakor egy, az Android-eszközökön futó alkalmazás is megjelenik. Ez a minta alkalmazás véletlenszerűen generált hőmérséklet-beolvasásokat küld.

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Az IoT hub segítségével megtekintheti az adatait. Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő parancsokat az Azure Cloud Shellben úgy, hogy a `YourIoTHubName` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan kezelheti a kapcsolatot és a megbízható üzenetkezelést](iot-hub-reliability-features-in-sdks.md) az IoT hub SDK-k használatával.
* Ismerje meg, hogyan [fejleszthet a mobil platformokra](iot-hub-how-to-develop-for-mobile-devices.md) , például az iOS-re és az Androidra.
* [Azure IoT SDK-platform támogatása](iot-hub-device-sdk-platform-support.md)