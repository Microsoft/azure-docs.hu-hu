---
title: Eszköz-telemetria küldése Azure IoT Hub rövid útmutatónak (Node.js)
description: Ebben a rövid útmutatóban a Azure IoT Hub eszközoldali SDK for Node.js telemetriai adatokat küld egy eszközről egy Iot Hubra.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 0e1c99124228da9490abaa17ecc41b931631d9fb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876975"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Rövid útmutató: Telemetria küldése eszközről IoT Hubra (Node.js)

**A következőkre** [vonatkozik: Eszközalkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban megismer egy alapszintű IoT-eszközalkalmazás-fejlesztési munkafolyamatot. Az Azure CLI használatával létrehozhat egy Azure IoT Hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK-val hozzáférhet az eszközhöz, és telemetriát küldhet a hubnak.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a kezdés előtt hozzon létre egyet ingyenesen.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben Azure Cloud Shell interaktív CLI-felület használatával. Ha a Cloud Shell, semmit sem kell telepítenie. Ha inkább helyileg szeretné használni a CLI-t, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Node.js 10+ .](https://nodejs.org) Ha a telepített Azure Cloud Shell használja, ne frissítse a Node.js. A Azure Cloud Shell már rendelkezik a legújabb Node.js verzióval.

    A következő paranccsal ellenőrizze a Node.js gép aktuális verzióját:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Üzenetek küldése a Node.js SDK használatával
Ebben a szakaszban a Node.js SDK használatával fog üzeneteket küldeni a szimulált eszközről az IoT Hubnak. 

1. Nyisson meg egy új terminálablakot. Ezzel a terminállal telepítheti a Node.js SDK-t, és használhatja Node.js kódot. Most már két terminálnak kell nyitva lennie: az Node.js-val való használatra megnyitott terminállal, valamint az előző szakaszokban az Azure CLI-parancsok beíratásához használt CLI-rendszerhéjjal.

1. Másolja az [Azure IoT Node.js SDK-eszközmintákat](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Lépjen az *azure-iot-sdk-node/device/samples/pnp könyvtárba:*

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Telepítse az Azure IoT Node.js SDK-t és a szükséges függőségeket:

    ```console
    npm install
    ```

    Ez a parancs telepíti az eszközminták könyvtárábanpackage.js *fájlban* megadott megfelelő függőségeket.

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
1. Futtassa az [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) parancsot a nyílt CLI-rendszerhéjban a szimulált IoT-eszközön futó események monitorozásának megkezdéséhez.  Az eseményüzenetek érkezésükkor a terminálon fognak formában edzeni.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Az Node.js terminálban futtassa a telepített mintafájl kódját az *simple_thermostat.js.* Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT Hubnak.

    A minta Node.js a terminálból:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Ez a kódminta Az Azure IoT Plug and Play, amellyel manuális konfigurálás nélkül integrálhatja az intelligens eszközöket a megoldásokba.  Alapértelmezés szerint a dokumentáció legtöbb mintája IoT-Plug and Play. Ha többet szeretne megtudni az IoT PnP előnyeiről, valamint a használatának vagy használatának melleti esetekről, olvassa el a Mi az [Az IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Mivel a Node.js kód szimulált telemetriai üzenetet küld az eszközről az IoT Hubnak, az üzenet megjelenik a CLI-rendszerhéjban, amely az eseményeket figyeli:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
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

Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás-munkafolyamatot sajátított el, amely segítségével biztonságosan csatlakoztathat egy eszközt a felhőhöz, és hogyan továbbíthat eszközről a felhőbe telemetriai adatokat. Az Azure CLI használatával létrehozott egy IoT Hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK-val fért hozzá az eszközhöz, és telemetriát küldött a hubra. 

A következő lépésben megismerheti az Azure IoT Node.js SDK-t az alkalmazásminták segítségével.

- [További Node.js:](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)Ez a könyvtár további mintákat tartalmaz a Node.js SDK-adattárból, hogy bemutassa IoT Hub forgatókönyveket.