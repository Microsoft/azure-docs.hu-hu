---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244579"
---
A következő erőforrások is elérhetők:

- [Node.js SDK-referenciák dokumentációja](/javascript/api/azure-iothub)
- [Szolgáltatás-ügyfél minták](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Digitális Twins-minták](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT Hub szolgáltatási ügyfélre vonatkozó példák

Ez a szakasz JavaScript-példákat mutat be a IoT Hub szolgáltatás ügyfelének és a **beállításjegyzéknek** és az **ügyfél** osztályoknak a használatával. A **beállításjegyzék** osztály használatával kommunikálhat az eszköz állapotával az eszközök ikrek használatával. A **beállításjegyzék** osztály használatával is [lekérdezheti az eszközök regisztrációját](../articles/iot-hub/iot-hub-devguide-query-language.md) a IoT Hubban. Az **ügyfél** osztály használatával parancsokat hívhat meg az eszközön. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg. A kódrészletekben a `deviceId` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-device-twin-and-model-id"></a>Az eszköz Twin és Model AZONOSÍTÓjának lekérése

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközhöz tartozó Twin és Model ID beszerzése:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Eszköz dupla frissítése

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A minta azt mutatja be, hogyan kell lekérni a Twin alkalmazást a frissítés előtt. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A minta azt mutatja be, hogyan kell lekérni a Twin alkalmazást a frissítés előtt. A tulajdonság a **thermostat1** összetevőben van definiálva:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitális kettős példák

A **DigitalTwinClient** osztály használatával a digitális Twins használatával kommunikálhat az eszköz állapotával. Az eszköz [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellje az eszköz által megvalósított tulajdonságokat és parancsokat határozza meg.

Ez a szakasz a digitális Twins API-t használó JavaScript-példákat mutatja be.

A `digitalTwinId` változó tárolja az IoT hub-ban regisztrált IoT Plug and Play eszköz azonosítóját.

### <a name="get-the-digital-twin-and-model-id"></a>A digitális Twin és a modell AZONOSÍTÓjának beolvasása

Az IoT hub-hoz csatlakoztatott IoT Plug and Play eszköz digitális iker-és modell-AZONOSÍTÓjának lekéréséhez:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Digitális dupla frissítés

A következő kódrészlet bemutatja, hogyan frissíthető `targetTemperature` egy eszköz tulajdonsága. A tulajdonság az eszköz alapértelmezett összetevőjében van meghatározva:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Az alábbi kódrészletből megtudhatja, hogyan frissítheti `targetTemperature` egy összetevő tulajdonságát. A tulajdonság a **thermostat1** összetevőben van definiálva:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Hívási parancs

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg az alapértelmezett összetevőben definiált parancsot:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Az alábbi kódrészletből megtudhatja, hogyan hívhatja `getMaxMinReport` meg a parancsot egy összetevőn. A parancs a **thermostat1** összetevőben van definiálva:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Eszköz telemetria beolvasása

A IoT Plug and Play-eszközök elküldik a DTDL-modellben definiált telemetria IoT Hub. Alapértelmezés szerint a IoT Hub a telemetria egy Event Hubs-végpontra irányítja, ahol felhasználhatja azt. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldéséhez](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

A következő kódrészlet bemutatja, hogyan olvashatja el a telemetria az alapértelmezett Event Hubs végpontról. Az ebben a kódrészletben található kód a IoT Hub rövid útmutatóból [küld telemetria egy eszközről egy IoT hubhoz, és elolvasta azt egy háttérbeli alkalmazással](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Az előző kód következő kimenete a többösszetevős **TemperatureController** IoT Plug and Play eszköz által elküldett hőmérsékleti telemetria mutatja. A `dt-subject` rendszer tulajdonság a telemetria küldő összetevő nevét jeleníti meg. Ebben a példában a két összetevő a `thermostat1` és `thermostat2` a DTDL modellben meghatározott módon van meghatározva. A `dt-dataschema` System tulajdonság a modell azonosítóját jeleníti meg:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Eszköz kettős módosítási értesítéseinek olvasása

A IoT Hub konfigurálhatja úgy, hogy az eszköz kettős módosítási értesítéseket készítsen egy támogatott végpontra való átirányításhoz. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző JavaScript-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszköz kettős változási értesítéseit egy nem összetevőhöz tartozó termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Az előző JavaScript-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza az eszközhöz tartozó kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Digitális kettős változásokról szóló értesítések olvasása

A IoT Hub konfigurálhatja úgy, hogy a rendszer egy támogatott végpontra irányítsa a digitális kettős módosítási értesítések létrehozását. További információ: [IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez különböző végpontokra > nem telemetria események](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Az előző JavaScript-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor IoT Hub létrehozza a digitális dupla változási értesítéseket egy nem összetevővel rendelkező termosztátos eszközhöz. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Az előző JavaScript-kódrészletben megjelenő kód a következő kimenetet hozza létre, amikor a IoT Hub létrehozza a digitális kettős változási értesítéseket egy összetevővel rendelkező eszközhöz. Ez a példa a kimenetet mutatja be, amikor a termosztát összetevővel rendelkező hőmérséklet-érzékelő eszköz értesítéseket hoz létre. Az alkalmazás tulajdonságai `iothub-message-schema` és `opType` információk a változási értesítés típusáról:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
