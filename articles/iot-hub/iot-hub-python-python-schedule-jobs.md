---
title: Feladatok ütemezhetnek az Azure IoT Hub (Python) segítségével | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beütemezés közvetlen metódus több eszközön való meghívásához. A Pythonhoz készült Azure IoT SDK-k segítségével implementálhatja a szimulált eszköz alkalmazásait és egy szolgáltatási alkalmazást a feladatok futtatásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876682"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Feladatok ütemezett és szórása (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a háttérbeli alkalmazások számára, hogy több millió eszközt ütemezzen és frissítsen feladatok létrehozásához és nyomon követéséhez.  A feladatokat a következő műveletekhez használhatja:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Elméletileg a feladatok az alábbi műveletek egyikét betakarják, és nyomon követik a végrehajtás előrehaladását egy adott eszközön, amelyet az eszközök kettős lekérdezése határoz meg.  Egy háttérbeli alkalmazás például felhasználhat egy feladatot egy újraindítási módszer meghívására 10 000-eszközökön, egy eszköz kettős lekérdezésével és egy későbbi időpontban ütemezve.  Az alkalmazás ezután nyomon követheti az előrehaladást, mivel az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

További információk a következő cikkekben felsorolt lehetőségekről:

* Eszközök Twin és Properties: Ismerkedés [az eszközök ikrekkel](iot-hub-python-twin-getstarted.md) és [oktatóanyaggal: az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok és [oktatóanyag: közvetlen metódusok](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy olyan Python által szimulált eszköz alkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi a **lockDoor**, amelyet a megoldás hátterében hívhat meg.

* Hozzon létre egy olyan Python-konzol alkalmazást, amely feladatokkal hívja meg a **lockDoor** Direct metódust a szimulált eszköz alkalmazásban, és az eszköz feladataival frissíti a kívánt tulajdonságokat.

Az oktatóanyag végén két Python-alkalmazás áll rendelkezésére:

**simDevice.py**, amely az eszköz identitásával csatlakozik az IoT hubhoz, és egy **lockDoor** Direct metódust kap.

a **scheduleJobService.py**, amely közvetlen metódust hív meg a szimulált eszköz alkalmazásban, és feladatokkal frissíti az eszközhöz tartozó dupla kívánt tulajdonságokat.

> [!NOTE]
> A **Pythonhoz készült Azure IOT SDK** nem támogatja közvetlenül a **feladatok** funkcióit. Ehelyett ez az oktatóanyag egy alternatív megoldást kínál, amely aszinkron szálakat és időzítőket használ. További frissítésekért tekintse meg az [Azure IOT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) -oldal **szolgáltatás ügyféloldali SDK** -szolgáltatásainak listáját.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol, amely egy szimulált **lockDoor** metódust indít el.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **simDevice.py** -fájlt a munkakönyvtárában.

3. Adja hozzá a következő `import` utasításokat és változókat a **simDevice.py** fájl elejéhez. Cserélje le a szöveget a `deviceConnectionString` fent létrehozott eszközhöz tartozó kapcsolatok sztringre:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adja hozzá a következő függvény visszahívást a **lockDoor** metódus kezeléséhez:

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Új függvény visszahívás hozzáadása az eszközök ikrek frissítéseinek kezeléséhez:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Adja hozzá a következő kódot a kezelő **lockDoor** metódushoz való regisztrálásához. Adja meg a `main` rutint is:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Mentse és zárjuk be a **simDevice.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely egy közvetlen metódust hív meg egy eszközön, és frissíti az eszközt. A szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére, hogy közvetlen metódust hívjon az eszközön. A szolgáltatásnak szüksége van a **beállításjegyzék olvasási** és **beállításjegyzék-írási** engedélyeire is az azonosító beállításjegyzékének olvasásához és írásához. Nincs olyan alapértelmezett megosztott hozzáférési szabályzat, amely csak ezeket az engedélyeket tartalmazza, ezért létre kell hoznia egyet.

Ha olyan megosztott hozzáférési szabályzatot szeretne létrehozni, amely a **szolgáltatás kapcsolódását**, a **beállításjegyzék olvasási** és **beállításjegyzék-írási** engedélyeit, valamint a szabályzat kapcsolati karakterláncának lekérését kéri, kövesse az alábbi lépéseket:

1. Nyissa meg az IoT hubot a [Azure Portal](https://portal.azure.com). Az IoT hub kiválasztásának legegyszerűbb módja az **erőforráscsoportok** kiválasztása. Válassza ki azt az erőforráscsoportot, ahol az IoT hub található, majd válassza ki az IoT hubot az erőforrások listájából.

2. Az IoT hub bal oldali ablaktábláján válassza a **megosztott hozzáférési házirendek** elemet.

3. A szabályzatok listájának felső menüjében válassza a **Hozzáadás** lehetőséget.

4. A **megosztott hozzáférési házirend hozzáadása** panelen adjon meg egy leíró nevet a szabályzatnak; például: *serviceAndRegistryReadWrite*. Az **engedélyek** területen válassza a **szolgáltatás kapcsolódása** és a **beállításjegyzék írása** lehetőséget (a **beállításjegyzék olvasása** beállítás automatikusan kiválasztásra kerül a beállításjegyzék **írásakor**). Ezután kattintson a **Létrehozás** elemre.

    ![Új megosztott elérési házirend hozzáadásának megjelenítése](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. A **megosztott hozzáférési szabályzatok** panelen válassza ki az új házirendet a házirendek listájából.

6. A **megosztott elérési kulcsok** területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezhetnek közvetlen metódus hívásához és az eszközök Twin tulajdonságainak frissítéséhez

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely egy közvetlen metódus használatával kezdeményez egy távoli **lockDoor** az eszközön, és frissíti az eszközhöz tartozó dupla kívánt tulajdonságokat is.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.py** -fájlt a munkakönyvtárában.

3. Adja hozzá a következő `import` utasításokat és változókat a **scheduleJobService.py** fájl elejéhez. Cserélje le a `{IoTHubConnectionString}` helyőrzőt arra a IoT hub-IoT, amelyet korábban átmásolt a [beolvasás az hub-kapcsolatok karakterláncában](#get-the-iot-hub-connection-string). Cserélje le a `{deviceId}` helyőrzőt az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban regisztrált eszköz azonosítójával:

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Adja hozzá a következő függvényt, amely az eszközök lekérdezésére szolgál:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Adja hozzá a következő metódusokat a közvetlen metódust és az eszköz ikerét meghívó feladatok futtatásához:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Adja hozzá a következő kódot a feladatok ütemezett és frissítési feladat állapotának megadásához. Adja meg a `main` rutint is:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Mentse és zárjuk be a **scheduleJobService.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A munkakönyvtárban a parancssorban futtassa a következő parancsot a közvetlen újraindítási módszer figyelésének megkezdéséhez:

    ```cmd/sh
    python simDevice.py
    ```

2. A munkakönyvtárban egy másik parancssorban futtassa a következő parancsot a feladatok elindításához az ajtó zárolásához és a Twin frissítéséhez:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Megjelenik az eszköz válaszai a Direct metódusra és az eszközök ikrek frissítésére a konzolon.

    ![IoT Hub Job minta 1 – eszköz kimenete](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job minta 2 – eszköz kimenete](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Ha továbbra is szeretné megkezdeni a IoT Hub és az eszközkezelés mintáit, például a távoli belső vezérlőprogram frissítését, tekintse meg a [belső vezérlőprogram frissítését](tutorial-firmware-update.md)ismertető témakört.
