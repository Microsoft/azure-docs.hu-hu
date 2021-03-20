---
title: Ismerkedés az Azure IoT Hub-eszközkezelés (Python) szolgáltatással | Microsoft Docs
description: Távoli eszköz újraindításának kezdeményezése IoT Hub-eszközkezelés használatával. A Pythonhoz készült Azure IoT SDK-val egy olyan szimulált eszköz alkalmazást hozhat létre, amely közvetlen metódust és egy közvetlen metódust meghívó szolgáltatási alkalmazást tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87872718"
---
# <a name="get-started-with-device-management-python"></a>Ismerkedés az Eszközkezelővel (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal használatával hozzon létre egy IoT Hub, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy olyan Python-konzol alkalmazást, amely a szimulált eszközön a IoT hub használatával meghívja a közvetlen újraindítási módszert.

Az oktatóanyag végén két Python-konzolos alkalmazás található:

* **dmpatterns_getstarted_device.** a korábban létrehozott eszköz identitásával csatlakozik az IoT hubhoz, a rendszer újraindítási közvetlen módszert kap, szimulálja a fizikai újraindítást, és az utolsó újraindítás idejét jelenti.

* **dmpatterns_getstarted_service.** a szimulált eszköz alkalmazásban egy közvetlen metódust meghívó. a. a parancs megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Hozzon létre egy olyan Python-konzol alkalmazást, amely válaszol a felhő által meghívott közvetlen metódusra

* Eszköz újraindításának szimulálása

* A jelentett tulajdonságok használatával engedélyezheti az eszköz kettős lekérdezéseit az eszközök azonosításához és a legutóbbi újraindításkor

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.** -es nevű fájlt a munkakönyvtárában.

3. Adja hozzá a következő `import` utasításokat a **dmpatterns_getstarted_device.** a fájl elején.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adja hozzá a **CONNECTION_STRING** változót. Cserélje le a `{deviceConnectionString}` helyőrző értékét az eszköz csatlakoztatási karakterláncára. Ezt a kapcsolattípus-karakterláncot korábban a [IoT hub új eszközének regisztrálása](#register-a-new-device-in-the-iot-hub)során másolta.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adja hozzá a következő függvény visszahívásait a közvetlen metódus eszközön való megvalósításához.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Indítsa el a Direct metódus-figyelőt, és várjon.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Mentse és zárjuk be a **dmpatterns_getstarted_device.** a fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz kettős lekérdezéseit használva észleli az adott eszköz utolsó újraindításának idejét.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.** -es nevű fájlt a munkakönyvtárában.

3. Adja hozzá a következő `import` utasításokat a **dmpatterns_getstarted_service.** a fájl elején.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adja hozzá a következő változó deklarációkat. Cserélje le a `{IoTHubConnectionString}` helyőrző értékét a korábban átmásolt IoT hub-beli [IoT hub-kapcsolatok karakterláncának lekérése](#get-the-iot-hub-connection-string)elemre. Cserélje le a `{deviceId}` helyőrző értékét az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban regisztrált eszköz azonosítójával.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adja hozzá a következő függvényt az eszköz metódusának meghívásához a céleszköz újraindításához, majd az eszközhöz tartozó ikrek lekérdezéséhez és az utolsó újraindítás időpontjának lekéréséhez.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Mentse és zárjuk be a **dmpatterns_getstarted_service.** a fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban futtassa a következő parancsot a közvetlen újraindítási módszer figyelésének megkezdéséhez.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Egy másik parancssorban futtassa a következő parancsot a Távoli újraindítás és a lekérdezés elindításához a Twin eszközre, hogy megkeresse az utolsó újraindítási időt.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Megjelenik az eszköz válasza a Direct metódusra a konzolon.

   Az alábbi ábrán látható az eszköz válasza a közvetlen újraindítási módszerre:

   ![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-python-python-device-management-get-started/device.png)

   Az alábbi ábrán látható, hogy a szolgáltatás hívja meg a Direct reboot metódust, és az eszköz Twin lekérdezése állapotot kérdezi le:

   ![Újraindítási szolgáltatás kimenetének indítása](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
