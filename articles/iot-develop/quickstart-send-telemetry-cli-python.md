---
title: Eszköz-telemetria küldése Azure IoT Hub rövid útmutatóba (Python)
description: Ebben a rövid útmutatóban az Azure IoT Hub Device SDK for Python használatával telemetriát küld egy eszközről egy Iot Hubra.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: be44ecf6dd154a21fd024ee37d92f61504350664
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876428"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Rövid útmutató: Telemetria küldése eszközről Azure IoT Hubra (Python)

**A következőkre** [vonatkozik: Eszközalkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban megismer egy alapszintű IoT-eszközalkalmazás-fejlesztési munkafolyamatot. Az Azure CLI használatával létrehoz egy Azure IoT Hubot és egy eszközt, majd az Azure IoT Python SDK-val létrehoz egy szimulált ügyféleszközt, és telemetriát küld a hubnak. 

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a kezdés előtt hozzon létre egyet ingyenesen.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben Azure Cloud Shell interaktív CLI-felület használatával. Ha a Cloud Shell, semmit sem kell telepítenie. Ha inkább helyileg szeretné használni a CLI-t, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). A Python egyéb támogatott verzióiért lásd: [Az Azure IoT-eszköz funkciói.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)
    
    Annak érdekében, hogy a Python-verzió naprakész legyen, futtassa a(on) `python --version` et. Ha a Python 2 és a Python 3 is telepítve van, és Python 3 környezetet használ, telepítse az összes kódtárat a `pip3` használatával. Ez a parancs biztosítja, hogy a kódtárak telepítve vannak a Python 3-as futásidejű környezetben.
    > [!IMPORTANT]
    > A Python-telepítőben válassza a **Python hozzáadása a PATH-hez lehetőséget.** Ha a Python 3.7-es vagy újabb verzió már telepítve van, ellenőrizze, hogy hozzáadta-e a Python telepítési mappáját a környezeti `PATH` változóhoz.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Üzenetek küldése a Python SDK használatával
Ebben a szakaszban a Python SDK használatával fog üzeneteket küldeni a szimulált eszközről az IoT Hubnak.

1. Nyisson meg egy új terminálablakot. Ezt a terminált fogja használni a Python SDK telepítéséhez és a Python-mintakód használatához. Most már két terminálnak kell nyitva lennie: a Python használatához megnyitott terminállal és az előző szakaszokban az Azure CLI-parancsok beíratásához használt CLI-felületel.       

1. Másolja az [Azure IoT Python SDK-eszközmintákat](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Lépjen az *azure-iot-sdk-python/azure-iot-device/samples/pnp könyvtárba:*

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Telepítse az Azure IoT Python SDK-t:

    ```console
    pip install azure-iot-device
    ```
1. Állítsa be az alábbi környezeti változókat, hogy a szimulált eszköz csatlakozik az Azure IoT-hez.
    * Állítson be egy nevű környezeti `IOTHUB_DEVICE_CONNECTION_STRING` változót. A változó értékeként használja az előző szakaszban mentett eszközkapcsolati sztringet.
    * Állítson be egy nevű környezeti `IOTHUB_DEVICE_SECURITY_TYPE` változót. A változóhoz használja a literális sztring `connectionString` értéket.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > A Windows CMD esetében nincsenek idézőjelek az egyes változók sztringértékei körül.

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

1. Futtassa az [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) parancsot a nyílt CLI-rendszerhéjban a szimulált IoT-eszközön futó események monitorozásának megkezdéséhez.  Az eseményüzenetek érkezésükkor gombra fognak érkezni a terminálon.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. A Python-terminálban futtassa a telepített mintafájl kódját *simple_thermostat.py fájllal.* Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT Hubnak.

    A Python-minta futtatása a terminálból:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > Ez a kódminta Az Azure IoT Plug and Play, amellyel manuális konfigurálás nélkül integrálhatja az intelligens eszközöket a megoldásokba.  Alapértelmezés szerint a dokumentáció legtöbb mintája IoT-Plug and Play. Ha többet szeretne megtudni az IoT PnP előnyeiről, valamint a használatának vagy használatának melleti esetekről, olvassa el a Mi az [Az IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

 Amikor a Python-kód üzenetet küld az eszközről az IoT Hubnak, az üzenet megjelenik a CLI-rendszerhéjban, amely figyeli az eseményeket:

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

Az eszköz most már biztonságosan csatlakoztatva van, és telemetriát küld a Azure IoT Hub.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat.

Erőforráscsoport törlése név alapján:
1. Futtassa [az az group delete](/cli/azure/group#az_group_delete) parancsot. Ez a parancs eltávolítja az erőforráscsoportot, a IoT Hub és a létrehozott eszközregisztrációt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa [az az group list parancsot](/cli/azure/group#az_group_list) az erőforráscsoport törlése megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás-munkafolyamatot sajátított el, amely segítségével biztonságosan csatlakoztathat egy eszközt a felhőhöz, és hogyan továbbíthat eszközről a felhőbe telemetriai adatokat. Az Azure CLI használatával létrehozott egy IoT Hubot és egy eszközt, majd az Azure IoT Python SDK-val létrehozott egy szimulált eszközt, és telemetriát küldött a hubnak. 

Következő lépésként tekintse át az Azure IoT Python SDK-t az alkalmazásminták segítségével.

- [Aszinkron minták:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios)Ez a könyvtár aszinkron Python-mintákat tartalmaz további IoT Hub forgatókönyvekhez.
- [Szinkron minták:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)Ez a könyvtár Python-mintákat tartalmaz a Python 2.7-es vagy a Python 3.6-os vagy további verziók szinkron kompatibilitási forgatókönyvéhez
- [IoT Edge:](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)Ez a könyvtár Python-mintákat tartalmaz az Edge-modulok és alsóbb rétegbeli eszközök használatához.
