---
title: fájl belefoglalása
description: fájl belefoglalása
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 10bd2c4902157b9e01b1cb0ff10b3ebdf448568c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244827"
---
A következő fejezetekben beállíthat egy terminált, és az Azure CLI használatával hozhat létre egy IoT hubot. Az Azure CLI-parancsokat futtató terminálok konfigurálásához használhatja a böngészőalapú Azure Cloud Shell, vagy használhat helyi terminált is.
* A Cloud Shell használatához lépjen a következő szakaszra: [indítsa el a Cloud Shell](#launch-the-cloud-shell). 
* Helyi terminál használatához ugorjon a következő szakaszra, és válassza a [helyi terminál megnyitása](#open-a-local-terminal)lehetőséget.

## <a name="launch-the-cloud-shell"></a>A Cloud Shell elindítása
Ebben a szakaszban egy Cloud Shell munkamenetet hoz létre, és konfigurálja a terminál környezetét.

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.  

A Cloud Shell elindítása:

1. A Azure Portal jobb felső menüsorában kattintson a **Cloud Shell** gombra. 

    ![Azure Portal Cloud Shell gomb](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Ha első alkalommal használja a Cloud Shell, a rendszer kéri, hogy hozzon létre egy tárolót, amely a Cloud Shell használatához szükséges.  Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását. 

2. Válassza ki az előnyben részesített CLI-környezetet a **környezet kiválasztása** legördülő menüben. Ez a rövid útmutató a **bash** -környezetet használja. Az alábbi CLI-parancsok a PowerShell-környezetben is működnek. 

    ![CLI-környezet kiválasztása](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Ugorja át a következő szakaszt, és válassza az [Azure IoT bővítmény telepítése](#install-the-azure-iot-extension)lehetőséget. 

## <a name="open-a-local-terminal"></a>Helyi terminál megnyitása
Ha Cloud Shell helyett helyi terminált szeretne használni, végezze el ezt a szakaszt.  

1. Nyisson meg egy helyi terminált.
1. Futtassa az az [login](/cli/azure/reference-index#az_login) parancsot:

   ```azurecli
   az login
   ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, https://aka.ms/devicelogin és adja meg a terminálon megjelenő engedélyezési kódot.

    Ha nem érhető el webböngésző, vagy a webböngésző nem nyílik meg, használja az eszköz kódjának folyamatát `az login --use-device-code` .

1. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

    További információ a különböző hitelesítési módszerekről: [Bejelentkezés az Azure CLI használatával]( /cli/azure/authenticate-azure-cli ).

1. Ugorjon a következő szakaszra: [telepítse az Azure IoT bővítményt](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Az Azure IoT bővítmény telepítése
Ebben a szakaszban az Azure CLI-hez készült Microsoft Azure IoT-bővítményt telepíti a CLI-rendszerhéjba. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

> [!IMPORTANT]
> A rövid útmutató többi részén található terminál-parancsok ugyanúgy működnek, mint Cloud Shell vagy egy helyi terminálon. A parancs futtatásához válassza a **Másolás** elemet a rövid útmutatóban található kód egy blokkjának másolásához. Ezután illessze be a CLI-rendszerhéjba, és futtassa.

Futtassa az az [Extension Add](/cli/azure/extension#az-extension-add) parancsot. 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Ebben a szakaszban az Azure CLI használatával hozzon létre egy IoT hubot és egy erőforráscsoportot.  Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az IoT hub központi üzenetküldési központként működik a IoT-alkalmazás és az eszközök közötti kétirányú kommunikációhoz. 

IoT hub és erőforráscsoport létrehozása:

1. Futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához. A következő parancs létrehoz egy *MyResourceGroup* nevű erőforráscsoportot a *eastus* helyen. 
    >[!NOTE]
    > Opcionálisan másik helyet is beállíthat. Az elérhető helyszínek megjelenítéséhez futtassa a parancsot `az account list-locations` . Ez az oktatóanyag a *eastus* használja, ahogy az a example parancsban is látható. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Futtassa az az [IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) parancsot egy IOT hub létrehozásához. Az IoT hub létrehozása eltarthat néhány percig. 

    *YourIotHubName*. Cserélje le ezt a helyőrzőt és a környező kapcsos zárójeleket a következő parancsban az IoT hub számára kiválasztott néven. Az IoT hub nevének globálisan egyedinek kell lennie az Azure-ban. Használja az IoT hub nevét a rövid útmutató hátralévő részében, ahol megjelenik a helyőrző.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Szimulált eszköz létrehozása
Ebben a szakaszban egy szimulált IoT-eszközt hoz létre, amely az IoT hubhoz csatlakozik. 

Szimulált eszköz létrehozása:
1. Futtassa az az [IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) parancsot a CLI-rendszerhéjban. Ezzel létrehozza a szimulált eszköz identitását. 

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    *myDevice*. Ezt a nevet közvetlenül is használhatja a szimulált eszköz AZONOSÍTÓhoz a cikk további részében. Igény szerint más nevet is használhat. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Futtassa az az [IOT hub Device-Identity kapcsolat-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) parancsot. 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    A következő formátumú a kapcsolatok karakterláncának kimenete:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Mentse a kapcsolatok karakterláncát egy biztonságos helyen. 

> [!NOTE]
> Tartsa megnyitva a CLI-rendszerhéjt. A későbbi lépésekben szükség lesz rá.