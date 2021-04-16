---
title: Első lépések az Azure IoT Hub (Python) használatával – | Microsoft Docs
description: Az eszközfelügyelet IoT Hub távoli eszköz-újraindítás kezdeményezéséhez. A Pythonhoz készült Azure IoT SDK használatával egy szimulált eszközalkalmazást valósíthat meg, amely közvetlen metódust és a közvetlen metódust meghívó szolgáltatásalkalmazást tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: c07d26715aff2c8dd5e00a7d7adbb548adf00a28
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482500"
---
# <a name="get-started-with-device-management-python"></a>Az eszközkezelés első lépések (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal hozzon létre egy IoT Hub és hozzon létre egy eszközidentitást az IoT Hubban.

* Hozzon létre egy szimulált eszközalkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok a felhőből vannak meghívva.

* Hozzon létre egy Python-konzolalkalmazást, amely a szimulált eszközalkalmazás közvetlen újraindítási metódusát hívja meg az IoT Hubon keresztül.

Az oktatóanyag végén két Python-konzolalkalmazása lesz:

* **dmpatterns_getstarted_device.py,** amely csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitával, közvetlen újraindítási metódust fogad, fizikai újraindítást szimulál, és jelenti az utolsó újraindítás idejét.

* **dmpatterns_getstarted_service.py** fájl, amely egy közvetlen metódust hív meg a szimulálteszköz-alkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben található eszközminta MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy egyes vállalati és oktatási hálózati környezetekben ez a port le van tiltva. További információ és a probléma megoldásának módjai: Csatlakozás IoT Hub [(MQTT) .](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT Hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Olyan Python-konzolalkalmazás létrehozása, amely válaszol a felhő által hívott közvetlen metódusra

* Eszköz-újraindítás szimulálása

* A jelentett tulajdonságok használatával engedélyezheti, hogy az ikereszköz-lekérdezések azonosítják az eszközöket, és hogy mikor újraindultak utoljára

1. A parancssorban futtassa a következő parancsot az **azure-iot-device csomag telepítéséhez:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.py** nevű fájlt a munkakönyvtárban.

3. Adja hozzá a következő `import` utasításokat a **dmpatterns_getstarted_device.py fájl elején.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adja hozzá **CONNECTION_STRING** változót. Cserélje le `{deviceConnectionString}` a helyőrző értékét az eszköz kapcsolati sztringjére. Ezt a kapcsolati sztringet korábban a [Register a new device in the IoT Hub (Új eszköz regisztrálása az IoT Hubban) központban korábban kimásolódta.](#register-a-new-device-in-the-iot-hub)  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adja hozzá a következő függvény-visszahívásokat a közvetlen metódus megvalósításához az eszközön.

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

6. Indítsa el a közvetlen metódus figyelőt, és várjon.

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

7. Mentse és zárja be **a dmpatterns_getstarted_device.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási szabályzatokat (például exponenciális le- és leépítést) kell implementálni az átmeneti hibák kezelésével kapcsolatos cikkben [javasolt módon.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Az IoT Hub kapcsolati sztring lekérte

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban egy Python-konzolalkalmazást hoz létre, amely távoli újraindítást kezdeményez egy eszközön egy közvetlen módszerrel. Az alkalmazás ikereszköz-lekérdezésekkel deríti fel az eszköz legutóbbi újraindítási idejét.

1. Futtassa a következő parancsot a parancssorban az **azure-iot-hub csomag telepítéséhez:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.py** nevű fájlt a munkakönyvtárban.

3. Adja hozzá a következő `import` utasításokat a **dmpatterns_getstarted_service.py fájl elején.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adja hozzá a következő változódeklarációkat. Cserélje le a helyőrző értékét a Korábban az IoT Hub kapcsolati sztring letöltőjében kimásott `{IoTHubConnectionString}` [IoT Hub kapcsolati sztringre.](#get-the-iot-hub-connection-string) Cserélje le a helyőrző értékét az Új eszköz regisztrálása az `{deviceId}` [IoT Hubban helyen regisztrált eszközazonosítóra.](#register-a-new-device-in-the-iot-hub)

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adja hozzá a következő függvényt, amely meghívja az eszköz metódusát a céleszköz újraindítása érdekében, majd lekérdezi az ikereszközt, és lekérdezi az utolsó újraindítás idejét.

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

6. Mentse és zárja be a **dmpatterns_getstarted_service.py** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban futtassa a következő parancsot a közvetlen újraindítási metódus figyelmének megkezdéséhez.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Egy másik parancssorban futtassa a következő parancsot a távoli újraindítás aktiválására, és az ikereszköz lekérdezésével keresse meg az utolsó újraindítás idejét.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Az eszköz válasza a közvetlen metódusra a konzolon látható.

   Az alábbiakban látható az eszköz válasza a közvetlen újraindítási metódusra:

   ![Szimulálteszköz-alkalmazás kimenete](./media/iot-hub-python-python-device-management-get-started/device.png)

   Az alábbiakban látható a közvetlen újraindítási metódust hívó szolgáltatás, és lekérdezi az ikereszköz állapotát:

   ![Újraindítási szolgáltatás kimenetének aktiválása](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
