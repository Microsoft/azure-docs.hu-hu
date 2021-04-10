---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: d215da660929c66514f9d0cf5f3161c83a1c330c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582777"
---
## <a name="model-id-announcement"></a>Modell AZONOSÍTÓjának bejelentése

A modell AZONOSÍTÓjának bejelentéséhez az eszköznek tartalmaznia kell azt a kapcsolódási adatok között:

```c
static const char g_ThermostatModelId[] = "dtmi:com:example:Thermostat;1";
IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceHandle = NULL;
deviceHandle = CreateDeviceClientLLHandle();
iothubResult = IoTHubDeviceClient_LL_SetOption(
    deviceHandle, OPTION_MODEL_ID, g_ThermostatModelId);
```

> [!TIP]
> Modulok és IoT Edge esetén használja a következőt `IoTHubModuleClient_LL` : `IoTHubDeviceClient_LL` .

> [!TIP]
> Ez az egyetlen, amikor az eszköz beállíthatja a modell AZONOSÍTÓját, az eszköz csatlakoztatása után nem frissíthető.

## <a name="dps-payload"></a>DPS-adattartalom

A [Device kiépítési szolgáltatást (DPS)](../articles/iot-dps/about-iot-dps.md) használó eszközök tartalmazhatják a `modelId` kiépítési folyamat során a következő JSON-adattartalommal használható eszközöket.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetria, tulajdonságok és parancsok implementálása

A [IoT Plug and Play-modellek összetevőinek ismertetése](../articles/iot-pnp/concepts-modeling-guide.md)című témakörben leírtak szerint az eszköz-építőknek el kell dönteniük, hogy milyen összetevőket szeretnének használni az eszközeik leírásához. Az összetevők használatakor az eszközöknek az ebben a részben ismertetett szabályokat kell követniük.

### <a name="telemetry"></a>Telemetria

Az alapértelmezett összetevő nem igényel speciális tulajdonságot.

Beágyazott összetevők használatakor az eszközöknek egy üzenet tulajdonságot kell megadniuk az összetevő nevével:

```c
void PnP_ThermostatComponent_SendTelemetry(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    IOTHUB_CLIENT_RESULT iothubResult;

    char temperatureStringBuffer[32];

    if (snprintf(
        temperatureStringBuffer,
        sizeof(temperatureStringBuffer),
        g_temperatureTelemetryBodyFormat,
        pnpThermostatComponent->currentTemperature) < 0)
    {
        LogError("snprintf of current temperature telemetry failed");
    }
    else if ((messageHandle = PnP_CreateTelemetryMessageHandle(
        pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
    {
        LogError("Unable to create telemetry message");
    }
    else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(
        deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send telemetry message, error=%d", iothubResult);
    }

    IoTHubMessage_Destroy(messageHandle);
}

// ...

PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
```

### <a name="read-only-properties"></a>Csak olvasható tulajdonságok

Az alapértelmezett összetevőből származó tulajdonságok jelentése nem igényel különleges szerkezetet:

```c
static const char g_maxTemperatureSinceRebootFormat[] = "{\"maxTempSinceLastReboot\":%.2f}";

char maxTemperatureSinceRebootProperty[256];

snprintf(
    maxTemperatureSinceRebootProperty,
    sizeof(maxTemperatureSinceRebootProperty),
    g_maxTemperatureSinceRebootFormat,
    38.7);

IOTHUB_CLIENT_RESULT iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)maxTemperatureSinceRebootProperty,
    strlen(maxTemperatureSinceRebootProperty), NULL, NULL));
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Beágyazott összetevők használatakor a tulajdonságokat az összetevő nevén belül kell létrehozni:

```c
STRING_HANDLE PnP_CreateReportedProperty(
    const char* componentName,
    const char* propertyName,
    const char* propertyValue
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":%s}",
            propertyName, propertyValue);
    }
    else 
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":%s}}",
            componentName, propertyName, propertyValue);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent =
        (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    char maximumTemperatureAsString[32];
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;

    if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString),
        "%.2f", pnpThermostatComponent->maxTemperature) < 0)
    {
        LogError("Unable to create max temp since last reboot string for reporting result");
    }
    else if ((jsonToSend = PnP_CreateReportedProperty(
                pnpThermostatComponent->componentName,
                g_maxTempSinceLastRebootPropertyName,
                maximumTemperatureAsString)) == NULL)
    {
        LogError("Unable to build max temp since last reboot property");
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
                deviceClientLL,
                (const unsigned char*)jsonToSendStr,
                jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
        {
            LogError("Unable to send reported state, error=%d", iothubClientResult);
        }
        else
        {
            LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s",
                pnpThermostatComponent->componentName);
        }
    }

    STRING_delete(jsonToSend);
}

// ...

PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Írható tulajdonságok

Ezeket a tulajdonságokat beállíthatja az eszköz, vagy frissítheti a megoldás. Ha a megoldás frissíti a tulajdonságot, az ügyfél visszahívási értesítést kap a vagy a alkalmazásban `DeviceClient` `ModuleClient` . A IoT Plug and Play konvenciók követéséhez az eszköznek tájékoztatnia kell a szolgáltatást arról, hogy a tulajdonság sikeresen megérkezett.

#### <a name="report-a-writable-property"></a>Írható tulajdonság jelentése

Ha egy eszköz írható tulajdonságot jelent, tartalmaznia kell az `ack` egyezményekben definiált értékeket.

Írható tulajdonság bejelentése az alapértelmezett összetevőből:

```c
IOTHUB_CLIENT_RESULT iothubClientResult;
char targetTemperatureResponseProperty[256];

snprintf(
    targetTemperatureResponseProperty,
    sizeof(targetTemperatureResponseProperty),
    "{\"targetTemperature\":{\"value\":%.2f,\"ac\":%d,\"av\":%d,\"ad\":\"%s\"}}",
    23.2, 200, 3, "Successfully updated target temperature");

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)targetTemperatureResponseProperty,
    strlen(targetTemperatureResponseProperty), NULL, NULL);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Egy beágyazott összetevőből származó írható tulajdonság jelentéséhez a Twin elemnek tartalmaznia kell egy jelölőt:

```c
STRING_HANDLE PnP_CreateReportedPropertyWithStatus(const char* componentName,
    const char* propertyName, const char* propertyValue,
    int result, const char* description, int ackVersion
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}",
            propertyName, propertyValue,
            result, description, ackVersion);
    }
    else
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}}",
            componentName, propertyName, propertyValue,
            result, description, ackVersion);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

// ...

char targetTemperatureAsString[32];
IOTHUB_CLIENT_RESULT iothubClientResult;
STRING_HANDLE jsonToSend = NULL;

snprintf(targetTemperatureAsString,
    sizeof(targetTemperatureAsString),
    "%.2f",
    23.2);
jsonToSend = PnP_CreateReportedPropertyWithStatus(
    "thermostat1",
    "targetTemperature",
    targetTemperatureAsString,
    200,
    "complete",
    3);

const char* jsonToSendStr = STRING_c_str(jsonToSend);
size_t jsonToSendStrLen = strlen(jsonToSendStr);

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)jsonToSendStr,
    jsonToSendStrLen, NULL, NULL);

STRING_delete(jsonToSend);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Előfizetés a kívánt tulajdonság frissítéseire

A szolgáltatások frissíthetik a csatlakoztatott eszközökön értesítést kiváltó kívánt tulajdonságokat. Ez az értesítés tartalmazza a frissített kívánt tulajdonságokat, beleértve a frissítést azonosító verziószámot is. Az eszközöknek a jelentett tulajdonságokkal megegyező üzenettel kell válaszolniuk `ack` .

Az alapértelmezett összetevő látja az egyetlen tulajdonságot, és létrehozza a jelentett `ack` verziót a kapott verzióval:

```c
static void Thermostat_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback)
{
    // The device handle associated with this request is passed as the context,
    // since we will need to send reported events back.
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL =
        (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    JSON_Value* versionValue = NULL;
    JSON_Value* targetTemperatureValue = NULL;

    jsonStr = CopyTwinPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    targetTemperatureValue = json_object_get_value(desiredObject, "targetTemperature"));
    versionValue = json_object_get_value(desiredObject, "$version"));
    json_value_get_type(versionValue);
    json_value_get_type(targetTemperatureValue);

    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    // ...

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, 200, version, "Successfully updated target temperature");

    json_value_free(rootValue);
    free(jsonStr);
}

// ...

IOTHUB_CLIENT_RESULT iothubResult;
iothubResult = IoTHubDeviceClient_LL_SetDeviceTwinCallback(
    deviceHandle, Thermostat_DeviceTwinCallback, (void*)deviceHandle))
```

Az eszköz Twin a kívánt és jelentett szakaszban található tulajdonságot jeleníti meg:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

A beágyazott összetevők megkapják az összetevő nevével burkolt kívánt tulajdonságokat, és jelenteniük kell a `ack` jelentett tulajdonságot:

```c
bool PnP_ProcessTwinData(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size, const char** componentsInModel,
    size_t numComponentsInModel,
    PnP_PropertyCallbackFunction pnpPropertyCallback,
    void* userContextCallback)
{
    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    bool result;

    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    
    result = VisitDesiredObject(
        desiredObject, componentsInModel,
        numComponentsInModel, pnpPropertyCallback,
        userContextCallback);


    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...
static const char g_thermostatComponent1Name[] = "thermostat1";
static const size_t g_thermostatComponent1Size = sizeof(g_thermostatComponent1Name) - 1;
static const char g_thermostatComponent2Name[] = "thermostat2";

static const char* g_modeledComponents[] = {g_thermostatComponent1Name, g_thermostatComponent2Name};
static const size_t g_numModeledComponents = sizeof(g_modeledComponents) / sizeof(g_modeledComponents[0]);

static void PnP_TempControlComponent_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback
)
{
    PnP_ProcessTwinData(
        updateState, payload,
        size, g_modeledComponents,
        g_numModeledComponents,
        PnP_TempControlComponent_ApplicationPropertyCallback,
        userContextCallback);
}
```

A beágyazott összetevőhöz tartozó Twin eszköz a kívánt és jelentett szakaszt a következőképpen mutatja be:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Parancsok

Az alapértelmezett összetevő megkapja a parancs nevét, ahogy azt a szolgáltatás meghívja.

A beágyazott összetevők az összetevő nevével és az elválasztóval ellátott parancs nevét kapják meg `*` .

```c
void PnP_ParseCommandName(
    const char* deviceMethodName,
    unsigned const char** componentName,
    size_t* componentNameSize,
    const char** pnpCommandName
)
{
    const char* separator;

    if ((separator = strchr(deviceMethodName, "*")) != NULL)
    {
        *componentName = (unsigned const char*)deviceMethodName;
        *componentNameSize = separator - deviceMethodName;
        *pnpCommandName = separator + 1;
    }
    else
    {
        *componentName = NULL;
        *componentNameSize = 0;
        *pnpCommandName = deviceMethodName;
    }
}

static int PnP_TempControlComponent_DeviceMethodCallback(
    const char* methodName,
    const unsigned char* payload,
    size_t size,
    unsigned char** response,
    size_t* responseSize,
    void* userContextCallback)
{
    (void)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    int result;
    unsigned const char *componentName;
    size_t componentNameSize;
    const char *pnpCommandName;

    *response = NULL;
    *responseSize = 0;

    // Parse the methodName into its componentName and CommandName.
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);

    // Parse the JSON of the payload request.
    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    if (componentName != NULL)
    {
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
    }
    else
    {
        LogInfo("Received PnP command for TemperatureController component, command=%s", pnpCommandName);
        if (strcmp(pnpCommandName, g_rebootCommand) == 0)
        {
            result = PnP_TempControlComponent_InvokeRebootCommand(rootValue);
        }
        else
        {
            LogError("PnP command=s%s is not supported by TemperatureController", pnpCommandName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }

    if (*response == NULL)
    {
        SetEmptyCommandResponse(response, responseSize, &result);
    }

    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...

PNP_DEVICE_CONFIGURATION g_pnpDeviceConfiguration;
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

#### <a name="request-and-response-payloads"></a>Kérelmek és válaszok hasznos adatai

A parancsok a kérelem és a válasz hasznos adatai meghatározására használják a típusokat. Az eszköznek deszerializálnia kell a bejövő bemeneti paramétert, és szerializálnia kell a választ. Az alábbi példa bemutatja, hogyan implementálhat egy olyan parancsot, amely a hasznos adatokban definiált komplex típusokat tartalmazza:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

A következő kódrészletek azt mutatják be, hogyan valósítja meg egy eszköz a parancs definícióját, beleértve a szerializálás és a deszerializálás engedélyezéséhez használt típusokat is:

```c
static const char g_maxMinCommandResponseFormat[] = "{\"maxTemp\":%.2f,\"minTemp\":%.2f,\"avgTemp\":%.2f,\"startTime\":\"%s\",\"endTime\":\"%s\"}";

// ...

static bool BuildMaxMinCommandResponse(
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent,
    unsigned char** response,
    size_t* responseSize)
{
    int responseBuilderSize = 0;
    unsigned char* responseBuilder = NULL;
    bool result;
    char currentTime[TIME_BUFFER_SIZE];

    BuildUtcTimeFromCurrentTime(currentTime, sizeof(currentTime));
    responseBuilderSize = snprintf(NULL, 0, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures /
        pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime, currentTime));

    responseBuilder = calloc(1, responseBuilderSize + 1));

    responseBuilderSize = snprintf(
        (char*)responseBuilder, responseBuilderSize + 1, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures / pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime,
        currentTime));

    *response = responseBuilder;
    *responseSize = (size_t)responseBuilderSize;

    return true;
}
```

> [!Tip]
> A kérelem és a válasz neve nem szerepel a dróton továbbított szerializált adattartalomban.
