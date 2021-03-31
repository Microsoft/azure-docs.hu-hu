---
title: Ismerkedés az Azure IoT Hub Device Twins (Python) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A Pythonhoz készült Azure IoT SDK-k segítségével megvalósíthatja a szimulált eszköz alkalmazást és egy olyan szolgáltatási alkalmazást, amely hozzáadja a címkéket, és futtatja a IoT Hub lekérdezést.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87875472"
---
# <a name="get-started-with-device-twins-python"></a>Ismerkedés a Device Twins (Python) eszközzel

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Python-konzolos alkalmazás lesz:

* **AddTagsAndQuery.py**, a Python háttér-alkalmazás, amely címkéket és lekérdezéseket tesz az ikreknek.

* **ReportConnectivity.py**, egy Python-alkalmazás, amely egy olyan eszközt szimulál, amely az IoT hubhoz a korábban létrehozott eszköz identitásával csatlakozik, és a kapcsolati feltételét jelzi.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely hely metaadatainak hozzáadását teszi elérhetővé a **{Device ID}** szolgáltatáshoz hozzárendelt eszközökhöz. Ezután lekérdezi az IoT hub eszközben tárolt ikreket, és kiválasztja a Redmondban található eszközöket, majd azokat, amelyek a mobil kapcsolatokat jelentik.

1. Nyisson meg egy parancssort a munkakönyvtárában, és telepítse a **Pythonhoz készült Azure IoT hub Service SDK**-t.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery.py** -fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Adja hozzá a következő kódot. A helyére írja `[IoTHub Connection String]` be az IoT hub-beli, a [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)során másolt karakterláncot. Cserélje le a helyére `[Device Id]` az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban regisztrált eszköz azonosítóját.
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Adja hozzá a következő kódot a **AddTagsAndQuery.py** -fájlhoz:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    A **IoTHubRegistryManager** objektum az összes olyan metódust elérhetővé teszi, amely az eszközökhöz tartozó ikrek szolgáltatással való interakcióhoz szükséges. A kód először inicializálja a **IoTHubRegistryManager** objektumot, majd frissíti az eszközt a **DEVICE_ID** számára, és végül két lekérdezést futtat. Az első kiválasztja a **Redmond43** -üzemben található eszközökből származó ikreket, a második pedig csak azokat az eszközöket választja ki, amelyek mobil hálózaton keresztül is csatlakoznak.

6. Adja hozzá a következő kódot a  **AddTagsAndQuery.py** végén a **iothub_service_sample_run** függvény megvalósításához:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Futtassa az alkalmazást az alábbiakkal:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    A lekérdezés eredményei között egy eszközt kell látnia, amely a **Redmond43** -ban található összes eszközt kéri, a nem pedig a lekérdezést, amely a mobil hálózatot használó eszközökre korlátozza az eredményeket.

    ![első lekérdezés, amely a Redmondban lévő összes eszközt megjeleníti](./media/iot-hub-python-twin-getstarted/service-1.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a következőhöz csatlakozik a központhoz: **{Device ID}**, majd frissíti az eszköz Twin által jelentett tulajdonságait, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. A munkakönyvtárban található parancssorból telepítse a **Pythonhoz készült Azure IoT hub Device SDK**-t:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.py** -fájlt.

3. Adja hozzá a következő kódot a szükséges modulok importálásához az eszköz SDK-ból:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adja hozzá a következő kódot. Cserélje le a `[IoTHub Device Connection String]` helyőrző értékét az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban elemre másolt eszköz-összekapcsolási karakterlánccal.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adja hozzá a következő kódot a **ReportConnectivity.py** fájlhoz az eszköz ikrek funkciójának megvalósításához:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    A **IoTHubModuleClient** objektum az eszközön található ikrekkel való interakcióhoz szükséges összes módszert elérhetővé teszi. Az előző kód azt követően, hogy inicializálta a **IoTHubModuleClient** objektumot, lekéri az eszközhöz tartozó Twin eszközt, és frissíti a jelentett tulajdonságot a kapcsolati információkkal.

6. Adja hozzá a következő kódot a  **ReportConnectivity.py** végén a **iothub_client_sample_run** függvény megvalósításához:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Futtassa az eszköz alkalmazást:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Meg kell jelennie arról, hogy az eszköz Twin jelentett tulajdonságai frissítve lettek.

    ![a jelentett tulajdonságok frissítése az eszköz alkalmazásból](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Most, hogy az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell. Lépjen vissza, és futtassa újra a lekérdezéseket:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ekkor a **{Device ID}** értéknek mindkét lekérdezési eredményben szerepelnie kell.

    ![a Service app második lekérdezése](./media/iot-hub-python-twin-getstarted/service-2.png)

    Az eszköz alkalmazásban megtekintheti, hogy a szolgáltatásalkalmazás által küldött kívánt tulajdonságok közül a szolgáltatás érkezett-e.

    ![a kívánt tulajdonságok fogadása az eszköz alkalmazásában](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat a beállításjegyzék használatával.

Az alábbi források segítségével megismerheti a következőket:

* Telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-python.md) oktatóanyaggal.

* Konfigurálja az eszközöket a Device Twin által kívánt tulajdonságokkal, és [használja a kívánt tulajdonságokat az eszközök konfigurálása](tutorial-device-twins.md) oktatóanyaghoz.

* Vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból való bekapcsolással), a [Direct Methods oktatóanyag használatával](quickstart-control-device-python.md) .
