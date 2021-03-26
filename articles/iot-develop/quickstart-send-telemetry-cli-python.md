---
title: Eszköz telemetria küldése az Azure IoT Hub rövid útmutatóba (Python)
description: Ebben a rövid útmutatóban a Pythonhoz készült Azure IoT Hub Device SDK használatával küld telemetria az eszközről egy IOT hubhoz.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: f28ad8f93769bc95c87095a545f608827c319dd3
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106819"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Gyors útmutató: telemetria küldése az eszközről egy Azure IoT hub-ra (Python)

**A** következőkre vonatkozik: [eszköz-alkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban egy alapszintű IoT-fejlesztési munkafolyamatot sajátíthat el. Az Azure CLI-vel létrehozhat egy Azure IoT hub-t és egy eszközt, majd az Azure IoT Python SDK-val létrehoz egy szimulált eszközt, és elküldheti a telemetria a hubhoz. 

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben futtatott interaktív CLI-rendszerhéj Azure Cloud Shellával. Ha a Cloud Shell használja, semmit nem kell telepítenie. Ha a parancssori felület helyi használatát szeretné használni, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Python 3.7 +](https://www.python.org/downloads/). A Python más verziói esetében lásd: az [Azure IoT eszköz funkciói](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    A Python verziójának naprakészen tartása érdekében futtassa a parancsot `python --version` . Ha a Python 2 és a Python 3 is telepítve van, és Python 3 környezetet használ, telepítse az összes tárat a használatával `pip3` . Ez a parancs biztosítja, hogy a kódtárak a Python 3 futtatókörnyezetre legyenek telepítve.
    > [!IMPORTANT]
    > A Python-telepítőben válassza a Python elérési **útvonalhoz való hozzáadásának** lehetőségét. Ha már telepítette a Python 3,7-es vagy újabb verzióját, győződjön meg róla, hogy a Python telepítési mappáját hozzáadta a `PATH` környezeti változóhoz.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Üzenetek küldése a Python SDK használatával
Ebben a szakaszban a Python SDK-t fogja használni a szimulált eszközről az IoT hub-ba való üzenetküldéshez.

1. Nyisson meg egy új terminálablakot. Ezt a terminált fogja használni a Python SDK telepítéséhez és a Python-mintakód használatához. Ekkor két terminált kell megnyitnia: az imént megnyitotta a Python használatát, az előző részekben használt CLI-rendszerhéj pedig Azure CLI-parancsokat adhat meg.       

1. Másolja az [Azure IoT PYTHON SDK-eszközök mintáit](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Navigáljon az *Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* könyvtárba:

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Telepítse az Azure IoT Python SDK-t:

    ```console
    pip install azure-iot-device
    ```
1. Állítsa be mindkét környezeti változót, hogy a szimulált eszköz az Azure IoT kapcsolódjon.
    * Állítson be egy nevű környezeti változót `IOTHUB_DEVICE_CONNECTION_STRING` . A változó értékeként használja az előző szakaszban mentett eszköz-kapcsolatok karakterláncát.
    * Állítson be egy nevű környezeti változót `IOTHUB_DEVICE_SECURITY_TYPE` . A változó esetében használja a literális karakterlánc értéket `connectionString` .

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > A Windows CMD esetében nincsenek idézőjelek az egyes változókhoz tartozó sztringek körül.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux vagy Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. A megnyitott CLI-rendszerhéjban futtassa az az [IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) parancsot a szimulált IOT-eszközön lévő események figyelésének megkezdéséhez.  Az események a terminálon érkeznek.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. A Python-terminálban futtassa a *simple_thermostat.* a telepített minta fájl kódját. Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT hubhoz.

    A Python-minta futtatása a terminálról:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Ez a mintakód az Azure IoT Plug and Play használja, amely lehetővé teszi, hogy manuális konfiguráció nélkül integrálja az intelligens eszközöket a megoldásaiba.  Alapértelmezés szerint a dokumentációban szereplő legtöbb minta a IoT Plug and Play használja. Ha többet szeretne megtudni a IoT PnP előnyeiről, illetve a használatának vagy használatának mellőzéséről, olvassa el a [Mi az a IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 Mivel a Python-kód üzenetet küld az eszközről az IoT hubhoz, az üzenet a CLI-rendszerhéjban jelenik meg, amely figyeli az eseményeket:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

Az eszköz mostantól biztonságosan csatlakozik, és telemetria küld az Azure IoT Hubnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat.

Erőforráscsoport törlése név alapján:
1. Futtassa az az [Group delete](/cli/azure/group#az-group-delete) parancsot. Ezzel a paranccsal eltávolítható az erőforráscsoport, a IoT Hub és a létrehozott eszköz regisztrálása.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa az az [Group List](/cli/azure/group#az-group-list) parancsot az erőforráscsoport törlésének megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás munkafolyamatát ismertette az eszköz felhőbe való biztonságos csatlakoztatásához és az eszközről a felhőbe irányuló telemetria küldéséhez. Az Azure CLI-vel létrehozott egy IoT hubot és egy eszközt, majd az Azure IoT Python SDK-val létrehozta a szimulált eszközt, és elküldheti a telemetria a hubhoz. 

A következő lépésként Fedezze fel az Azure IoT Python SDK-t az Application Samples használatával.

- [Aszinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Ez a könyvtár aszinkron Python-mintákat tartalmaz a további IoT hub forgatókönyvekhez.
- [Szinkron minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Ez a könyvtár a Python 2,7-es vagy a Python 3.6 +-hoz készült szinkron kompatibilitási forgatókönyvekkel használható Python-mintákat tartalmaz.
- [IoT Edge minták](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Ez a könyvtár Python-mintákat tartalmaz az Edge-modulok és az alsóbb rétegbeli eszközök használatához.
