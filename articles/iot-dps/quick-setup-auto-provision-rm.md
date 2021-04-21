---
title: Rövid útmutató – Azure IoT Hub Device Provisioning Service (DPS) létrehozása Azure Resource Manager sablonnal (ARM-sablon)
description: Azure rövid útmutató – Megtudhatja, hogyan hozhat létre Azure IoT Hub Device Provisioning Service -t (DPS) egy Azure Resource Manager (ARM-sablon) használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: bcb37c624f53d961020de022569a621ca1dfaba1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788983"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Rövid útmutató: A IoT Hub Device Provisioning Service (DPS) beállítása ARM-sablonnal

Az eszközök [üzembe Azure Resource Manager](../azure-resource-manager/management/overview.md) Azure-felhőerőforrások programozott módon történő beállításához használhat egy Azure Resource Manager sablont (ARM-sablont). Ezek a lépések azt mutatják be, hogyan hozhat létre egy IoT Hubot és egy új IoT Hub Device Provisioning Service ARM-sablonnal. Az Iot Hub a sablon használatával a DPS-erőforráshoz is kapcsolódik. Ez a hivatkozás lehetővé teszi, hogy a DPS-erőforrás az Ön által konfigurált kiosztási szabályzatok alapján eszközöket rendeljen a központhoz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ez a rövid útmutató az [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), a és az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) használatával végzi el az erőforráscsoport létrehozásához és a sablon üzembe helyezéséhez szükséges programozási lépéseket, de a [PowerShell,](../azure-resource-manager/templates/deploy-powershell.md)a .NET, a Ruby vagy más programozási nyelvek használatával egyszerűen elvégezheti ezeket a lépéseket, és üzembe helyezheti a sablont. 

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonok használatának módszerét, az alábbi Üzembe helyezés az **Azure-ban** gombra kattintva megnyithatja a sablont az üzembe helyezéshez a Azure Portal.

[![Üzembe helyezés az Azure-ban – áttekintés](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/) közül származik.

> [!NOTE]
> Jelenleg nincs ARM-sablontámogatás a regisztrációk új DPS-erőforrásokkal való létrehozásához. Ez egy általános és ismert kérés, amely implementációra van mérlegelve.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

A fenti sablonban két Azure-erőforrás van definiálva:

* [**Microsoft.Devices/iothubs:**](/azure/templates/microsoft.devices/iothubs)Létrehoz egy új Azure IoT Hub.
* [**Microsoft.Devices/provisioningservices:**](/azure/templates/microsoft.devices/provisioningservices)Létrehoz egy új Azure IoT Hub Device Provisioning Service-t, IoT Hub már hozzá van kapcsolva.


## <a name="deploy-the-template"></a>A sablon üzembe helyezése

#### <a name="deploy-with-the-portal"></a>Üzembe helyezés a Portallal

1. Az alábbi rendszerkép kiválasztásával jelentkezzen be az Azure-ba, és nyissa meg az üzembe helyezéshez szükséges sablont. A sablon létrehoz egy új Iot Hub- és DPS-erőforrást. A központ a DPS-erőforrásban lesz összekapcsolva.

    [![Üzembe helyezés az Azure-ban a Portálon – lépések](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket, majd kattintson **a Felülvizsgálat + létrehozás gombra.**

    ![ARM-sablon üzembe helyezési paraméterei a portálon](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Ha az alábbiakban nincs megadva, az alapértelmezett értékkel hozza létre az Iot Hub- és DPS-erőforrást.

    | Mező | Leírás |
    | :---- | :---------- |
    | **Előfizetés** | Válassza ki Azure-előfizetését. |
    | **Erőforráscsoport** | Kattintson **az Új létrehozása** elemre, adjon meg egy egyedi nevet az erőforráscsoportnak, majd kattintson az OK **gombra.** |
    | **Régió** | Válasszon ki egy régiót az erőforrások számára. Például: USA **keleti régiója.** |
    | **Iot Hub neve** | Adjon meg egy nevet a IoT Hub névtérben globálisan egyedinek kell lennie a *azure-devices.net* belül. Az üzembe helyezés ellenőrzésekor a következő szakaszban szükség lesz a központ nevére. |
    | **Kiépítési szolgáltatás neve** | Adja meg az új Device Provisioning Service- (DPS-) erőforrás nevét. A névnek globálisan egyedinek kell lennie a *.azure-devices-provisioning.net* névtérben. Az üzembe helyezés ellenőrzésekor szüksége lesz a DPS-névre a következő szakaszban. |
    
3. A következő képernyőn olvassa el a feltételeket. Ha elfogadja az összes feltételt, kattintson a **Létrehozás gombra.** 

    Az üzembe helyezés néhány percet igénybe vehet. 

    A Azure Portal mellett használhatja a Azure PowerShell, az Azure CLI-t és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-powershell.md)


#### <a name="deploy-with-the-azure-cli"></a>Üzembe helyezés az Azure CLI-val

Az Azure CLI használatához 2.6-os vagy újabb verzió szükséges. Ha helyileg futtatja az Azure CLI-t, ellenőrizze a verzióját a következő futtatásával: `az --version`

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. Ha az Azure CLI-t helyileg futtatja ahelyett, hogy a portálon futtatja, be kell jelentkeznie. A parancssorba való bejelentkezéshez futtassa a [bejelentkezési parancsot:](/cli/azure/get-started-with-az-cli2)
    
    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat](/cli/azure/account):
    
    ```azurecli
    az account list -o table
    ```

    A következő paranccsal válassza ki azt az előfizetést, amely az IoT Hub- és DPS-erőforrások létrehozásához szükséges parancsok futtatásához használható. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Másolja és illessze be az alábbi parancsokat a parancssorba. Ezután hajtsa végre a parancsokat az **ENTER billentyű lenyomásával.**
   
    > [!TIP]
    > A parancsok kérik az erőforráscsoport helyét. Az elérhető helyek listáját az parancs első futtatásával tudja megtekinteni:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. A parancsok a következő információkat kérik. Adja meg az egyes értékeket, majd nyomja le az **ENTER billentyűt.**

    | Paraméter | Leírás |
    | :-------- | :---------- |
    | **Projektnév** | A paraméter értéke egy erőforráscsoport létrehozásához lesz használva, amely az összes erőforrást fogja használni. A `rg` sztring az erőforráscsoport nevének végén lesz hozzáadva. |
    | **Helyen** | Ez az érték az a régió, ahol az összes erőforrás található. |
    | **iotHubName** | Adjon meg egy nevet a IoT Hub névtérben globálisan egyedinek kell lennie a *azure-devices.net* belül. Az üzembe helyezés érvényesítésekor a következő szakaszban szüksége lesz a központ nevére. |
    | **provisioningServiceName** | Adja meg az új Device Provisioning Service- (DPS-) erőforrás nevét. A névnek globálisan egyedinek kell lennie a *.azure-devices-provisioning.net* névtérben. A dps-névre a következő szakaszban lesz szüksége az üzembe helyezés érvényesítésekor. |

    A sablon üzembe helyezéséhez az AzureCLI használható. Az Azure CLI mellett a következő funkciókat is használhatja: Azure PowerShell, Azure Portal és REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-powershell.md)


## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Az üzembe helyezés ellenőrzéséhez futtassa a következő [parancsot](/cli/azure/resource#az_resource_list) az erőforrások listához, és keresse meg az új kiépítési szolgáltatást és az IoT Hubot a kimenetben:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Annak ellenőrzéséhez, hogy a központ már csatlakoztatva van-e a DPS-erőforráshoz, futtassa a [következő DPS-bővítmény show parancsát.](/cli/azure/iot/dps#az_iot_dps_show)

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Figyelje meg a taghoz kapcsolt `iotHubs` hubokat.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, a Azure Portal vagy az Azure CLI használatával törölheti az erőforráscsoportot és annak összes erőforrását.

Ha törölni szeretne egy erőforráscsoportot és annak összes erőforrását a Azure Portal, nyissa meg az erőforráscsoportot, és kattintson az Erőforráscsoport törlése **elemre,** és a lap tetején.

Az Azure CLI használatával üzembe helyezett erőforráscsoport törlése:

```azurecli
az group delete --name "${projectName}rg"
```

Az erőforráscsoportokat és az egyes erőforrásokat a Azure Portal, a PowerShell vagy a REST API-k, valamint a Azure Resource Manager vagy a IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT Hubot és egy Device Provisioning Service-példányt, és összekapcsolta a két erőforrást. Ha meg szeretne ismerkedni ezzel a beállítással egy eszköz üzembe helyezésével, folytassa az eszköz létrehozásának rövid útmutatóját.

> [!div class="nextstepaction"]
> [Rövid útmutató eszköz üzembe helyezéséhez](./quick-create-simulated-device-symm-key.md)
