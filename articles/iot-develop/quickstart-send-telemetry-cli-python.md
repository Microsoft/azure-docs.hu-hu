---
title: Eszköz telemetria küldése az Azure IoT Hub rövid útmutatóba (Python)
description: Ebben a rövid útmutatóban a Pythonhoz készült Azure IoT Hub Device SDK használatával küld telemetria az eszközről egy IOT hubhoz.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: ccf7f272069516d53732b10ec1af5bb4841f1208
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730422"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Gyors útmutató: telemetria küldése az eszközről egy Azure IoT hub-ra (Python)

**A** következőkre vonatkozik: [eszköz-alkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban egy alapszintű IoT-fejlesztési munkafolyamatot sajátíthat el. Az Azure CLI-vel létrehozhat egy Azure IoT hub-t és egy eszközt, majd az Azure IoT Python SDK-val létrehoz egy szimulált eszközt, és elküldheti a telemetria a hubhoz. 

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben futtatott interaktív CLI-rendszerhéj Azure Cloud Shellával. Ha a Cloud Shell használja, semmit nem kell telepítenie. Ha a parancssori felület helyi használatát szeretné használni, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Python 3.7 +](https://www.python.org/downloads/). A Python más verziói esetében lásd: az [Azure IoT eszköz funkciói](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    A Python verziójának naprakészen tartása érdekében futtassa a parancsot `python --version` . Ha a Python 2 és a Python 3 is telepítve van, és Python 3 környezetet használ, telepítse az összes tárat a használatával `pip3` . Ez biztosítja, hogy a kódtárak a Python 3 futtatókörnyezetre legyenek telepítve.
    > [!IMPORTANT]
    > A Python-telepítőben válassza a Python elérési **útvonalhoz való hozzáadásának** lehetőségét. Ha már telepítette a Python 3,7-es vagy újabb verzióját, győződjön meg róla, hogy a Python telepítési mappáját hozzáadta a `PATH` környezeti változóhoz.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Üzenetek küldése a Python SDK használatával
Ebben a szakaszban a Python SDK-t fogja használni a szimulált eszközről az IoT hub-ba való üzenetküldéshez.

1. Nyisson meg egy új terminálablakot. Ezt a terminált fogja használni a Python SDK telepítéséhez és a Python-mintakód használatához. Ekkor két terminált kell megnyitnia: az imént megnyitotta a Python használatát, az előző részekben használt CLI-rendszerhéj pedig az Azure CLI-parancsok megadásához.       

1. Másolja az [Azure IoT PYTHON SDK-eszközök mintáit](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    és navigáljon az *Azure-IOT-SDK-Python/Azure-IOT-Device/Samples* könyvtárba:

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Telepítse az Azure IoT Python SDK-t:

    ```console
    pip install azure-iot-device
    ```
1. Állítsa be az eszközhöz tartozó kapcsolatok karakterláncát a nevű környezeti változóként `IOTHUB_DEVICE_CONNECTION_STRING` . Ez az a karakterlánc, amelyet az előző szakaszban kapott a szimulált Python-eszköz létrehozása után.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > A Windows CMD esetében nincsenek a kapcsolatok sztringjét körülvevő idézőjelek.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. A megnyitott CLI-rendszerhéjban futtassa az az [IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub?preserve-view=true&view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) parancsot a szimulált IOT-eszközön lévő események figyelésének megkezdéséhez.  Az események a terminálon lesznek kinyomtatva, ahogy érkeznek.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. A Python-terminálban futtassa a *simple_send_message.* a telepített minta fájl kódját. Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT hubhoz.

    A Python-minta futtatása a terminálról:
    ```console
    python ./simple_send_message.py
    ```

    A Python-kódot a Python IDE-ből is futtathatja a minta használatával:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Mivel a Python-kód üzenetet küld az eszközről az IoT hubhoz, az üzenet a CLI-rendszerhéjban jelenik meg, amely figyeli az eseményeket:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Az eszköz mostantól biztonságosan csatlakozik, és telemetria küld az Azure IoT Hubnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat.

Erőforráscsoport törlése név alapján:
1. Futtassa az az [Group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) parancsot. Ezzel eltávolítja az erőforráscsoportot, a IoT Hub és a létrehozott eszköz regisztrációját.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa az az [Group List](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-list) parancsot az erőforráscsoport törlésének megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás munkafolyamatát ismertette az eszköz felhőbe való biztonságos csatlakoztatásához és az eszközről a felhőbe irányuló telemetria küldéséhez. Az Azure CLI-vel létrehozott egy IoT hubot és egy eszközt, majd az Azure IoT Python SDK-val létrehozta a szimulált eszközt, és elküldheti a telemetria a hubhoz. 

A következő lépésként Fedezze fel az Azure IoT Python SDK-t az Application Samples használatával.

- [Aszinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Ez a könyvtár aszinkron Python-mintákat tartalmaz a további IoT hub forgatókönyvekhez.
- [Szinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Ez a könyvtár a Python 2,7-es vagy a Python 3.5 +-hez készült szinkron kompatibilitási forgatókönyvekkel használható Python-mintákat tartalmaz.
- [IoT Edge minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Ez a könyvtár Python-mintákat tartalmaz az Edge-modulok és az alsóbb rétegbeli eszközök használatához.