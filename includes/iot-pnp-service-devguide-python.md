---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510614"
---
A következő erőforrások is elérhetők:

- [A Python SDK referenciadokumentációja](/python/api/azure-iot-hub/azure.iot.hub)
- [Szolgáltatás-ügyfél minták](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digitális Twins-minták](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>IoT Hub szolgáltatási ügyfélre vonatkozó példák

Ez a szakasz a IoT Hub szolgáltatás ügyfelét és a **IoTHubRegistryManager** és a **CloudToDeviceMethod** osztályt használó Python-példákat mutatja be. A **IoTHubRegistryManager** osztály használatával kommunikálhat az eszköz állapotával az eszközök ikrek használatával. A **IoTHubRegistryManager** osztály használatával is [lekérdezheti az eszközök regisztrációját](../articles/iot-hub/iot-hub-devguide-query-language.md) a IoT Hubban. A **CloudToDeviceMethod** osztály használatával parancsokat hívhat meg az eszközön. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg. A kódrészletekben a `device_id` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-device-twin-and-model-id"></a>Az eszköz Twin és Model AZONOSÍTÓjának lekérése

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközhöz tartozó Twin és Model ID beszerzése:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Eszköz dupla frissítése

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A minta azt mutatja be, hogyan kell lekérni a Twin-ket a `etag` frissítés előtt. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A minta azt mutatja be, hogyan kell lekérni a Twin-ket a `ETag` frissítés előtt. A tulajdonság a **thermostat1** összetevőben van definiálva:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Egy összetevő egyik tulajdonságához a tulajdonság-javítás a következő példához hasonlóan néz ki:

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Hívási parancs

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg az alapértelmezett összetevőben definiált parancsot:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitális kettős példák

A **DigitalTwinClient** osztály használatával a digitális Twins használatával kommunikálhat az eszköz állapotával. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg.

A `device_id` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-digital-twin-and-model-id"></a>A digitális Twin és a modell AZONOSÍTÓjának beolvasása

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszköz digitális iker-és modell-AZONOSÍTÓjának lekéréséhez:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Digitális dupla frissítés

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A tulajdonság a **thermostat1** összetevőben van definiálva:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Hívási parancs

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg az alapértelmezett összetevőben definiált parancsot:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Eszköz telemetria beolvasása

A IoT Plug and Play-eszközök elküldik a DTDL-modellben definiált telemetria IoT Hub. Alapértelmezés szerint a IoT Hub a telemetria egy Event Hubs-végpontra irányítja, ahol felhasználhatja azt. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldéséhez](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

A következő kódrészlet bemutatja, hogyan olvashatja el a telemetria az alapértelmezett Event Hubs végpontról. Az ebben a kódrészletben található kód a IoT Hub rövid útmutatóból [küld telemetria egy eszközről egy IoT hubhoz, és elolvasta azt egy háttérbeli alkalmazással](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Az előző kód következő kimenete azt a hőmérséklet-telemetria jeleníti meg, amelyet a No-Component **termosztát** IoT küld Plug and Play eszköznek, amely csak az alapértelmezett összetevővel rendelkezik. A `dt-dataschema` System tulajdonság a modell azonosítóját jeleníti meg:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Az előző kód következő kimenete a többösszetevős **TemperatureController** IoT Plug and Play eszköz által elküldett hőmérsékleti telemetria mutatja. A `dt-subject` rendszer tulajdonság a telemetria küldő összetevő nevét jeleníti meg. Ebben a példában a két összetevő a `thermostat1` és `thermostat2` a DTDL modellben meghatározott módon van meghatározva. A `dt-dataschema` System tulajdonság a modell azonosítóját jeleníti meg:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Eszköz kettős módosítási értesítéseinek olvasása

A IoT Hub konfigurálhatja úgy, hogy az eszköz kettős módosítási értesítéseket készítsen egy támogatott végpontra való átirányításhoz. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző Python-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszköz kettős változási értesítéseit egy nem összetevőhöz tartozó termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Az előző Python-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszközhöz tartozó kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Digitális kettős változásokról szóló értesítések olvasása

A IoT Hub konfigurálhatja úgy, hogy a rendszer egy támogatott végpontra irányítsa a digitális kettős módosítási értesítések létrehozását. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző Python-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza a digitális dupla változási értesítéseket egy nem összetevővel rendelkező termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Az előző Python-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor a IoT Hub létrehozza a digitális kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
