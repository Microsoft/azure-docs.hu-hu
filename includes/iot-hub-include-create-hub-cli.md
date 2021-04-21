---
title: fájl belefoglalása
description: fájl belefoglalása
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: be51d4db6c98b63fcb1c9c60df045b494536d78f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766521"
---
A következő szakaszokban beállít egy terminált, és az Azure CLI használatával létrehoz egy IoT Hubot. Az Azure CLI-parancsokat futtató terminálok konfigurálhoz használhatja a böngészőalapú Azure Cloud Shell vagy egy helyi terminált.
* A Cloud Shell a következő szakaszra: Indítsa el a [Cloud Shell.](#launch-the-cloud-shell) 
* Ha helyi terminált használ, hagyja ki a következő szakaszt, és nyissa meg [a Helyi terminál megnyitása szakaszt.](#open-a-local-terminal)

## <a name="launch-the-cloud-shell"></a>Indítsa el a Cloud Shell
Ebben a szakaszban létrehoz egy Cloud Shell munkamenetet, és konfigurálja a terminálkörnyezetet.

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.  

A következő Cloud Shell:

1. Kattintson **a Cloud Shell** jobb felső menüsávjában található Azure Portal. 

    ![Azure Portal Cloud Shell gomb](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Ha első alkalommal használja a Cloud Shell, a rendszer felkéri, hogy hozzon létre egy tárolót, amely szükséges a Cloud Shell.  Válasszon ki egy előfizetést egy tárfiók létrehozásához, és válassza Microsoft Azure fájlok megosztását. 

2. Válassza ki az előnyben részesített CLI-környezetet a **Környezet kiválasztása legördülő** menüben. Ez a rövid útmutató a **Bash-környezetet** használja. Az alábbi CLI-parancsok a PowerShell-környezetben is működnek. 

    ![A CLI-környezet kiválasztása](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Hagyja ki a következő szakaszt, és [ugorjon az Azure IoT-bővítmény telepítése című szakaszra.](#install-the-azure-iot-extension) 

## <a name="open-a-local-terminal"></a>Helyi terminál megnyitása
Ha a helyi terminált választotta a Cloud Shell helyett, töltse ki ezt a szakaszt.  

1. Nyisson meg egy helyi terminált.
1. Futtassa [az az login](/cli/azure/reference-index#az_login) parancsot:

   ```azurecli
   az login
   ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban https://aka.ms/devicelogin megjelenő engedélyezési kódot.

    Ha nem érhető el webböngésző, vagy a webböngésző nem nyílik meg, használja az eszköz kódfolyamát a `az login --use-device-code` következővel: .

1. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

    További információ a különböző hitelesítési módszerekről: [Bejelentkezés az Azure CLI használatával]( /cli/azure/authenticate-azure-cli ).

1. Ugrás a következő szakaszra: [Az Azure IoT-bővítmény telepítése.](#install-the-azure-iot-extension) 

## <a name="install-the-azure-iot-extension"></a>Az Azure IoT-bővítmény telepítése
Ebben a szakaszban az Azure CLI-hez Microsoft Azure ioT-bővítményt telepíti a CLI-felületre. Az IOT-bővítmény IoT Hub, IoT Edge és IoT Device Provisioning Service-specifikus parancsokat ad hozzá az Azure CLI-hez.

> [!IMPORTANT]
> A rövid útmutató hátralévő részében a terminálparancsok ugyanúgy működnek Cloud Shell helyi terminálban. Parancs futtatásához válassza a Másolás **lehetőséget** egy kódblokk másoláshoz ebben a rövid útmutatóban. Ezután illessze be a CLI-felületbe, és futtassa.

Futtassa [az az extension add](/cli/azure/extension#az_extension_add) parancsot. 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Ebben a szakaszban az Azure CLI használatával fog létrehozni egy IoT Hubot és egy erőforráscsoportot.  Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az IoT Hub központi üzenetközpontként működik az IoT-alkalmazás és az eszközök közötti kétirányú kommunikációhoz. 

IoT Hub és erőforráscsoport létrehozása:

1. Futtassa [az az group create parancsot](/cli/azure/group#az_group_create) egy erőforráscsoport létrehozásához. A következő parancs létrehoz egy *MyResourceGroup* nevű erőforráscsoportot az *eastus* helyen. 
    >[!NOTE]
    > Igény szerint másik helyet is beállíthat. Az elérhető helyekért futtassa a következőt: `az account list-locations` . Ez az oktatóanyag *az eastus régiót használja* a példaparancsban látható módon. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Futtassa [az az iot hub create parancsot](/cli/azure/iot/hub#az_iot_hub_create) egy IoT Hub létrehozásához. Az IoT Hub létrehozása eltarthat néhány percig. 

    *YourIotHubName*. Cserélje le ezt a helyőrzőt és a körüli kapcsos zárójeleket a következő parancsban az IoT Hubhoz választott névvel. Az IoT Hub nevének globálisan egyedinek kell lennie az Azure-ban. Használja az IoT Hub nevét a rövid útmutató további részében, bárhol is legyen a helyőrző.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása
Ebben a szakaszban egy szimulált IoT-eszközt hoz létre, amely csatlakozik az IoT Hubhoz. 

Szimulált eszköz létrehozása:
1. Futtassa [az az iot hub device-identity create parancsot](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) a CLI-rendszerhéjban. Ez létrehozza a szimulált eszközidentitást. 

    *YourIotHubName*. Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    *myDevice*. Ezt a nevet használhatja közvetlenül a cikk további részében a szimulált eszközazonosítóhoz. Másik nevet is használhat. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Futtassa [az az iot hub device-identity connection-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) parancsot. 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    A kapcsolati sztring kimenete a következő formátumban van:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Mentse a kapcsolati sztringet egy biztonságos helyre. 

> [!NOTE]
> Ne nyissa meg a CLI-rendszerhéjat. A későbbi lépésekben szükség lesz rá.