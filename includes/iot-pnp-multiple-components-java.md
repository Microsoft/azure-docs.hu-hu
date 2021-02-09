---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: bece0f95f3cd87bcf803637835ef1854606b088b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834154"
---
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre több összetevőből álló IoT Plug and Play eszköz-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure CLI használatával megtekintheti az általa küldött telemetria. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure CLI használatával tudja értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Plug and Play-eszköz-alkalmazást összetevőkkel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás Java nyelven íródott, és megtalálható a Javához készült Azure IoT Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A mintakód letöltése.
> * Hozzon létre egy mintakód-kódot.
> * Futtassa a minta-eszköz alkalmazást, és ellenőrizze, hogy csatlakozik-e az IoT hubhoz.
> * Tekintse át a forráskódot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* Java SE Development Kit 8. A [Java hosszú távú Azure-és Azure stack-támogatásában](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)a **hosszú távú támogatás** alatt válassza a **Java 8** lehetőséget.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>A kód letöltése

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), már klónozotta a tárházat.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT Java SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-java) tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

A művelet elvégzése több percet is igénybe vehet.

## <a name="build-the-code"></a>A kód létrehozása

Windows rendszeren navigáljon a klónozott Java SDK-tárház gyökérkönyvtárához. A függőségek létrehozásához futtassa a következő parancsot:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta alkalmazás futtatásához navigáljon a *\device\iot-Device-samples\pnp-Device-sample\temperature-Controller-Device-Sample* mappára, és futtassa a következő parancsot:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](../articles/iot-pnp/concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard `DeviceClient` osztály használatával csatlakozik az IoT hubhoz. Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

A modell AZONOSÍTÓját a kód a következő kódrészletben látható módon tárolja:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Miután az eszköz csatlakozik az IoT hubhoz, a kód regisztrálja a parancs-kezelőket.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

A kívánt tulajdonság-frissítésekhez külön kezelők tartoznak a két termosztát-összetevőn:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

A mintakód a telemetria az egyes termosztát-összetevőkből küldi el:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

A `sendTemperatureReading` metódus a `PnpHhelper` osztály használatával hoz létre üzeneteket az egyes összetevőkhöz:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

Az `PnpHelper` osztály más mintavételi módszereket is tartalmaz, amelyek több összetevő-modellel is használhatók.

Az Azure IoT Explorer eszköz használatával megtekintheti a két termosztát-összetevő telemetria és tulajdonságait:

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Több összetevős eszköz az Azure IoT Explorerben":::

Az Azure IoT Explorer eszköz használatával a parancsok a két termosztát-összetevő vagy az alapértelmezett összetevő egyikében is hívhatók.
