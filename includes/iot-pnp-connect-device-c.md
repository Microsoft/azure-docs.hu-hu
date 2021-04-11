---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f626d6058374d52b5a1fd07bfdb229cfb715ca45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612890"
---
Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás C nyelven íródott, amely a C Azure IoT Device SDK-ban szerepel. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Ezt a rövid útmutatót Linux vagy Windows rendszeren is futtathatja. Az ebben a rövid útmutatóban található rendszerhéj-parancsok követik a (z) "" elérésiút-elválasztók Linux-konvencióját `/` , ha a Windows rendszerű számítógépeken a következőt használja: " `\` ".

Az előfeltételek eltérnek az operációs rendszertől:

### <a name="linux"></a>Linux

Ez a rövid útmutató feltételezi, hogy a Ubuntu Linux használja. A rövid útmutató lépései az Ubuntu 18,04 használatával lettek tesztelve.

A gyors útmutató Linuxon való elvégzéséhez telepítse a következő szoftvereket a helyi linuxos környezetbe:

Telepítse a **GCC**, a **git**, a **CMAK** és az összes szükséges függőséget a `apt-get` parancs használatával:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ellenőrizze, hogy a verziója `cmake` meghaladja-e a **2.8.12** , és hogy a **GCC** verziója meghaladja-e a **4.4.7**-t.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

A rövid útmutató Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) a **C++** számítási feladatait is tartalmazza.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

## <a name="download-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT C SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-c) tárházának klónozásához a következő helyre:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

A művelet elvégzése több percet is igénybe vehet.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK használatával hozza létre a mellékelt mintakód:

1. Hozzon létre egy _CMAK_ -alkönyvtárat az eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Az SDK és a minták létrehozásához futtassa a következő parancsokat:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> Windows rendszeren megnyithatja a parancs által generált megoldást a `cmake` Visual Studio 2019-ben. Nyissa meg a *azure_iot_sdks. SLN* projektfájlt a _CMAK_ könyvtárban, és állítsa be a **pnp_simple_thermostat** projektet indítási projektként a megoldásban. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)információit.

A minta alkalmazásnak az SDK-ban való futtatásához, amely szimulál egy IoT Plug and Play eszköz telemetria küldését az IoT hubhoz:

A _CMAK_ mappában navigáljon a végrehajtható fájlt tartalmazó mappához, és futtassa a következőt:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> A Visual Studióban a Windowson a kód végrehajtásának nyomon követéséhez adjon hozzá egy töréspontot a `main` függvényhez a _pnp_simple_thermostat. c_ fájlban.

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy egyszerű IoT Plug and Play termosztátos eszközt implementál. A minta által megvalósított modell nem használ IoT Plug and Play [összetevőket](../articles/iot-pnp/concepts-modeling-guide.md). A [termosztát eszköz DTDL-modell fájlja](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard függvény használatával csatlakozik az IoT hubhoz:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

A tulajdonságokat frissítő, a parancsokat kezelő és a telemetria küldő kód megegyezik egy olyan eszköz kódjával, amely nem használja a IoT Plug and Play konvencióit.

A kód a plébános-függvénytárat használja az IoT hub-ból eljuttatott hasznos adatokban található JSON-objektumok elemzéséhez:

```c
// JSON parser
#include "parson.h"
```
