---
title: Ismerkedés az Azure IoT Hub identitás- és a modul (Python) ikermodulja |} A Microsoft Docs
description: Ismerje meg, hogyan modul identitás létrehozása és frissítése az ikermodul a Pythonhoz készült IoT SDK-k használatával.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: bf2137803c7a6c70f9efe75524a4ed5cf51d6ca1
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451442"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Ismerkedés az IoT Hub identitás- és modul ikermodul Python-háttérrendszer és a Python-eszköz használata

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Amíg az Azure IoT Hub eszközidentitása és eszközikre lehetővé teszi a háttéralkalmazás számára az eszköz konfigurálását, és rálátást nyújt az eszköz feltételeire, a modulidentitás és a moduliker az eszköz egyes összetevőihez biztosítja ezeket a lehetőségeket. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.
>

Ez az oktatóanyag végén kettő Python-alkalmazással rendelkezik:

* A **CreateIdentities** egy eszközidentitást, egy modulidentitást valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközhöz és a modulügyfelekhez.
* Az **UpdateModuleTwinReportedProperties** a moduliker jelentett tulajdonságainak frissítését továbbítja az IoT Hub részére.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
>

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)
* An IoT Hub.
* Telepítse a legújabb [Python SDK](https://github.com/Azure/azure-iot-sdk-python).

Létrehozta az IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati sztringgel.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Az IoT Hub eszközidentitás és a egy modul identitás létrehozása

Ebben a szakaszban hozzon létre egy Python-alkalmazás által létrehozott egy eszközidentitást, valamint egy modul identitás az IoT hub identitásjegyzékében. Egy eszköz vagy modul csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] „Identitásjegyzék” című szakaszát. A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

A Python-fájlt adja hozzá a következő kódot:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Ez az alkalmazás egy új eszközidentitást hoz létre azonosító **myFirstDevice** és a egy modul identitás azonosítójú **myFirstModule** az eszköz **myFirstDevice**. (Ha ez a modulazonosító már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő modulinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált modulalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információkért lásd az [Azure IoT Hub fejlesztői útmutatóját][lnk-devguide-identity].
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Frissítse a Python eszközoldali SDK-val ikermodul

Ebben a szakaszban hoz létre egy Python-alkalmazás a szimulált eszköz, amely frissíti az ikermodul jelentett tulajdonságokként.

1. **Szerezze be a modul kapcsolati sztringjét** – ezt most megteheti, ha bejelentkezik az [Azure Portalra][lnk-portal]. Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresés myFirstDevice, nyissa meg azt, és tekintse meg a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

  ![Az Azure Portal moduladatai][15]

1. **UpdateModuleTwinReportedProperties alkalmazás létrehozása** adja hozzá a következő `using` tetején található utasításokat a **Program.cs** fájlt:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. 

## <a name="get-updates-on-the-device-side"></a>Az eszközoldalon frissítések beszerzése

Mellett a fenti kóddal, az alábbi kódblokkot, amellyel az ikereszköz frissítésének letöltése hozzáadhat üzenet az eszközön.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```


## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések][lnk-device-management]
* [Ismerkedés az IoT Edge szolgáltatással][lnk-iot-edge]


<!-- Images. -->
[15]:./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
