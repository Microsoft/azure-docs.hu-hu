---
title: Eszköz fejlesztői útmutatója (C#) – IoT Plug and Play | Microsoft Docs
description: A C#-IoT Plug and Play leírása
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 22ffbed56b15b55b28bf150b90e489be9e4cfeaf
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090420"
---
# <a name="iot-plug-and-play-device-developer-guide-c"></a>IoT Plug and Play-eszköz fejlesztői útmutatója (C#)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>Modell AZONOSÍTÓjának bejelentése

A modell AZONOSÍTÓjának bejelentéséhez az eszköznek tartalmaznia kell azt a kapcsolódási adatok között:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Az új `ClientOptions` túlterhelés a `DeviceClient` kapcsolatok inicializálásához használt összes módszernél elérhető.

> [!TIP]
> Modulok és IoT Edge esetén használja a következőt `ModuleClient` : `DeviceClient` .

## <a name="dps-payload"></a>DPS-adattartalom

A [Device kiépítési szolgáltatást (DPS)](../iot-dps/about-iot-dps.md) használó eszközök tartalmazhatják a `modelId` kiépítési folyamat során a következő JSON-adattartalommal használható eszközöket.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetria, tulajdonságok és parancsok implementálása

A [IoT Plug and Play-modellek összetevőinek ismertetése](concepts-components.md)című témakörben leírtak szerint az eszköz-építőknek el kell dönteniük, hogy milyen összetevőket szeretnének használni az eszközeik leírásához. Az összetevők használatakor az eszközöknek az ebben a részben ismertetett szabályokat kell követniük.

### <a name="telemetry"></a>Telemetria

Az alapértelmezett összetevő nem igényel speciális tulajdonságot.

Beágyazott összetevők használatakor az eszközöknek egy üzenet tulajdonságot kell megadniuk az összetevő nevével:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Csak olvasható tulajdonságok

Az alapértelmezett összetevőből származó tulajdonságok jelentése nem igényel különleges szerkezetet:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Beágyazott összetevők használatakor a tulajdonságokat az összetevő nevén belül kell létrehozni:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
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

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Egy beágyazott összetevőből származó írható tulajdonság jelentéséhez a Twin elemnek tartalmaznia kell egy jelölőt:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
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

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
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
          "ad": "complete"
      }
  }
}
```

A beágyazott összetevők megkapják az összetevő nevével burkolt kívánt tulajdonságokat, és jelenteniük kell a `ack` jelentett tulajdonságot:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
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

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Kérelmek és válaszok hasznos adatai

A parancsok a kérelem és a válasz hasznos adatai meghatározására használják a típusokat. Az eszköznek deszerializálnia kell a bejövő bemeneti paramétert, és szerializálnia kell a választ. Az alábbi példa bemutatja, hogyan implementálhat egy olyan parancsot, amely a hasznos adatokban definiált komplex típusokat tartalmazza:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

A következő kódrészletek azt mutatják be, hogyan valósítja meg egy eszköz a parancs definícióját, beleértve a szerializálás és a deszerializálás engedélyezéséhez használt típusokat is:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> A kérelem és a válasz neve nem szerepel a dróton továbbított szerializált adattartalomban.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
