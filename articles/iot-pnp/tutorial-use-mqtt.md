---
title: Oktatóanyag – Azure IoT-Plug and Play eszköz-ügyfél létrehozása a MQTT használatával | Microsoft Docs
description: Oktatóanyag – a MQTT protokoll használata közvetlenül egy IoT Plug and Play-ügyfél létrehozásához az Azure IoT-eszköz SDK-k használata nélkül
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 64ff4615c2b41e341352dce5143d48ec8e6e802a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066772"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Oktatóanyag – a MQTT használata IoT Plug and Play eszköz ügyfelének fejlesztéséhez

Az Azure IoT-eszközök SDK-k egyikével kell felépíteni a IoT Plug and Play-eszköz ügyfeleit, ha ez egyáltalán lehetséges. Olyan forgatókönyvekben azonban, mint például a memóriában korlátozott eszköz használata, előfordulhat, hogy egy MQTT-függvénytárat kell használnia az IoT hub-vel való kommunikációhoz.

Az oktatóanyagban szereplő minta az [Eclipse Mosquitto](http://mosquitto.org/) MQTT könyvtárat és a Visual studiót használja. Az oktatóanyag lépései azt feltételezik, hogy a Windowst használja a fejlesztői gépen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Töltse le és hozza létre az Eclipse Mosquitto könyvtárat.
> * Módosítsa a C-alapú MQTT mintát, hogy az eszköz IoT Plug and Play eszköz legyen.
> * Azonosítsa a IoT Plug and Play eszköz által használt MQTT témákat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) a C++ számítási feladatait is tartalmazza az **asztali fejlesztés**
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Az *Azure IoT Explorer* eszköz használatával hozzáadhat egy új eszközt a IoT hubhoz. Az IoT hub és az Azure IoT Explorer eszköz konfigurálásakor a [környezet beállítása a IoT-Plug and Play rövid útmutatók és oktatóanyagok számára](set-up-environment.md)készült:

1. Indítsa el az **Azure IoT Explorer** eszközt.
1. Az **IoT-hubok** lapon válassza az **eszközök megtekintése** ebben a központban lehetőséget.
1. Az **eszközök** lapon válassza az **+ új** lehetőséget.
1. Hozzon létre egy *My-mqtt-Device* nevű eszközt, amely egy automatikusan létrehozott szimmetrikus kulcsot használ.
1. Az **eszköz identitása** lapon bontsa ki a **kapcsolati sztring sas-tokenrel elemet**.
1. Válassza ki a **szimmetrikus kulcsként** használandó **elsődleges kulcsot** , állítsa a lejárati időt 60 percre, majd válassza a **Létrehozás** lehetőséget.
1. Másolja a generált **sas-jogkivonat kapcsolati karakterláncát**, ezt az értéket később az oktatóanyagban használja.

## <a name="clone-sample-repo"></a>A klónozott minta tárháza

Használja a következő parancsot a minta tárház klónozásához a helyi gépen található megfelelő helyre:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>A MQTT-könyvtár telepítése

Az `vcpkg` eszköz használatával letöltheti és felépítheti az Eclipse Mosquitto könyvtárat.

Használja a következő parancsot a **Vcpkg** -tárháznak a helyi gépen található megfelelő helyre történő klónozásához:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

A környezet előkészítéséhez használja a következő parancsokat `vcpkg` . A futtatásakor rendszergazda jogú parancssor szükséges `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Az Eclipse Mosquitto könyvtárának letöltéséhez és létrehozásához használja a következő parancsot:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>A minta migrálása a IoT-Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>A nem IoT Plug and Play mintakód áttekintése

Frissítse a kódot az IoT hub és az eszköz részletes adataival, mielőtt felépíti és futtatja.

Ha a Visual Studióban szeretné megtekinteni a mintakódt, nyissa meg a *MQTTWin32. SLN* Solution fájlt a *IoTMQTTSample\src\Windows* mappában.

**Megoldáskezelő** kattintson a jobb gombbal a **TelemetryMQTTWin32** projektre, és válassza a **beállítás indítási projektként** lehetőséget.

A **TelemetryMQTTWin32** projektben nyissa meg a **MQTT_Mosquitto. cpp** forrásfájlt. Frissítse a kapcsolati adatok definícióit a korábban létrehozott eszköz adataival. Cserélje le a token sztring helyőrzőit a következőre:

* `IOTHUBNAME` azonosító az IoT hub nevével.
* `DEVICEID` azonosító és `my-mqtt-device` .
* `PWD` az azonosító az eszközhöz generált SAS-jogkivonat kapcsolati karakterláncának megfelelő részével. Használja a kapcsolati sztring részét `SharedAccessSignature sr=` egészen a végéig.

Győződjön meg arról, hogy a kód megfelelően működik, az Azure IoT Explorer indításával megkezdheti a telemetria figyelését.

Futtassa az alkalmazást (CTRL + F5), néhány másodperc elteltével a következőhöz hasonló kimenet jelenik meg:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT-minta alkalmazás kimenete":::

Az Azure IoT Explorerben láthatja, hogy az eszköz nem IoT Plug and Play eszköz:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Nem IoT Plug and Play eszköz az Azure IoT Explorerben":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Az eszköz IoT Plug and Play eszközvé tétele

A IoT Plug and Play eszköznek egyszerű konvenciókat kell követnie. Ha egy eszköz a csatlakozáskor egy modell-azonosítót küld, a IoT Plug and Play eszköz lesz.

Ebben a példában egy modell AZONOSÍTÓját adja hozzá a MQTT-kapcsolati csomaghoz. Adja át a modell AZONOSÍTÓját a querystring paraméterként, `USERNAME` és módosítsa a következőt `api-version` `2020-09-30` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

A minta újraépítése és futtatása.

Az eszköz Twin csomag tartalmazza a modell AZONOSÍTÓját:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="A modell AZONOSÍTÓjának megtekintése az Azure IoT Explorerben":::

Most már megtekintheti a IoT Plug and Play összetevőjét:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Összetevők megtekintése az Azure IoT Explorerben":::

Mostantól módosíthatja az eszköz kódját a modellben definiált telemetria, tulajdonságok és parancsok megvalósításához. Ha szeretné megtekinteni egy példát a termosztátos eszköz Mosquitto-függvénytár használatával történő megvalósítására, tekintse [meg a MQTT PnP használata az Azure IoTHub szolgáltatással a IOT SDK nélkül](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) a githubon.

> [!NOTE]
>Az ügyfél a `IoTHubRootCA_Baltimore.pem` Főtanúsítvány fájljának használatával ellenőrzi, hogy az IoT hub milyen identitással csatlakozik.

### <a name="mqtt-topics"></a>MQTT témakörök

A következő definíciók az eszköz által az IoT hub-ra való adatküldéshez használt MQTT-témakörök. További információ: kommunikáció az [IoT hub használatával a MQTT protokollal](../iot-hub/iot-hub-mqtt-support.md):

* A `DEVICE_CAPABILITIES_MESSAGE` meghatározza az eszköz által a által megvalósított felületek jelentésére használt témakört.
* A `DEVICETWIN_PATCH_MESSAGE` meghatározza azt a témakört, amelyet az eszköz használ a IoT hub tulajdonság-frissítéseinek jelentéséhez.
* A `DEVICE_TELEMETRY_MESSAGE` meghatározza azt a témakört, amelyet az eszköz használ a telemetria az IoT hubhoz való küldéséhez.

A MQTT kapcsolatos további információkért tekintse meg az Azure IoT GitHub-tárház [MQTT-mintáit](https://github.com/Azure-Samples/IoTMQTTSample/) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan módosíthat egy MQTT-eszköz ügyfelet a IoT Plug and Play konvenciók követéséhez. Ha többet szeretne megtudni a MQTT protokoll IoT Hub támogatásáról, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Kommunikáció az IoT hub használatával a MQTT protokollal](../iot-hub/iot-hub-mqtt-support.md)