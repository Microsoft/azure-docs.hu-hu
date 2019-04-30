---
title: Első lépések az ikereszközökhöz Azure IoT Hub (Python) |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub device twins címkéket adhat hozzá, majd az IoT Hub-lekérdezést. Az Azure IoT SDK Pythonhoz készült használatával valósítható meg a szimulált eszközalkalmazás és a egy szolgáltatás-alkalmazást, amely hozzáadja a címkék és az IoT Hub-lekérdezést.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 3e703c999d57cf62064291cf91059a17a959a2c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442633"
---
# <a name="get-started-with-device-twins-python"></a>Első lépések az ikereszközökhöz (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén két Python-konzolalkalmazással fog rendelkezni:

* **AddTagsAndQuery.py**, egy Python-háttér-alkalmazást, amely címkét ad hozzá, és lekérdezi az ikereszközök.

* **ReportConnectivity.py**, Python-alkalmazás, amely szimulálja a olyan eszköz, amely az IoT hubhoz a korábban létrehozott eszközidentitással, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k](iot-hub-devguide-sdks.md) használható eszköz és a háttér-alkalmazásokat hozhat létre az Azure IoT SDK-kkal kapcsolatos információkat biztosít.

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).

* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra](https://www.microsoft.com/download/confirmation.aspx?id=48145) van szükség a Python natív DLL-jei használatához.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

> [!NOTE]
> `azure-iothub-service-client` és `azure-iothub-device-client` rendszerhez a *pip*-csomagok jelenleg csak Windows operációs rendszer alatt érhetőek el. A Linux/Mac OS, tekintse meg a Linux és Mac OS-specifikus szakaszokat a a [a fejlesztési környezet előkészítését a Pythonhoz készült](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) közzététele.
>

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Az alkalmazás létrehozása

Ebben a szakaszban hoz létre egy Python-Konzolalkalmazás, amely hozzáadja az ikereszköz társított metaadatok helye a **{Device ID}**. Ezután lekérdezi az ikereszközök tárolja az IoT hub kiválasztása az eszközök, Redmond, és amelyekre a mobilhálózati kapcsolat jelent.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK-t**. Az SDK telepítése után zárja be a parancssort.

   ```
   pip install azure-iothub-service-client
   ```

2. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery.py** fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adja hozzá a következő kódot, lecserélve a helyőrző `[IoTHub Connection String]` és `[Device Id]` az IoT hub és az eszköz azonosítója, a korábbi szakaszokban létrehozott kapcsolati karakterláncára.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Adja hozzá a következő kódot a **AddTagsAndQuery.py** fájlt:

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    A **beállításjegyzék** vezérlőnek az ikereszközökhöz, a szolgáltatás használatához szükséges összes módszert. A kód először inicializálja a **beállításjegyzék** objektumot, majd az eszközök ikereszköze a frissítések **deviceId**, és végül a két lekérdezést futtat. Az első kiválasztja a csak az ikereszközök található eszközök a **Redmond43** gépek és a második megjeleníthető a lekérdezést, válassza ki a keresztül mobilhálózati is csatlakozó eszközöket.

6. Adja hozzá a következő kódot végén **AddTagsAndQuery.py** megvalósításához a **iothub_service_sample_run** függvény:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Futtassa az alkalmazást:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Megjelenik a lekérdezés feltevéséhez az eredmények között egy eszközön található összes eszköz **Redmond43** sem a lekérdezést, amely korlátozza az eredményeket a mobilhálózati használó eszközöket.

    ![első lekérdezés Redmond jeleníti meg az összes eszközt](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

A következő szakaszban, hogy egy eszközalkalmazás létrehozása, amely jelent a kapcsolati adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban hoz létre egy Python-Konzolalkalmazás, amely csatlakozik a hubhoz, mint a **{Device ID}**, és majd a frissítések az ikereszköz a jelentett tulajdonságok alapján, hogy csatlakoztatva van mobilhálózat használata adatokat tartalmazzák.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK-t**. Az SDK telepítése után zárja be a parancssort.

    ```
    pip install azure-iothub-device-client
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.py** fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Adja hozzá a következő kódot, lecserélve a helyőrző `[IoTHub Device Connection String]` a korábbi szakaszokban létrehozott IoT hub-eszköz kapcsolati karakterláncára.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Adja hozzá a következő kódot a **ReportConnectivity.py** fájlt, végrehajtására az eszköz twins funkciókat:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    A **ügyfél** vezérlőnek az ikereszközökhöz az eszközről való kommunikációhoz szükséges összes módszert. Az előző kód után inicializálja a **ügyfél** objektumot, az eszköz az ikereszköz beolvasása és frissíti a jelentett tulajdonság a kapcsolati adatokkal.

6. Adja hozzá a következő kódot végén **ReportConnectivity.py** megvalósításához a **iothub_client_sample_run** függvény:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Az eszközalkalmazás futtatása:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Az ikereszközök frissített megerősítő kell megjelennie.

    ![a párok frissítése](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Most, hogy az eszköz jelenik meg a kapcsolati információkat, akkor meg kell jelennie mindkét lekérdezést. Lépjen vissza, és futtassa újból a lekérdezést:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ezúttal a **{Device ID}** meg kell jelennie mindkét lekérdezés eredményeit.

    ![második lekérdezés](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Címkeként, egy háttér-alkalmazásból hozzáadott eszközök metaadatait, és a egy szimulált eszközalkalmazás zapsáno do kapcsolat eszközadatokat a jelentés azokat az ikereszköz. Azt is megtanulta, hogyan kérdezhet le ezt az információt a beállításjegyzék használatával.

Az alábbi forrásanyagokból megtudhatja, hogyan lehet:

* Telemetriát az eszközökről a [IoT Hub használatának első lépései](quickstart-send-telemetry-python.md) oktatóanyag.

* Konfigurálhatja az eszközöket használó eszköz ikereszköz kívánt tulajdonságait a a [használata kívánt tulajdonságok konfigurálhatja az eszközöket](tutorial-device-twins.md) oktatóanyag.

* Az eszközök, interaktív módon (például bekapcsolása egy felhasználó által felügyelt alkalmazásból ventilátor), szabályozhatja a [közvetlen metódusok használata](quickstart-control-device-python.md) oktatóanyag.