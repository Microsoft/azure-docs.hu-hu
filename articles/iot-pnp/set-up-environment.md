---
title: Rövid útmutató – a IoT Plug and Playához szükséges IoT-erőforrások beállítása | Microsoft Docs
description: Rövid útmutató – hozzon létre egy IoT Hub és egy eszköz kiépítési szolgáltatási példányát, amelyet a IoT Plug and Play rövid útmutatók és oktatóanyagok segítségével használhat.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5446f8481439dabffe67f9b00b5f65b1da495746
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742653"
---
# <a name="quickstart-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Rövid útmutató: a környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok

Mielőtt IoT Plug and Play a gyors útmutatókat és oktatóanyagokat, konfigurálnia kell egy IoT hubot és a Device kiépítési szolgáltatást (DPS) az Azure-előfizetésében. Szüksége lesz a minta-alkalmazások és az Azure IoT Explorer eszköz helyi példányaira is.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha el szeretné kerülni az Azure CLI helyi telepítésének követelményét, használhatja a Azure Cloud Shell a Cloud Services beállításához.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Erőforrások létrehozása

Hozzon létre egy Azure-erőforráscsoportot az erőforrásokhoz:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Hozzon létre egy IoT hubot. A következő parancs a nevet használja `my-pnp-hub` példaként a létrehozandó IoT hub neveként. Válasszon egy egyedi nevet az IoT hub számára a következő helyett `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Hozzon létre egy DPS-példányt. A következő parancs a nevet használja `my-pnp-dps` példaként a létrehozandó DPS-példány neveként. Válassza ki a DPS-példány egyedi nevét, amelyet a következő helyen kíván használni `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Ha a DPS-példányt a IoT hubhoz szeretné kapcsolni, használja a következő parancsokat. Cserélje `my-pnp-dps` le `my-pnp-hub` a és a nevet a korábban kiválasztott egyedi nevekre:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>A beállítások beolvasása

Egyes rövid útmutatók és oktatóanyagok az IoT hub kapcsolatok karakterláncát használják. A kapcsolódási karakterláncra az Azure IoT Explorer eszköz beállításakor is szükség van. Kérje le a kapcsolatok karakterláncát, és jegyezze fel most. A helyére írja be `my-pnp-hub` az IoT hub számára kiválasztott egyedi nevet:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

A rövid útmutatók és oktatóanyagok többsége a DPS-konfiguráció *azonosító hatókörét* használja. Kérje le az azonosító hatókörét, és jegyezze fel most. Cserélje le `my-pnp-dps` a t a DPS-példányhoz választott egyedi névre:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Az összes rövid útmutató és oktatóanyag a DPS-eszközök regisztrálására használható. A következő parancs használatával hozzon létre egy `my-pnp-device` *egyéni eszközt* a DPS-példányban. Cserélje le a helyére `my-pnp-dps` a DPS-példányhoz választott egyedi nevet. Jegyezze fel a regisztrációs azonosítót és az elsődleges kulcs értékét a gyors útmutatókban és oktatóanyagokban:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Környezeti változók létrehozása

Hozzon létre öt környezeti változót a minták konfigurálásához a gyors útmutatókban és oktatóanyagokban, hogy az eszköz kiépítési szolgáltatása (DPS) használatával kapcsolódjon az IoT hub-hoz:

* **IOTHUB_DEVICE_SECURITY_TYPE**: az érték `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: a DPS-azonosító hatóköre előzőleg jegyzetet készített.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: az érték `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: a beléptetés elsődleges kulcsa, amelyet korábban jegyzett készített.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: az érték `global.azure-devices-provisioning.net`

A szolgáltatási mintákat a következő környezeti változókra van szükség ahhoz, hogy azonosítsa a hubot és az eszközt, amelyhez csatlakozni szeretne:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **IOTHUB_DEVICE_ID**: `my-pnp-device` .

Például egy Linux bash-rendszerhéjban:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Például a Windows parancssorban:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>A modell fájljainak letöltése

A rövid útmutatók és oktatóanyagok a hőmérséklet-vezérlőhöz és a termosztátos eszközökhöz használják a mintaként szolgáló fájlokat. A minta modell fájljainak letöltése:

1. Hozzon létre egy *modell* nevű mappát a helyi gépen.

1. Kattintson a jobb gombbal [ aTemperatureController.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) elemre, és mentse a JSON-fájlt a *modellek* mappába.

1. Kattintson a jobb gombbal [ aThermostat.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) elemre, és mentse a JSON-fájlt a *modellek* mappába.

## <a name="install-the-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

A rövid útmutatók és oktatóanyagok az **Azure IoT Explorer** eszközt használják. Nyissa meg az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) kiadásait, és bontsa ki a legújabb kiadáshoz tartozó eszközök listáját. Töltse le és telepítse az alkalmazás legújabb verzióját az operációs rendszeréhez.

Amikor először futtatja az eszközt, a rendszer kéri az IoT hub-kapcsolatok karakterláncát. Használja a korábban feljegyzett kapcsolatok sztringjét.

Konfigurálja az eszközt a korábban letöltött modell-fájlok használatára. Az eszköz kezdőlapján válassza a **IoT Plug and Play beállítások**, majd a **+ > helyi mappa hozzáadása** elemet. Válassza ki a korábban létrehozott *modellek* mappát. Ezután válassza a **Mentés** lehetőséget a beállítások mentéséhez.

További információt az [Azure IoT Explorer telepítése és használata](howto-use-iot-explorer.md)című témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az IoT hub és a DPS-példány az összes IoT Plug and Play gyors és oktatóanyagokat is használhatja, így csak egyszer kell elvégeznie a jelen cikkben szereplő lépéseket. Ha elkészült, eltávolíthatja őket az előfizetésből a következő paranccsal:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a környezetét, kipróbálhatja az alábbiak egyikét vagy az oktatóanyagokat, például:

> [!div class="nextstepaction"]
> [Plug and Play IoT csatlakoztatása IoT Hub](quickstart-connect-device.md)
