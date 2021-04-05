---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510588"
---
A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy lehetővé teszi az eszköz modelljének használatát az alapul szolgáló eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan használható a Python a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

A rövid útmutató elvégzéséhez a fejlesztői gépen a Python 3,7 szükséges. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

Az **Azure-IOT-Device** csomag pip-ként van közzétéve.

A helyi Python-környezetben a következő módon telepítse a csomagot:

```cmd/sh
pip install azure-iot-device
```

Telepítse az **Azure-IOT-hub** csomagot a következő parancs futtatásával:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)információit.

Ebben a rövid útmutatóban egy Pythonban írt minta termosztát-eszközt használ a IoT Plug and Play eszközként. A minta eszköz futtatása:

1. Nyisson meg egy terminál-ablakot tetszőleges mappában. Futtassa a következő parancsot az [Azure IoT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub-tárház ezen a helyen történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a */Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* mappára.

1. Futtassa a minta termosztátos eszközt a következő paranccsal:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy minta IoT megoldást használunk a Pythonban, hogy együttműködjön az imént beállított eszközzel.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra.

1. Navigáljon a klónozott Python SDK-tárház */Azure-IOT-SDK-Python/Azure-IOT-hub/Samples* mappájához.

1. Nyissa meg a *registry_manager_pnp_sample.* rajzfájl fájlt, és tekintse át a kódot. Ez a minta bemutatja, hogyan használhatja a **IoTHubRegistryManager** osztályt a IoT Plug and Play eszközével való interakcióhoz.

> [!NOTE]
> Ezek a szolgáltatási minták a **IoTHubRegistryManager** osztályt használják a **IoT hub szolgáltatás ügyfelétől**. Ha többet szeretne megtudni az API-król, beleértve a digitális Twins API-t, tekintse meg a [szolgáltatás fejlesztői útmutatóját](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Az eszköz dupla letöltése

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](../articles/iot-pnp/set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Használja a következő parancsot a **szolgáltatás** -terminálon a minta futtatásához:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Ha Linux rendszeren futtatja ezt a mintát, használja a (z `export` `set` ) helyett.

A kimenet megjeleníti az eszköz Twin és kiírja a modell AZONOSÍTÓját:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

A következő kódrészlet a *registry_manager_pnp_sample.* a (z):

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Eszköz kettős frissítése

Ez a minta bemutatja, hogyan frissítheti az `targetTemperature` írható tulajdonságot az eszközön:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Ellenőrizze, hogy a frissítés az **eszköz** -terminálon van-e alkalmazva, amely a következő kimenetet jeleníti meg:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

A **szolgáltatás** -terminál ellenőrzi, hogy a javítás sikeres volt-e:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Parancs meghívása

A minta ezután meghívja a parancsot:

A **szolgáltatás** -terminál egy megerősítő üzenetet jelenít meg az eszközről:

```cmd/sh
The device method has been successfully invoked
```

Az **eszköz** -terminálon láthatja, hogy az eszköz megkapja a következő parancsot:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
