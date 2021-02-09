---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 398f11c6e55cb62e0b29c148494cf3d590200b9d
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834177"
---
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Plug and Play-eszköz-alkalmazást összetevőkkel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás C nyelven íródott, amely a C Azure IoT Device SDK-ban szerepel. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A mintakód letöltése.
> * Hozzon létre egy mintakód-kódot.
> * Futtassa a minta-eszköz alkalmazást, és ellenőrizze, hogy csatlakozik-e az IoT hubhoz.
> * Tekintse át a forráskódot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Ezt az oktatóanyagot Linux vagy Windows rendszeren is elvégezheti. Az oktatóanyagban található rendszerhéj-parancsok követik a "" Path elválasztók Linux-egyezményét `/` , ha a Windows rendszerű számítógépeken is ezt követően szeretné felcserélni ezeket a elválasztókat a következőre: " `\` ".

Az előfeltételek eltérnek az operációs rendszertől:

### <a name="linux"></a>Linux

Ez az oktatóanyag feltételezi, hogy Ubuntu Linux használ. Az oktatóanyag lépései az Ubuntu 18,04 használatával lettek tesztelve.

Az oktatóanyag Linuxon való elvégzéséhez telepítse a következő szoftvereket a helyi linuxos környezetbe:

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

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a Visual Studio [telepítésekor](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) a **C++** számítási feladatait is tartalmazza.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).

## <a name="download-the-code"></a>A kód letöltése

Ha elvégezte a gyors üzembe helyezést [: csatlakoztasson egy minta IoT Plug and Play Linux vagy Windows rendszerű eszközön futó alkalmazást, hogy IoT hub (C)](../articles/iot-pnp/quickstart-connect-device.md) már letöltötte a kódot.

Ebben az oktatóanyagban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C SDK-t.

Nyisson meg egy parancssort egy tetszőleges mappában. Futtassa az alábbi parancsot az [Azure IoT C SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-c) tárházának klónozásához a következő helyre:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

A kódot a Visual Studióval vagy a parancssorból is létrehozhatja és futtathatja `cmake` .

### <a name="use-visual-studio"></a>A Visual Studio használata

1. Nyissa meg a klónozott tárház gyökérkönyvtárát. Néhány másodperc elteltével a **CMAK** -támogatás a Visual Studióban csak a projekt futtatásához és hibakereséséhez szükséges.
1. Ha a Visual Studio elkészült, a **Megoldáskezelőban** navigáljon a minta *iothub_client/Samples/PnP/pnp_temperature_controller/*.
1. Kattintson a jobb gombbal a *pnp_temperature_controller. c* fájlra, majd válassza a **hibakeresési konfiguráció hozzáadása** elemet. Válassza az **alapértelmezett** lehetőséget.
1. A Visual Studio megnyitja a *launch.vs.js* fájlt. Szerkessze a fájlt a következő kódrészletben látható módon a szükséges környezeti változók megadásához. A hatókör-azonosító és a beléptetési elsődleges kulcs tudomásul jutott, amikor befejezte a [környezet beállítását a IoT Plug and Play rövid útmutatók és oktatóanyagok](../articles/iot-pnp/set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Kattintson a jobb gombbal a *pnp_temperature_controller. c* fájlra, és válassza a **beállítás indítási elemként** lehetőséget.
1. A kód végrehajtásának nyomon követéséhez a Visual Studióban adjon hozzá egy töréspontot a `main` függvényhez a *pnp_temperature_controller. c* fájlban.
1. Most már futtathatja és hibakereséssel is elvégezheti a mintát a **hibakeresési** menüből.

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

### <a name="use-cmake-at-the-command-line"></a>Használat `cmake` a parancssorban

A minta összeállítása:

1. Hozzon létre egy _CMAK_ almappát a KLÓNOZOTT eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Futtassa az alábbi parancsokat az SDK-hoz és a mintákhoz tartozó projektfájlok létrehozásához és létrehozásához:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)információit.

A minta futtatása:

1. A _CMAK_ mappában navigáljon a végrehajtható fájlt tartalmazó mappához, és futtassa a következőt:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. Ez a példa [több összetevővel](../articles/iot-pnp/concepts-components.md)rendelkező modellt valósít meg. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

### <a name="iot-plug-and-play-helper-functions"></a>IoT Plug and Play segítő függvények

Ebben a példában a kód néhány segítő függvényt használ a */gyakori hibák* mappából:

*pnp_device_client_ll* tartalmazza a IoT Plug and Play csatlakozási metódusát, amely a `model-id` paraméterrel együtt szerepel: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol*: a IoT Plug and Play segítő függvényeket tartalmazza:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Ezek a segítő függvények elég általánosak a saját projektben való használathoz. Ez a minta azokat a három fájlban használja, amelyek megfelelnek a modell egyes összetevőinek:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Például a *pnp_deviceinfo_component* fájlban a `SendReportedPropertyForDeviceInformation` függvény két segítő függvényt használ:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

A minta minden összetevője ezt a mintát követi.

### <a name="code-flow"></a>Kód folyamatábrája

A `main` függvény inicializálja a kapcsolatokat, és elküldi a modell azonosítóját:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

A kód a `PnP_CreateDeviceClientLLHandle` IoT hub-hoz való kapcsolódásra, `modelId` beállításra van beállítva, és a közvetlen metódusok és az eszközök kettős frissítései számára beállítja az eszköz módszereit és az eszközök kettős visszahívási kezelőit:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` a a kapcsolatok adatait is tartalmazza. A környezeti változó **IOTHUB_DEVICE_SECURITY_TYPE** meghatározza, hogy a minta kapcsolati sztringet vagy az eszköz kiépítési szolgáltatását használja-e az IoT hubhoz való kapcsolódáshoz.

Amikor az eszköz a modell AZONOSÍTÓját küldi, IoT válik Plug and Play eszköznek.

Ha a visszahívási kezelők vannak érvényben, az eszköz a kettős frissítésekre és a közvetlen metódusokra hívja a következő műveleteket:

* Az eszköz kettős visszahívása esetén a `PnP_TempControlComponent_DeviceTwinCallback` meghívja a `PnP_ProcessTwinData` függvényt az adat feldolgozására. `PnP_ProcessTwinData` a *látogatói minta* használatával elemezheti a JSON-t, majd meglátogathatja az egyes tulajdonságokat, és meghívja az `PnP_TempControlComponent_ApplicationPropertyCallback` egyes elemeket.

* A visszahívási parancsok esetében a `PnP_TempControlComponent_DeviceMethodCallback` függvény a segítő függvényt használja a parancs és az összetevők nevének elemzéséhez:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    A `PnP_TempControlComponent_DeviceMethodCallback` függvény ezután meghívja a parancsot az összetevőn:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

A `main` függvény inicializálja az IoT hub számára továbbított írásvédett tulajdonságokat:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

A `main` függvény egy hurkot ad az esemény-és telemetria-adat frissítéséhez az egyes összetevőknél:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

A `PnP_ThermostatComponent_SendTelemetry` függvény a struct használatát mutatja be `PNP_THERMOSTAT_COMPONENT` . A minta ezt a struktúrát használja a hőmérséklet-vezérlő két termosztátjának adatainak tárolására. A kód a `PnP_CreateTelemetryMessageHandle` függvényt használja az üzenet előkészítéséhez és elküldéséhez:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

A `main` függvény végül elpusztítja a különböző összetevőket, és bezárja a központhoz való kapcsolódást.
