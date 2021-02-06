---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 236c8856dc2e693518f2f0055b622c9a1c88695c
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616348"
---
## <a name="model-id-announcement"></a>Modell AZONOSÍTÓjának bejelentése

A modell AZONOSÍTÓjának bejelentéséhez az eszköznek tartalmaznia kell azt a kapcsolódási adatok között:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> Modulok és IoT Edge esetén használja a következőt `IoTHubModuleClient` : `IoTHubDeviceClient` .

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

A [IoT Plug and Play-modellek összetevőinek ismertetése](../articles/iot-pnp/concepts-components.md)című témakörben leírtak szerint az eszköz-építőknek el kell dönteniük, hogy milyen összetevőket szeretnének használni az eszközeik leírásához. Az összetevők használatakor az eszközöknek az ebben a részben ismertetett szabályokat kell követniük.

### <a name="telemetry"></a>Telemetria

Az alapértelmezett összetevő nem igényel speciális tulajdonságot.

Beágyazott összetevők használatakor az eszközöknek egy üzenet tulajdonságot kell megadniuk az összetevő nevével:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Csak olvasható tulajdonságok

Az alapértelmezett összetevőből származó tulajdonságok jelentése nem igényel különleges szerkezetet:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
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

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Írható tulajdonságok

Ezeket a tulajdonságokat beállíthatja az eszköz, vagy frissítheti a megoldás. Ha a megoldás frissíti a tulajdonságot, az ügyfél visszahívási értesítést kap a vagy a alkalmazásban `IoTHubDeviceClient` `IoTHubModuleClient` . A IoT Plug and Play konvenciók követéséhez az eszköznek tájékoztatnia kell a szolgáltatást arról, hogy a tulajdonság sikeresen megérkezett.

#### <a name="report-a-writable-property"></a>Írható tulajdonság jelentése

Ha egy eszköz írható tulajdonságot jelent, tartalmaznia kell az `ack` egyezményekben definiált értékeket.

Írható tulajdonság bejelentése az alapértelmezett összetevőből:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

Az eszköz Twin a következő jelentett tulajdonsággal frissült:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Egy beágyazott összetevőből származó írható tulajdonság jelentéséhez a Twin elemnek tartalmaznia kell egy jelölőt:

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Előfizetés a kívánt tulajdonság frissítéseire

A szolgáltatások frissíthetik a csatlakoztatott eszközökön értesítést kiváltó kívánt tulajdonságokat. Ez az értesítés tartalmazza a frissített kívánt tulajdonságokat, beleértve a frissítést azonosító verziószámot is. Az eszközöknek a jelentett tulajdonságokkal megegyező üzenettel kell válaszolniuk `ack` .

Az alapértelmezett összetevő látja az egyetlen tulajdonságot, és létrehozza a jelentett `ack` verziót a kapott verzióval:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

Az eszközök Twin összetevője a kívánt és jelentett szakaszt mutatja a következőképpen:

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

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
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

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> A kérelem és a válasz neve nem szerepel a dróton továbbított szerializált adattartalomban.
