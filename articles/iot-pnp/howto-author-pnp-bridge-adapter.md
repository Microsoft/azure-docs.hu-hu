---
title: Adapter létrehozása a IoT Plug and Play híd számára | Microsoft Docs
description: Azonosítsa a IoT Plug and Play Bridge-adapter összetevőit. Megtudhatja, hogyan terjesztheti ki a hidat a saját adapterének megírásával.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ac359e73ae71fd2163fb178caab4a2b5f908a008
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057456"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>A IoT Plug and Play-híd kiterjesztése
A [IoT Plug and Play-híd](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) lehetővé teszi az átjáróhoz csatlakoztatott meglévő eszközök csatlakoztatását az IoT hubhoz. A Bridge használatával képezhető le a csatlakoztatott eszközökre a IoT Plug and Play-adapterek. A IoT Plug and Play felület határozza meg az eszköz által küldött telemetria, az eszköz és a felhő között szinkronizált tulajdonságokat, valamint azokat a parancsokat, amelyekre az eszköz válaszol. A nyílt forráskódú híd alkalmazást Windows-vagy Linux-átjárón is telepítheti és konfigurálhatja. Emellett a híd Azure IoT Edge futtatókörnyezeti modulként is futtatható.

Ez a cikk részletesen ismerteti a következőket:

- Terjessze ki a IoT Plug and Play hidat egy adapterrel.
- Közös visszahívások implementálása egy Bridge-adapterhez.

A híd használatát bemutató egyszerű példát a következő témakörben talál: [Hogyan csatlakoztatható a IoT Plug and Play Bridge-minta, amely Linux vagy Windows rendszeren fut a IoT hub](howto-use-iot-pnp-bridge.md).

A cikk útmutatásai és mintái az [Azure digitális Twins](../digital-twins/overview.md) és a [IoT Plug and Play](overview-iot-plug-and-play.md)alapvető ismeretét feltételezik. A cikk emellett azt is feltételezi, hogy [a IoT Plug and Play-híd hogyan hozható létre és helyezhető üzembe](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Tervezési útmutató a IoT Plug and Play-híd kibővítéséhez adapterrel

A híd képességeinek kibővítéséhez saját híd-adaptereket hozhat létre.

A híd adaptereket használ a következőhöz:

- Kapcsolat létesítése egy eszköz és a felhő között.
- Az adatáramlás engedélyezése egy eszköz és a felhő között.
- Eszközkezelés engedélyezése a felhőből.

Minden híd-adapternek a következőket kell tennie:

- Hozzon létre egy digitális Twins-felületet.
- A felület használatával a felhőalapú funkciókhoz, például a telemetria, a tulajdonságokhoz és a parancsokhoz köthető az eszközökön.
- Vezérlő-és adatkommunikációt hozhat létre az eszköz hardverével vagy belső vezérlőprogram szolgáltatásával.

Az egyes hidak-adapterek egy adott típusú eszközzel működnek, attól függően, hogy az adapter hogyan kapcsolódik az eszközhöz, és hogyan kommunikál az eszközzel. Akkor is, ha az eszközzel folytatott kommunikáció kézfogási protokollt használ, a Bridge-adapterek több módon is rendelkezhetnek az eszközről származó adatok értelmezéséhez. Ebben az esetben a Bridge-adapter a konfigurációs fájlban található adapter információit használja az *interfész konfigurációjának* meghatározásához, amelyet az adapternek használnia kell az adatok elemzéséhez.

Az eszközzel folytatott kommunikációhoz a Bridge-adapter egy kommunikációs protokollt használ, amelyet a mögöttes operációs rendszer vagy az eszköz gyártója biztosít az eszköz és az API-k.

A felhővel való interakcióhoz a Bridge-adapter az Azure IoT Device C SDK által biztosított API-kat használja a telemetria küldéséhez, a digitális dupla felületek létrehozásához, a tulajdonságok küldéséhez, valamint a tulajdonságok frissítéseihez és parancsaihoz tartozó visszahívási függvények létrehozásához.

### <a name="create-a-bridge-adapter"></a>Híd-adapter létrehozása

A híd a [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) felületen definiált API-k megvalósításához egy híd adaptert vár:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Ebben az illesztőfelületben:

- `PNPBRIDGE_ADAPTER_CREATE` létrehozza az adaptert, és beállítja az illesztőfelület-felügyeleti erőforrásokat. Az adapterek globális adapter-paraméterekkel is hivatkozhatnak az adapterek létrehozásához. Ezt a függvényt egyszer kell meghívni egyetlen adapterhez.
- `PNPBRIDGE_COMPONENT_CREATE` létrehozza a digitális Twin-ügyfél felületeit, és összekapcsolja a visszahívási funkciókat. Az adapter kezdeményezi a kommunikációs csatornát az eszközre. Az adapter beállíthatja az erőforrásokat, hogy engedélyezze a telemetria folyamatot, de a telemetria csak a `PNPBRIDGE_COMPONENT_START` hívása után induljon el. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél.
- `PNPBRIDGE_COMPONENT_START` a rendszer úgy hívja, hogy a Bridge-adapter megkezdje a telemetria továbbítását az eszközről a digitális kettős ügyfél felé. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél.
- `PNPBRIDGE_COMPONENT_STOP` leállítja a telemetria folyamatot.
- `PNPBRIDGE_COMPONENT_DESTROY` megsemmisíti a digitális kettős ügyfelet és a hozzá tartozó felületi erőforrásokat. Ezt a függvényt egyszer kell meghívni a konfigurációs fájl minden egyes illesztőfelület-összetevőjénél, ha a híd le van szakítva, vagy végzetes hiba történik.
- `PNPBRIDGE_ADAPTER_DESTROY` a Bridge-adapter erőforrásainak tisztítása.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Bridge Core interakció a Bridge-adapterekkel

A következő lista ismerteti, hogy mi történik a híd indításakor:

1. A híd indításakor a Bridge adapter-kezelő a konfigurációs fájlban definiált összes illesztőfelület-összetevőn áttekinti a megfelelő adaptert, és meghívja azokat `PNPBRIDGE_ADAPTER_CREATE` . Az adapter a globális adapterek konfigurációs paramétereit használva állíthatja be az erőforrásokat a különböző *illesztőfelület-konfigurációk* támogatásához.
1. A Bridge Manager a megfelelő Bridge-adapter hívásával kezdeményezi a kezelőfelület létrehozását a konfigurációs fájlban lévő minden eszközön `PNPBRIDGE_COMPONENT_CREATE` .
1. Az adapter megkapja a csatoló összetevőhöz tartozó opcionális adapterek konfigurációs beállításait, és ezeket az információkat használja az eszközhöz való csatlakozás beállításához.
1. Az adapter létrehozza a digitális Twin-ügyfél felületeit, és összekapcsolja a visszahívási függvényeket a tulajdonságok frissítéseihez és parancsaihoz. Az eszközök kapcsolatainak létrehozása nem blokkolja a visszahívások visszaküldését, miután a digitális Twin Interface létrehozása sikeresen megtörtént. Az aktív eszköz kapcsolata független a híd által létrehozott aktív felületi ügyféllel. Ha egy kapcsolat meghiúsul, az adapter feltételezi, hogy az eszköz inaktív. A híd adapter dönthet úgy, hogy újrapróbálkozik ezzel a kapcsolattal.
1. Miután a Bridge adapter-kezelő a konfigurációs fájlban megadott összes illesztőfelület-összetevőt létrehozta, regisztrálja az összes felületet az Azure IoT Hub. A regisztráció blokkoló, aszinkron hívás. A hívás befejezésekor elindítja a visszahívást a Bridge-adapteren, amely elkezdheti a felhőből a tulajdonságok és a parancsok visszahívásának kezelését.
1. A Bridge adapter-kezelő ezután meghívja az `PNPBRIDGE_INTERFACE_START` egyes összetevőket, és a Bridge-adapter elindítja a digitális Twin-ügyfélnek a telemetria jelentését.

### <a name="design-guidelines"></a>Tervezési irányelvek

Új Bridge-adapter fejlesztésekor kövesse az alábbi irányelveket:

- Határozza meg, hogy mely eszközök támogatottak, és hogy az adaptert használó összetevők felületének definíciója hogyan néz ki.
- Határozza meg, hogy az adapternek milyen illesztőfelület-és globális paramétereket kell meghatároznia a konfigurációs fájlban.
- Azonosítsa az összetevő tulajdonságainak és parancsainak támogatásához szükséges alacsony szintű eszköz-kommunikációt.
- Határozza meg, hogy az adapternek hogyan kell elemezni a nyers adatait az eszközről, és át kell alakítania azokat a telemetria, amelyeket a IoT Plug and Play Interface definition határoz meg.
- Implementálja a korábban ismertetett híd-adapter felületet.
- Adja hozzá az új adaptert az adapter jegyzékfájlhoz, és hozza létre a hidat.

### <a name="enable-a-new-bridge-adapter"></a>Új híd adapter engedélyezése

Az adapterek a hídon való engedélyezéséhez adjon hozzá egy hivatkozást a [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> A Bridge-adapter visszahívásait egymás után hívja meg a rendszer. Az adapternek nem szabad letiltani a visszahívást, mert ez megakadályozza, hogy a híd magja előrehaladást érjen el.

### <a name="sample-camera-adapter"></a>Minta kamera adapter

A [kamera-adapter információi](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) az Ön által engedélyezhető minta kamera-adaptert ismertetik.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Példák a gyakori adapter-forgatókönyvekre/visszahívásokra

A következő szakasz részletesen ismerteti, hogy a híd adaptere hogyan valósítja meg a visszahívásokat számos gyakori forgatókönyvhöz és használathoz, ez a szakasz a következő visszahívásokat tartalmazza:
- [Fogadási tulajdonságok frissítése (felhőből eszközre)](#receive-property-update-cloud-to-device)
- [Tulajdonság frissítésének jelentése (eszközről a felhőbe)](#report-a-property-update-device-to-cloud)
- [Telemetria küldése (eszközről a felhőbe)](#send-telemetry-device-to-cloud)
- [Fogadja a parancs frissítésének visszahívását a felhőből, és dolgozza fel az eszköz oldalán (felhőből eszközre)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Válasz a parancs frissítésére az eszköz oldalán (eszközről a felhőbe)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Az alábbi példák a [környezeti érzékelő minta adapterén](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)alapulnak.

### <a name="receive-property-update-cloud-to-device"></a>Fogadási tulajdonságok frissítése (felhőből eszközre)
Az első lépés egy visszahívási függvény regisztrálása:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
A következő lépés a visszahívási függvény megvalósítása az eszközön található tulajdonság frissítésének olvasásához:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Tulajdonság frissítésének jelentése (eszközről a felhőbe)
Az eszköz az összetevő létrehozása után bármikor jelentést tud készíteni a felhőhöz a következő állapottal: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Telemetria küldése (eszközről a felhőbe)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Fogadja a parancs frissítésének visszahívását a felhőből, és dolgozza fel az eszköz oldalán (felhőből eszközre)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Válasz a parancs frissítésére az eszköz oldalán (eszközről a felhőbe)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a IoT Plug and Play-hídra, látogasson el a [IoT Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-tárházra.
