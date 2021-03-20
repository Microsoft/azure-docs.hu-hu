---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (Python) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről a Pythonhoz készült Azure IoT SDK-k használatával. Módosít egy szimulált eszközt a felhőből az eszközre irányuló üzenetek fogadására és a felhőből az eszközre irányuló üzenetek küldésére szolgáló háttérbeli alkalmazás módosítására.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87876801"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Üzenetküldés a felhőből az eszközre IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-python.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-python.md)épít. A következőket mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

A felhőből az eszközre irányuló üzenetekről a [IoT hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Python-konzolos alkalmazást fog futtatni:

* A **SimulatedDevice.py** az alkalmazás egy olyan módosított verziója, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-python.md), amely csatlakozik a IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* **SendCloudToDeviceMessage.py**, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak a IoT hubon keresztül.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban egy Python-konzol alkalmazást hoz létre, amely szimulálja az eszközt, és fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. A munkakönyvtárban található parancssorból telepítse a **Pythonhoz készült Azure IoT hub Device SDK**-t:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.py** nevű fájlt.

1. Adja hozzá a következő `import` utasításokat és változókat a **SimulatedDevice.py** fájl elejéhez:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Adja hozzá a következő kódot a **SimulatedDevice.py** fájlhoz. A helyőrző értékét cserélje le a telemetria eszközről `{deviceConnectionString}` [egy IoT hub-re való küldéséhez](quickstart-send-telemetry-python.md) létrehozott eszközhöz tartozó eszköz-kapcsolódási karakterláncra:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adja hozzá a következő függvényt a fogadott üzenetek a konzolra való nyomtatásához:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Adja hozzá a következő kódot az ügyfél inicializálásához, és várja meg a felhőből az eszközre irányuló üzenet fogadását:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Adja hozzá a következő fő függvényt:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Mentse és zárja be a **SimulatedDevice.py** fájlt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése eszközről egy IoT hubhoz](quickstart-send-telemetry-python.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak. Szüksége lesz annak az eszköznek az eszköz-AZONOSÍTÓJÁRA, amelyet a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-python.md) -gyors üzembe helyezéshez adott meg. Szüksége lesz a korábban átmásolt IoT hub-kapcsolatok karakterláncára is a [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében.

1. Nyisson meg egy parancssort a munkakönyvtárában, és telepítse a **Pythonhoz készült Azure IoT hub Service SDK**-t.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage.py** nevű fájlt.

1. Adja hozzá a következő `import` utasításokat és változókat a **SendCloudToDeviceMessage.py** fájl elejéhez:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.py** fájlhoz. Cserélje le a `{iot hub connection string}` és a `{device id}` helyőrző értékeket az IoT hub-beli kapcsolatok karakterláncára és a korábban feljegyzett eszköz azonosítóra:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adja hozzá a következő kódot, amely üzeneteket küld az eszköznek:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Adja hozzá a következő fő függvényt:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Mentse és zárjuk be a **SendCloudToDeviceMessage.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A munkakönyvtárában a parancssorban futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```shell
    python SimulatedDevice.py
    ```

    ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-python-python-c2d/device-1.png)

1. Nyisson meg egy új parancssort a munkakönyvtárában, és futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek küldéséhez:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldéséhez.](./media/iot-hub-python-python-c2d/service.png)

1. Jegyezze fel az eszköz által fogadott üzeneteket.

    ![Üzenet érkezett](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://azure.microsoft.com/documentation/suites/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
