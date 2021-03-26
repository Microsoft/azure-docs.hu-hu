---
title: Eszköz telemetria küldése az Azure IoT Hub rövid útmutatóba (Node.js)
description: Ebben a rövid útmutatóban az Azure IoT Hub eszközoldali SDK for Node.js használatával küld telemetria az eszközről egy IOT hubhoz.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 047700be674dfab997b5c87f7446c19fdea9e0eb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605960"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Gyors útmutató: telemetria küldése az eszközről egy IoT hubhoz (Node.js)

**A** következőkre vonatkozik: [eszköz-alkalmazás fejlesztése](about-iot-develop.md#device-application-development)

Ebben a rövid útmutatóban egy alapszintű IoT-fejlesztési munkafolyamatot sajátíthat el. Az Azure CLI-vel létrehozhat egy Azure IoT hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK használatával elérheti az eszközt, és elküldheti a telemetria a központba.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben futtatott interaktív CLI-rendszerhéj Azure Cloud Shellával. Ha a Cloud Shell használja, semmit nem kell telepítenie. Ha a parancssori felület helyi használatát szeretné használni, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). Ha a Azure Cloud Shell használja, ne frissítse Node.js telepített verzióját. A Azure Cloud Shell már rendelkezik a legújabb Node.js-verzióval.

    A következő parancs használatával ellenőrizze a Node.js aktuális verzióját a fejlesztői gépen:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Üzenetek küldése a Node.js SDK-val
Ebben a szakaszban a Node.js SDK használatával küld üzeneteket a szimulált eszközről az IoT hubhoz. 

1. Nyisson meg egy új terminálablakot. Ezt a terminált fogja használni a Node.js SDK telepítéséhez és Node.js mintakód használatához. Ekkor két terminált kell megnyitnia: az imént megnyitotta, hogy működjön a Node.jskal, és hogy az előző részekben használt CLI-rendszerhéj beírja az Azure CLI-parancsokat.

1. Másolja az [Azure IoT Node.js SDK-eszközök mintáit](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) a helyi gépre:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navigáljon az *Azure-IOT-SDK-Node/Device/Samples/PnP* könyvtárba:

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Telepítse az Azure IoT Node.js SDK-t és a szükséges függőségeket:

    ```console
    npm install
    ```

    Ez a parancs telepíti a megfelelő függőségeket, amelyeket az eszköz Samples könyvtáránakpackage.jsfájljában *meg* kell adni.

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
1. A megnyitott CLI-rendszerhéjban futtassa az az [IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) parancsot a szimulált IOT-eszközön lévő események figyelésének megkezdéséhez.  Az események a terminálon lesznek kinyomtatva, ahogy érkeznek.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. A Node.js-terminálban futtassa a *simple_thermostat.js* telepített sablonfájl kódját. Ez a kód hozzáfér a szimulált IoT-eszközhöz, és üzenetet küld az IoT hubhoz.

    A Node.js minta futtatása a terminálról:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Ez a mintakód az Azure IoT Plug and Play használja, amely lehetővé teszi, hogy manuális konfiguráció nélkül integrálja az intelligens eszközöket a megoldásaiba.  Alapértelmezés szerint a dokumentációban szereplő legtöbb minta a IoT Plug and Play használja. Ha többet szeretne megtudni a IoT PnP előnyeiről, illetve a használatának vagy használatának mellőzéséről, olvassa el a [Mi az a IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Mivel a Node.js kód egy szimulált telemetria üzenetet küld az eszközről az IoT hub-ra, az üzenet a CLI-rendszerhéjban jelenik meg, amely figyeli az eseményeket:

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

Ebben a rövid útmutatóban egy alapszintű Azure IoT-alkalmazás munkafolyamatát ismertette az eszköz felhőbe való biztonságos csatlakoztatásához és az eszközről a felhőbe irányuló telemetria küldéséhez. Az Azure CLI-vel létrehozta az IoT hubot és egy szimulált eszközt, majd az Azure IoT Node.js SDK használatával fér hozzá az eszközhöz, és elküldheti a telemetria a hubhoz. 

A következő lépésként Fedezze fel az Azure IoT Node.js SDK-t az alkalmazási mintákon keresztül.

- [További Node.js minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): Ez a könyvtár több mintát tartalmaz az Node.js SDK-tárházból a IoT hub forgatókönyvek bemutatásához.