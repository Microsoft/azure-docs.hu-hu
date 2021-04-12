---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: d878c7abf025b5c66790a96f9f921f669dcdf1ef
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491119"
---
## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../articles/iot-central/core/quick-deploy-iot-central.md). Az alkalmazást a 2020. július 14-én vagy azt követően kell létrehozni.
* Egy, a [Python](https://www.python.org/) 3,7-es vagy újabb verzióját futtató fejlesztési gép. A `python --version` parancssorban futtatva ellenőrizhető a verzió. A Python számos operációs rendszer számára elérhető. Az oktatóanyagban szereplő utasítások feltételezik, hogy a **Python** -parancsot a Windows-parancssorban futtatja.
* A [Microsoft Azure IOT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) GitHub-adattár helyi másolata, amely tartalmazza a mintakód. Ezzel a hivatkozással töltheti le az adattár másolatát: [zip letöltése](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Ezután bontsa ki a fájlt egy megfelelő helyre a helyi gépen.

## <a name="review-the-code"></a>A kód áttekintése

A korábban letöltött, a Pythonhoz készült Microsoft Azure IoT SDK másolatában nyissa meg az *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples/PnP/temp_controller_with_thermostats.* Copy fájlt egy szövegszerkesztőben.

Amikor futtatja a mintát IoT Centralhoz való csatlakozáshoz, az eszköz kiépítési szolgáltatását (DPS) használja az eszköz regisztrálásához és egy kapcsolati karakterlánc létrehozásához. A minta lekérdezi a DPS által igényelt, a parancssori környezettől kapott kapcsolatok információit.

A `main` függvény:

* A DPS használatával kiépíti az eszközt. A kiépítési információ tartalmazza a modell AZONOSÍTÓját. IoT Central a modell AZONOSÍTÓját használja az eszközhöz tartozó sablon azonosításához vagy létrehozásához. További információ: [eszköz hozzárendelése egy eszköz sablonnal](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Létrehoz egy `Device_client` objektumot, és beállítja a `dtmi:com:example:TemperatureController;2` modell azonosítóját a kapcsolatok megnyitása előtt.
* Kezdeti tulajdonságértékek küldése IoT Centralra. A használatával `pnp_helper` hozza létre a javításokat.
* Figyelőket hoz létre a `getMaxMinReport` és `reboot` parancsokhoz. Mindegyik termosztát-összetevő saját `getMaxMinReport` paranccsal rendelkezik.
* Tulajdonság-figyelőt hoz létre az írható tulajdonságok frissítéseinek figyeléséhez.
* Elindítja a ciklust, hogy a rendszer a két termosztát-összetevőből és a munkakészlet telemetria származó hőmérséklet-telemetria a 8 másodpercenkénti alapértelmezett összetevőből küldje el.

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
            print("Device was assigned")
            print(registration_result.registration_state.assigned_hub)
            print(registration_result.registration_state.device_id)
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

    ################################################
    # Update readable properties from various components

    properties_root = pnp_helper.create_reported_properties(serialNumber=serial_number)
    properties_thermostat1 = pnp_helper.create_reported_properties(
        thermostat_1_component_name, maxTempSinceLastReboot=98.34
    )
    properties_thermostat2 = pnp_helper.create_reported_properties(
        thermostat_2_component_name, maxTempSinceLastReboot=48.92
    )
    properties_device_info = pnp_helper.create_reported_properties(
        device_information_component_name,
        swVersion="5.5",
        manufacturer="Contoso Device Corporation",
        model="Contoso 4762B-turbo",
        osName="Mac Os",
        processorArchitecture="x86-64",
        processorManufacturer="Intel",
        totalStorage=1024,
        totalMemory=32,
    )

    property_updates = asyncio.gather(
        device_client.patch_twin_reported_properties(properties_root),
        device_client.patch_twin_reported_properties(properties_thermostat1),
        device_client.patch_twin_reported_properties(properties_thermostat2),
        device_client.patch_twin_reported_properties(properties_device_info),
    )

    ################################################
    # Get all the listeners running
    print("Listening for command requests and property updates")

    global THERMOSTAT_1
    global THERMOSTAT_2
    THERMOSTAT_1 = Thermostat(thermostat_1_component_name, 10)
    THERMOSTAT_2 = Thermostat(thermostat_2_component_name, 10)

    listeners = asyncio.gather(
        execute_command_listener(
            device_client, method_name="reboot", user_command_handler=reboot_handler
        ),
        execute_command_listener(
            device_client,
            thermostat_1_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_command_listener(
            device_client,
            thermostat_2_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    ################################################
    # Function to send telemetry every 8 seconds

    async def send_telemetry():
        print("Sending telemetry from various components")

        while True:
            curr_temp_ext = random.randrange(10, 50)
            THERMOSTAT_1.record(curr_temp_ext)

            temperature_msg1 = {"temperature": curr_temp_ext}
            await send_telemetry_from_temp_controller(
                device_client, temperature_msg1, thermostat_1_component_name
            )

            curr_temp_int = random.randrange(10, 50)  # Current temperature in Celsius
            THERMOSTAT_2.record(curr_temp_int)

            temperature_msg2 = {"temperature": curr_temp_int}

            await send_telemetry_from_temp_controller(
                device_client, temperature_msg2, thermostat_2_component_name
            )

            workingset_msg3 = {"workingSet": random.randrange(1, 100)}
            await send_telemetry_from_temp_controller(device_client, workingset_msg3)

    send_telemetry_task = asyncio.ensure_future(send_telemetry())

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

A `execute_command_listener` függvény a parancs kéréseit kezeli, akkor futtatja a `max_min_handler` függvényt, amikor az eszköz megkapja a parancsot a `getMaxMinReport` termosztát összetevőihez és a `reboot_handler` függvényhez, amikor az eszköz megkapja a `reboot` parancsot. A modul használatával hozza `pnp_helper` létre a választ:

```python
async def execute_command_listener(
    device_client,
    component_name=None,
    method_name=None,
    user_command_handler=None,
    create_user_response_handler=None,
):
    while True:
        if component_name and method_name:
            command_name = component_name + "*" + method_name
        elif method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        values = command_request.payload
        print(values)

        if user_command_handler:
            await user_command_handler(values)
        else:
            print("No handler provided to execute")

        (response_status, response_payload) = pnp_helper.create_response_payload_with_status(
            command_request, method_name, create_user_response=create_user_response_handler
        )

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

A `async def execute_property_listener` kezeli az írható tulajdonságok frissítéseit, például `targetTemperature` a termosztát összetevőihez, és a JSON-választ generálja. A modul használatával hozza `pnp_helper` létre a választ:

```python
async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        print(patch)
        properties_dict = pnp_helper.create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

A `send_telemetry_from_temp_controller` függvény elküldi a telemetria üzeneteket a termosztát összetevőiről IoT Centralra. A modul használatával hozza `pnp_helper` létre az üzeneteket:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = pnp_helper.create_telemetry(telemetry_msg, component_name)
    await device_client.send_message(msg)
    print("Sent message")
    print(msg)
    await asyncio.sleep(5)
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>A kód futtatása

A minta alkalmazás futtatásához nyisson meg egy parancssori környezetet, és nyissa meg az *Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* mappát, amely tartalmazza a *temp_controller_with_thermostats.* a minta fájlt.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Telepítse a szükséges csomagokat:

```cmd/sh
pip install azure-iot-device
```

Minta futtatása:

```cmd/sh
python temp_controller_with_thermostats.py
```

Az alábbi kimenetben az eszköz regisztrálása és a IoT Centralhoz való csatlakozás látható. A minta elküldi a `maxTempSinceLastReboot` tulajdonságokat a két termosztát-összetevőtől, mielőtt elkezdené a telemetria küldését:

```cmd/sh
Device was assigned
iotc-60a.....azure-devices.net
sample-device-01
Updating pnp properties for root interface
{'serialNumber': 'alohomora'}
Updating pnp properties for thermostat1
{'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
Updating pnp properties for thermostat2
{'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
Updating pnp properties for deviceInformation
{'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
Listening for command requests and property updates
Press Q to quit
Sending telemetry from various components
Sent message
{"temperature": 27}
Sent message
{"temperature": 17}
Sent message
{"workingSet": 13}
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

```cmd/sh
{'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
the data in the desired properties patch was: {'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
Values received are :-
{'targetTemperature': 67, '__t': 'c'}
Sent message

...

Command request received with payload
2021-03-31T05:00:00.000Z
Will return the max, min and average temperature from the specified time 2021-03-31T05:00:00.000Z to the current time
Done generating
{"avgTemp": 4.0, "endTime": "2021-03-31T12:29:48.322427", "maxTemp": 18, "minTemp": null, "startTime": "2021-03-31T12:28:28.322381"}
```
