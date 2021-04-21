---
title: Eszköz-telemetria küldése Azure IoT Hub rövid útmutatóhoz (Node.js)
description: Ebben a rövid útmutatóban a Azure IoT Hub Eszközoldali SDK for Node.js telemetriai adatokat fog küldeni egy eszközről egy Iot Hubra.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 3d42ac814678136c2f6342cd1064e3c3ff394507
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777238"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra (Node.js)

**A következőkre** [vonatkozik: Eszközalkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban megismer egy alapszintű IoT-eszközalkalmazás-fejlesztési munkafolyamatot. Az Azure CLI használatával létrehoz egy Azure IoT Hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK-val hozzáfér az eszközhöz, és telemetriát küld a hubnak.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) létre egyet ingyenesen.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben Azure Cloud Shell interaktív PARANCSSORi felület használatával. Ha a Cloud Shell, semmit sem kell telepítenie. Ha inkább helyileg szeretné használni a CLI-t, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Node.js 10+.](https://nodejs.org) Ha a telepített Azure Cloud Shell használja, ne frissítse a Node.js. A Azure Cloud Shell már rendelkezik a legújabb Node.js verzióval.

    Ellenőrizze a fejlesztői Node.js aktuális verzióját a következő paranccsal:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Üzenetek küldése a Node.js SDK használatával
Ebben a szakaszban a Node.js SDK használatával fog üzeneteket küldeni a szimulált eszközről az IoT Hubra. 

1. Nyisson meg egy új terminálablakot. Ezzel a terminállal telepítheti a Node.js SDK-t, és használhatja Node.js kódmintát. Most már két terminálnak kell nyitva lennie: az Node.js-val való használathoz megnyitott terminállal és az előző szakaszokban az Azure CLI-parancsok beíratásához használt CLI-felületel.

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

    Ez a parancs telepíti a megfelelő függőségeket apackage.js *az eszközmintakönyvtárban* található fájlban.

1. Állítsa be mindkét alábbi környezeti változót, hogy a szimulált eszköz csatlakozik az Azure IoT-hez.
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
1. Futtassa az [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) parancsot a nyílt CLI-rendszerhéjban a szimulált IoT-eszközön futó események monitorozásának megkezdéséhez.  Az eseményüzenetek érkezésükkor a terminálon nyomtatódnak ki.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Az Node.js terminálban futtassa a telepített mintafájl kódját a *simple_thermostat.js.* Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT Hubnak.

    A minta Node.js a terminálból:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Ez a kódminta Az Azure IoT Plug and Play, amellyel manuális konfigurálás nélkül integrálhatja az intelligens eszközöket a megoldásokba.  Alapértelmezés szerint a dokumentáció legtöbb mintája IoT-Plug and Play. Ha többet szeretne megtudni az IoT PnP előnyeiről, valamint a használatának vagy használatának melleti esetekről, olvassa el a Mi az [Az IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Ahogy a Node.js kód szimulált telemetriai üzenetet küld az eszközről az IoT Hubnak, az üzenet megjelenik a CLI-rendszerhéjban, amely figyeli az eseményeket:

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
1. Az [az group list paranccsal](/cli/azure/group#az_group_list) ellenőrizze, hogy az erőforráscsoport törölve lett-e.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás-munkafolyamatot sajátított el, amely segítségével biztonságosan csatlakoztathat egy eszközt a felhőhöz, és eszközről a felhőbe telemetriai adatokat továbbíthat. Az Azure CLI használatával létrehozott egy IoT Hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK-val fért hozzá az eszközhöz, és telemetriát küldött a hubra. 

A következő lépésben megismerheti az Azure IoT Node.js SDK-t az alkalmazásmintákon keresztül.

- [További Node.js minták:](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)Ez a könyvtár további mintákat tartalmaz a Node.js SDK-adattárból, hogy bemutassa IoT Hub forgatókönyveket.