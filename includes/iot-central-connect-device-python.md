---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 2eff30333362d461f196972fbaedbeac8f2ae7c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033861"
---
## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../articles/iot-central/core/quick-deploy-iot-central.md). Az alkalmazást a 2020. július 14-én vagy azt követően kell létrehozni.
* Egy, a [Python](https://www.python.org/) 3,7-es vagy újabb verzióját futtató fejlesztési gép. A `python --version` parancssorban futtatva ellenőrizhető a verzió. A Python számos operációs rendszer számára elérhető. Az oktatóanyagban szereplő utasítások feltételezik, hogy a **Python** -parancsot a Windows-parancssorban futtatja.
* A [Microsoft Azure IOT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) GitHub-adattár helyi másolata, amely tartalmazza a mintakód. Ezzel a hivatkozással töltheti le az adattár másolatát: [zip letöltése](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Ezután bontsa ki a fájlt egy megfelelő helyre a helyi gépen.

## <a name="review-the-code"></a>A kód áttekintése

A korábban letöltött, a Pythonhoz készült Microsoft Azure IoT SDK másolatában nyissa meg az *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP/simple_thermostat.* Copy fájlt egy szövegszerkesztőben.

Amikor futtatja a mintát IoT Centralhoz való csatlakozáshoz, az eszköz kiépítési szolgáltatását (DPS) használja az eszköz regisztrálásához és egy kapcsolati karakterlánc létrehozásához. A minta lekérdezi a DPS által igényelt, a parancssori környezettől kapott kapcsolatok információit.

A `main` függvény:

* A DPS használatával kiépíti az eszközt. A kiépítési információ tartalmazza a modell AZONOSÍTÓját. IoT Central a modell AZONOSÍTÓját használja az eszközhöz tartozó sablon azonosításához vagy létrehozásához. További információ: [eszköz hozzárendelése egy eszköz sablonnal](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Létrehoz egy `Device_client` objektumot, és beállítja a `dtmi:com:example:Thermostat;1` modell azonosítóját a kapcsolatok megnyitása előtt.
* A `maxTempSinceLastReboot` tulajdonságot a IoT Central küldi.
* Létrehoz egy figyelőt a `getMaxMinReport` parancshoz.
* Tulajdonság-figyelőt hoz létre az írható tulajdonságok frissítéseinek figyeléséhez.
* Elindít egy hurkot, amely 10 másodpercenként elküldi a hőmérséklet-telemetria.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":

            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":

        # ...

    # Connect the client.
    await device_client.connect()

    max_temp = 10.96  # Initial Max Temp otherwise will not pass certification
    await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": max_temp})

    listeners = asyncio.gather(
        execute_command_listener(
            device_client,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    async def send_telemetry():
        global max_temp
        global min_temp
        current_avg_idx = 0

        while True:
            current_temp = random.randrange(10, 50)
            if not max_temp:
                max_temp = current_temp
            elif current_temp > max_temp:
                max_temp = current_temp

            if not min_temp:
                min_temp = current_temp
            elif current_temp < min_temp:
                min_temp = current_temp

            avg_temp_list[current_avg_idx] = current_temp
            current_avg_idx = (current_avg_idx + 1) % moving_window_size

            temperature_msg1 = {"temperature": current_temp}
            await send_telemetry_from_thermostat(device_client, temperature_msg1)
            await asyncio.sleep(8)

    send_telemetry_task = asyncio.create_task(send_telemetry())

    # ...
```

A `provision_device` függvény a DPS használatával kiépíti az eszközt, és regisztrálja azt a IoT Central. A függvény tartalmazza az eszköz modell-AZONOSÍTÓját, amelyet a IoT Central az eszköz [sablonhoz való hozzárendelésére](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)használ a kiépítési hasznos adattartalomban:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )
    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

A `execute_command_listener` függvény a parancs kéréseit kezeli, futtatja a `max_min_handler` függvényt, amikor az eszköz megkapja a `getMaxMinReport` parancsot, majd a függvény futtatásával `create_max_min_report_response` létrehozza a választ:

```python
async def execute_command_listener(
    device_client, method_name, user_command_handler, create_user_response_handler
):
    while True:
        if method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        print(command_request.payload)

        values = {}
        if not command_request.payload:
            print("Payload was empty.")
        else:
            values = command_request.payload

        await user_command_handler(values)

        response_status = 200
        response_payload = create_user_response_handler(values)

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

A `async def execute_property_listener` kezeli az írható tulajdonságok frissítéseit, például `targetTemperature` a és a JSON-választ:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        print("the data in the desired properties patch was: {}".format(patch))

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

A `send_telemetry_from_thermostat` függvény elküldi a telemetria üzeneteket a IoT Centralnak:

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>A kód futtatása

A minta alkalmazás futtatásához nyisson meg egy parancssori környezetet, és nyissa meg az *Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* mappát, amely tartalmazza a *simple_thermostat.* a minta fájlt.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Telepítse a szükséges csomagokat:

```cmd/sh
pip install azure-iot-device
```

Minta futtatása:

```cmd/sh
python simple_thermostat.py
```

Az alábbi kimenetben az eszköz regisztrálása és a IoT Centralhoz való csatlakozás látható. A minta elküldi a `maxTempSinceLastReboot` tulajdonságot a telemetria küldésének megkezdése előtt:

```cmd/sh
Device was assigned
iotc-.......azure-devices.net
sample-device-01
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
Sent message
Sent message
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

```cmd/sh
Sent message
the data in the desired properties patch was: {'targetTemperature': {'value': 86.3}, '$version': 2}
Sent message

...

Sent message
Command request received with payload
2020-10-14T08:00:00.000Z
Will return the max, min and average temperature from the specified time 2020-10-14T08:00:00.000Z to the current time
Done generating
{"avgTemp": 31.5, "endTime": "2020-10-16T10:07:41.580722", "maxTemp": 49, "minTemp": 12, "startTime": "2020-10-16T10:06:21.580632"}
```
