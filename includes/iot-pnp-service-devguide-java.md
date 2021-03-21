---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 33752c1ebb83c5d63e8e1cb396c52f01f07046cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245087"
---
A következő erőforrások is elérhetők:

- [A Java SDK referenciadokumentációja](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice)
- [Szolgáltatás-ügyfél minták](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [Digitális Twins-minták](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>IoT Hub szolgáltatási ügyfélre vonatkozó példák

Ez a szakasz a **com. microsoft. Azure. SDK. IoT. Service. DeviceTwin** névtérből származó, a IoT hub szolgáltatás ügyfelét és a **DeviceTwin** és **DeviceMethod** osztályt használó Java-példákat mutatja be. A **DeviceTwin** osztály használatával kommunikálhat az eszköz állapotával az eszközök ikrek használatával. A **DeviceTwin** osztály használatával is [lekérdezheti az eszközök regisztrációját](../articles/iot-hub/iot-hub-devguide-query-language.md) a IoT Hubban. A **DeviceMethod** osztály használatával parancsokat hívhat meg az eszközön. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg. A kódrészletekben a `deviceId` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-device-twin-and-model-id"></a>Az eszköz Twin és Model AZONOSÍTÓjának lekérése

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközhöz tartozó Twin és Model ID beszerzése:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>Eszköz dupla frissítése

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A frissítés előtt be kell szereznie a Twin-et. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A frissítés előtt be kell szereznie a Twin-et. A tulajdonság a **thermostat1** összetevőben van definiálva:

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

Egy összetevő egyik tulajdonságához a tulajdonság-javítás a következő példához hasonlóan néz ki:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>Hívási parancs

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg az alapértelmezett összetevőben definiált parancsot:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitális kettős példák

A **DigitalTwinAsyncClient** osztályt a **com. microsoft. Azure. SDK. IOT. Service. digitaltwin** névtérben használja, hogy a digitális ikrek használatával kommunikáljon az eszköz állapotával. Az alábbi példák a **UpdateOperationUtility** és a **BasicDigitalTwin** osztályt is használják ugyanabból a névtérből. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg.

A `digitalTwinid` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-digital-twin-and-model-id"></a>A digitális Twin és a modell AZONOSÍTÓjának beolvasása

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszköz digitális iker-és modell-AZONOSÍTÓjának lekéréséhez:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>Digitális dupla frissítés

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A tulajdonság a **thermostat1** összetevőben van definiálva:

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>Hívási parancs

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg az alapértelmezett összetevőben definiált parancsot:

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>Eszköz telemetria beolvasása

A IoT Plug and Play-eszközök elküldik a DTDL-modellben definiált telemetria IoT Hub. Alapértelmezés szerint a IoT Hub a telemetria egy Event Hubs-végpontra irányítja, ahol felhasználhatja azt. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldéséhez](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

A következő kódrészlet bemutatja, hogyan olvashatja el a telemetria az alapértelmezett Event Hubs végpontról. Az ebben a kódrészletben található kód a IoT Hub rövid útmutatóból [küld telemetria egy eszközről egy IoT hubhoz, és elolvasta azt egy háttérbeli alkalmazással](../articles/iot-hub/quickstart-send-telemetry-java.md):

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

Az előző kód következő kimenete azt a hőmérséklet-telemetria jeleníti meg, amelyet a No-Component **termosztát** IoT küld Plug and Play eszköznek, amely csak az alapértelmezett összetevővel rendelkezik. A `dt-dataschema` System tulajdonság a modell azonosítóját jeleníti meg:

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

Az előző kód következő kimenete a többösszetevős **TemperatureController** IoT Plug and Play eszköz által elküldett hőmérsékleti telemetria mutatja. A `dt-subject` rendszer tulajdonság a telemetria küldő összetevő nevét jeleníti meg. Ebben a példában a két összetevő a `thermostat1` és `thermostat2` a DTDL modellben meghatározott módon van meghatározva. A `dt-dataschema` System tulajdonság a modell azonosítóját jeleníti meg:

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>Eszköz kettős módosítási értesítéseinek olvasása

A IoT Hub konfigurálhatja úgy, hogy az eszköz kettős módosítási értesítéseket készítsen egy támogatott végpontra való átirányításhoz. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző Java-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszköz kettős változási értesítéseit egy nem összetevőhöz tartozó termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

Az előző Java-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszközhöz tartozó kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>Digitális kettős változásokról szóló értesítések olvasása

A IoT Hub konfigurálhatja úgy, hogy a rendszer egy támogatott végpontra irányítsa a digitális kettős módosítási értesítések létrehozását. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző Java-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza a digitális dupla változási értesítéseket egy nem összetevővel rendelkező termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

Az előző Java-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor a IoT Hub létrehozza a digitális kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
