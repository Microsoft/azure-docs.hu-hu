---
title: Azure IoT Hub modul-identitás és-modul twin (Python)
description: Megtudhatja, hogyan hozhat létre modul-identitást és-frissítési modult a IoT SDK-k használatával a Pythonhoz.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139392"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Ismerkedés a IoT Hub modul identitásával és moduljával (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> A [modul identitásai és az ikrek modul](iot-hub-devguide-module-twins.md) hasonló az Azure IoT hub-eszközök identitásához és az eszközök ikrekhez, de finomabb részletességet biztosít. Habár az Azure IoT Hub eszköz-identitások és az eszközök ikrek lehetővé teszik egy háttérbeli alkalmazás számára az eszköz konfigurálását, valamint az eszköz feltételeinek, a modul-identitások és a modul-összetételek láthatóságának biztosítását az eszközök egyes összetevői számára. A több összetevővel (például operációs rendszer alapú eszközökkel vagy belső vezérlőprogram-eszközökkel) rendelkező kompatibilis eszközökön az egyes összetevők elkülönített konfigurációját és feltételeit teszik lehetővé.
>

Az oktatóanyag végén három Python-alkalmazás található:

* **CreateModule**, amely létrehoz egy eszköz identitását, egy modul identitását és a társított biztonsági kulcsokat az eszköz és a modul ügyfeleinek csatlakoztatásához.

* **UpdateModuleTwinDesiredProperties**, amely a frissített modult IoT hub kívánt tulajdonságokat küldi el.

* **ReceiveModuleTwinDesiredPropertiesPatch**, amely a modulhoz tartozó, a kívánt tulajdonságok javítását fogadja az eszközön.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy olyan háttér-szolgáltatást hoz létre, amely hozzáadja az eszközt az identitás-beállításjegyzékben, majd hozzáadja a modult az eszközhöz. Ennek a szolgáltatásnak szüksége van a **beállításjegyzék írási** engedélyére (amely magában foglalja a **beállításjegyzék olvasását** is). Olyan szolgáltatást is létrehozhat, amely hozzáadja a kívánt tulajdonságokat az újonnan létrehozott modulhoz tartozó modulhoz. A szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére. Habár vannak olyan alapértelmezett megosztott hozzáférési szabályzatok, amelyek egyenként adják meg ezeket az engedélyeket, ebben a szakaszban egy egyéni megosztott elérési házirendet hoz létre, amely mindkét engedélyt tartalmazza.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Eszköz identitásának és modul-identitásának létrehozása IoT Hub

Ebben a szakaszban létrehoz egy Python szolgáltatásalkalmazás-alkalmazást, amely létrehoz egy eszköz-identitást és egy modul-identitást az IoT hub identitás-beállításjegyzékében. Egy eszköz vagy modul nem tud csatlakozni az IoT hubhoz, hacsak nem rendelkezik bejegyzéssel az Identity registryben. További információ: [az Identity Registry megismerése az IoT hub-ban](iot-hub-devguide-identity-registry.md). A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. A parancssorban futtassa a következő parancsot a **msrest** csomag telepítéséhez. Erre a csomagra szüksége lesz a **HTTPOperationError** -kivételek elfogásához.

    ```cmd/sh
    pip install msrest
    ```

1. Egy szövegszerkesztővel hozzon létre egy **CreateModule.py** nevű fájlt a munkakönyvtárában.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le az *YourIoTHubConnectionString* -t az [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében lemásolt kapcsolatok karakterláncra.

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. A parancssorban futtassa a következő parancsot:

    ```cmd/sh
    python CreateModule.py
    ```

Ez az alkalmazás létrehoz egy **MYFIRSTDEVICE** azonosítóval és egy **myFirstModule** azonosítójú modul-identitással az eszköz **myFirstDevice** alatt. (Ha az eszköz vagy a modul azonosítója már létezik az Identity registryben, a kód egyszerűen lekéri a meglévő eszköz-vagy modul-információkat.) Az alkalmazás megjeleníti az egyes identitások AZONOSÍTÓját és elsődleges kulcsát.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [az Identity Registry megismerése az IoT hub-ban](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>A Twin modul frissítése a Python Service SDK használatával

Ebben a szakaszban egy olyan Python szolgáltatásalkalmazás-alkalmazást hoz létre, amely frissíti a modul a kívánt tulajdonságokat.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-hub** csomag telepítéséhez. Ezt a lépést kihagyhatja, ha az előző szakaszban telepítette az **Azure-IOT-hub** csomagot.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Egy szövegszerkesztővel hozzon létre egy **UpdateModuleTwinDesiredProperties.py** nevű fájlt a munkakönyvtárában.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le az *YourIoTHubConnectionString* -t az [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében lemásolt kapcsolatok karakterláncra.

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Frissítések letöltése az eszköz oldaláról

Ebben a szakaszban egy Python-alkalmazást hoz létre, amely a modulhoz tartozó Twin kívánt tulajdonságok frissítését kéri az eszközön.

1. A modulhoz tartozó kapcsolatok karakterláncának beolvasása. A [Azure Portalban](https://portal.azure.com/)navigáljon a IoT hub, és válassza a **IoT eszközök** elemet a bal oldali ablaktáblán. Válassza ki a **myFirstDevice** az eszközök listájából, és nyissa meg. A **modul-identitások** területen válassza a **myFirstModule** lehetőséget. Másolja ki a modul kapcsolati sztringjét. Ezt a következő lépésben kell megadnia.

   ![Az Azure Portal moduladatai](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Egy szövegszerkesztővel hozzon létre egy **ReceiveModuleTwinDesiredPropertiesPatch.py** nevű fájlt a munkakönyvtárában.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le a *YourModuleConnectionString* az 1. lépésben átmásolt modul-összekapcsolási sztringre.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Ebben a szakaszban a **ReceiveModuleTwinDesiredPropertiesPatch** -eszköz alkalmazást futtatja, majd futtatja a **UpdateModuleTwinDesiredProperties** szolgáltatás alkalmazást a modul kívánt tulajdonságainak frissítéséhez.

1. Nyisson meg egy parancssort, és futtassa az eszköz alkalmazást:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Eszköz-alkalmazás kezdeti kimenete](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Nyisson meg egy külön parancssort, és futtassa a szolgáltatás alkalmazást:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Figyelje meg, hogy a **TelemetryInterval** kívánt tulajdonság megjelenik a Service app-kimenetben a frissített modulban:

   ![Szolgáltatás-alkalmazás kimenete](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Ugyanez a tulajdonság jelenik meg az eszköz alkalmazás-kimenetében kapott kívánt tulajdonságok javításban:

   ![Az eszköz alkalmazás kimenete a kívánt tulajdonságok javítását mutatja](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Következő lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)