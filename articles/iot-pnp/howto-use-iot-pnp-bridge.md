---
title: Linux vagy Windows rendszeren futó IoT Plug and Play híd-minta összekapcsolása egy IoT hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play hidat Linuxon vagy Windowson, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834102"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux vagy Windows rendszeren futó IoT Plug and Play híd-minta összekapcsolása IoT Hub

Ez a cikk bemutatja, hogyan hozhatja létre a IoT Plug and Play híd minta környezeti adapterét, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A IoT Plug and Play-híd C nyelven íródott, és tartalmazza a C Azure IoT Device SDK-t. Az oktatóanyag végére képesnek kell lennie a IoT Plug and Play-híd futtatására és a jelentés telemetria megtekintésére az Azure IoT Explorerben:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Képernyőkép az Azure IoT Explorerről, amely egy jelentett telemetria (páratartalom, hőmérséklet) ábrázolja a IOT Plug and Play Bridge-ből.":::

## <a name="prerequisites"></a>Előfeltételek

A mintát futtathatja Windows vagy Linux rendszerű cikkben is. A útmutatóban szereplő rendszerhéj-parancsok követik az elérésiút-elválasztók Windows-konvencióját () `\` , ha a Linuxon a mellett a következőt használja: " `/` ".

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Ha a jelen cikk második részében a minta eszközzel szeretne kommunikálni, használja az **Azure IoT Explorer** eszközt. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a karakterláncot, amelyet a cikk későbbi részében használhat:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a karakterláncot, amelyet a cikk későbbi részében használhat:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>A híd letöltése és futtatása

Ebben a cikkben két lehetőség közül választhat a híd futtatásához. A következőket teheti:

- Töltse le az előre elkészített végrehajtható fájlt, és futtassa az ebben a részben leírtak szerint.
- Töltse le a forráskódot, majd hozza [létre és futtassa a hidat](#build-and-run-the-bridge) a következő szakaszban leírtak szerint.

A híd letöltése és futtatása:

1. Lépjen a IoT Plug and Play [releases oldalára](https://github.com/Azure/iot-plug-and-play-bridge/releases).
1. Töltse le az operációs rendszerének előre elkészített végrehajtható fájlját: **pnpbridge_bin.exe** for Windows vagy **pnpbridge_bin** for Linux.
1. Töltse le a környezeti érzékelő mintájának konfigurációs fájljában [config.js](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) mintát. Győződjön meg arról, hogy a konfigurációs fájl a végrehajtható fájllal megegyező mappában található.
1. Szerkessze a *config.js* fájlt:

    - Adja hozzá a `connection-string` korábban megjegyzett _eszköz-csatlakoztatási karakterlánc_ értékét.
    - Adja hozzá a `symmetric_key` megosztott elérési kulcs értékét az _eszköz kapcsolati karakterláncában_.
    - Cserélje le az `root_interface_model_id` értéket a értékre `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    A fájl *config.jsának* első szakasza a következő kódrészlethez hasonlít:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Futtassa a végrehajtható fájlt a parancssori környezetben. A híd a következőhöz hasonló kimenetet hoz létre:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>A híd létrehozása és futtatása

Ha saját maga szeretné felépíteni a végrehajtható fájlt, letöltheti a forráskódot, és létrehozhat parancsfájlokat.

Nyisson meg egy parancssort egy tetszőleges mappában. Futtassa az alábbi parancsot a [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Az adattár klónozása után frissítse az almodulokat. Az almodulok tartalmazzák a C Azure IoT SDK-t:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

A művelet elvégzése több percet is igénybe vehet.

> [!TIP]
> Ha a git Clone sub modul frissítésével kapcsolatos problémákba ütközik, ez a Windows-fájlelérési utakon ismert probléma. A probléma megoldásához próbálja meg a következő parancsot: `git config --system core.longpaths true`

A híd felépítési előfeltételei az operációs rendszertől eltérőek:

### <a name="windows"></a>Windows

A IoT Plug and Play Bridge Windows rendszeren való létrehozásához telepítse a következő szoftvereket:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) a **C++** számítási feladatait is tartalmazza.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

### <a name="linux"></a>Linux

Ez a cikk a Ubuntu Linux használatát feltételezi. A cikkben ismertetett lépéseket Ubuntu 18,04 használatával tesztelték.

A IoT Plug and Play Bridge Linux rendszeren való létrehozásához telepítse a **GCC**, a **git**, a **CMAK** és az összes szükséges függőséget a következő `apt-get` paranccsal:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ellenőrizze, hogy a verziója `cmake` meghaladja-e a **2.8.12** , és hogy a **GCC** verziója meghaladja-e a **4.4.7**-t.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>A IoT Plug and Play-híd létrehozása

Navigáljon a *pnpbridge* mappára az adattár könyvtárában.

A Windows a következőt futtatja a [Visual studióhoz készült fejlesztői parancssorban](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

A Linuxhoz hasonlóan a következőt is futtathatja:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows rendszeren megnyithatja a CMAK parancs által létrehozott megoldást a Visual Studio 2019-ben. Nyissa meg a *azure_iot_pnp_bridge. SLN* projektfájlt a CMAK könyvtárban, és állítsa be a *pnpbridge_bin* projektet indítási projektként a megoldásban. Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

### <a name="edit-the-configuration-file"></a>A konfigurációs fájl szerkesztése

A konfigurációs fájlokról a [IoT Plug and Play Bridge-fogalmakat ismertető dokumentumban](concepts-iot-pnp-bridge.md)olvashat bővebben.

Nyissa meg a fájl *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.jsét* egy szövegszerkesztőben.

- Adja hozzá a `connection-string` korábban megjegyzett _eszköz-csatlakoztatási karakterlánc_ értékét.
- Adja hozzá a `symmetric_key` megosztott elérési kulcs értékét az _eszköz kapcsolati karakterláncában_.
- Cserélje le az `root_interface_model_id` értéket a értékre `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

A fájl *config.jsának* első szakasza a következő kódrészlethez hasonlít:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>A IoT Plug and Play-híd futtatása

Indítsa el a IoT Plug and Play Bridge környezeti érzékelő mintáját. A paraméter az `config.json` előző szakaszban szerkesztett fájl elérési útja:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

A híd a következőhöz hasonló kimenetet hoz létre:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Futtassa a következő parancsokat a híd Linuxon való futtatásához:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>A modell fájljainak letöltése

A későbbiekben az Azure IoT Explorer használatával megtekintheti az eszközt, amikor az IoT hubhoz csatlakozik. Az Azure IoT Explorernek szüksége van az eszköz által küldött **modell-azonosítóval** megegyező nevű modell helyi példányára. A Model fájl lehetővé teszi, hogy a IoT Explorer megjelenjen az eszköz által megvalósított telemetria, tulajdonságok és parancsok.

Az Azure IoT Explorer modelljeinek letöltése:

1. Hozzon létre egy *modell* nevű mappát a helyi gépen.
1. Mentse [EnvironmentalSensor.jst](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) az előző lépésben létrehozott *modellek* mappájába.
1. Ha egy szövegszerkesztőben nyitja meg ezt a modellt, láthatja, hogy a modell egy összetevőt definiál `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` azonosítóként. Ez ugyanaz a modell-azonosító, amelyet a fájl *config.js* is használt.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

A híd elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e. Láthatja a modellben definiált telemetria, tulajdonságokat és parancsokat `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

* [Mi a IoT Plug and Play híd?](./concepts-iot-pnp-bridge.md)
* [IoT-Plug and Play-híd létrehozása, üzembe helyezése és kiterjesztése](howto-build-deploy-extend-pnp-bridge.md)
* [IoT Plug and Play híd a GitHubon](https://github.com/Azure/iot-plug-and-play-bridge)
