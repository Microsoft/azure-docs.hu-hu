---
title: Rövid útmutató – Azure-IoT Hub Device Provisioning Service (DPS) létrehozása Azure Resource Manager sablon használatával (ARM-sablon)
description: Azure rövid útmutató – megtudhatja, hogyan hozhat létre Azure-IoT Hub Device Provisioning Service (DPS) Azure Resource Manager sablonnal (ARM-sablon).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: cc55d1569c85dd0873d0132833acc99bd5d06136
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928941"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Rövid útmutató: a IoT Hub Device Provisioning Service (DPS) beállítása ARM-sablonnal

Az eszközök üzembe helyezéséhez szükséges Azure Cloud-erőforrások programozott beállításához használhat egy [Azure Resource Manager](../azure-resource-manager/management/overview.md) SABLONT (ARM-sablont). Ezek a lépések bemutatják, hogyan hozhat létre egy IoT hubot és egy új IoT Hub Device Provisioning Service ARM-sablonnal. Az IOT hub a DPS-erőforráshoz is kapcsolódik a sablon használatával. Ez a hivatkozás lehetővé teszi a DPS-erőforrás számára, hogy az Ön által konfigurált foglalási szabályzatok alapján rendeljen eszközöket a hubhoz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ez a rövid útmutató a [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)és az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) használatával hajtja végre az erőforráscsoport létrehozásához és a sablon üzembe helyezéséhez szükséges programozási lépéseket, azonban a [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), a .net, a Ruby vagy más programozási nyelvek használatával egyszerűen elvégezheti ezeket a lépéseket, és üzembe helyezheti a sablont. 

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, akkor az alábbi **üzembe helyezés az Azure** -ban gombra kattintva megnyílik a sablon az üzembe helyezéshez a Azure Portal.

[![Üzembe helyezés az Azure-ban – áttekintés](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/) közül származik.

> [!NOTE]
> Jelenleg nincs olyan ARM-sablon, amely új DPS-erőforrásokkal rendelkező beléptetések létrehozását támogatja. Ez egy közös és értelmezhető kérelem, amely a megvalósításnak tekintendő.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Két Azure-erőforrás van definiálva a fenti sablonban:

* [**Microsoft. Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): új Azure-IoT hub hoz létre.
* [**Microsoft. Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): új Azure-IoT hub Device Provisioning Service hoz létre, és az új IoT hub már csatolva van hozzá.


## <a name="deploy-the-template"></a>A sablon üzembe helyezése

#### <a name="deploy-with-the-portal"></a>Üzembe helyezés a Portallal

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a sablont az üzembe helyezéshez. A sablon egy új IOT hubot és DPS-erőforrást hoz létre. A központ a DPS erőforrásban lesz csatolva.

    [![Üzembe helyezés az Azure-ban a portálon – lépések](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket, majd kattintson a **felülvizsgálat + létrehozás** gombra.

    ![ARM-sablon telepítési paraméterei a portálon](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    Hacsak nem az alábbiakban van megadva, az alapértelmezett érték használatával hozza létre az IOT hub és a DPS erőforrást.

    | Mező | Leírás |
    | :---- | :---------- |
    | **Előfizetés** | Válassza ki Azure-előfizetését. |
    | **Erőforráscsoport** | Kattintson az **új létrehozása** elemre, és adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra. |
    | **Régió** | Válasszon régiót az erőforrások számára. Például az **USA keleti** régiója. |
    | **IOT hub neve** | Adja meg annak a IoT Hubnek a nevét, amelynek globálisan egyedinek kell lennie a *. Azure-Devices.net* névtérben. A központi telepítés ellenőrzésekor a következő szakaszban kell megadnia a hub nevét. |
    | **Kiépítési szolgáltatás neve** | Adja meg az új eszköz-kiépítési szolgáltatás (DPS) erőforrásának nevét. A névnek globálisan egyedinek kell lennie a *. Azure-Devices-Provisioning.net* névtérben. A telepítés ellenőrzésekor a következő szakaszban a DPS neve szükséges. |
    
3. A következő képernyőn olvassa el a feltételeket. Ha elfogadja az összes feltételt, kattintson a **Létrehozás** gombra. 

    Az üzembe helyezés néhány percet is igénybe vehet. 

    A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI használatához a 2,6-es vagy újabb verzió szükséges. Ha helyileg futtatja az Azure CLI-t, ellenőrizze, hogy fut-e a verzió: `az --version`

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. Ha helyileg futtatja az Azure CLI-t a portálon való futtatás helyett, be kell jelentkeznie. A parancssorba való bejelentkezéshez futtassa a [login parancsot](/cli/azure/get-started-with-az-cli2):
    
    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat](/cli/azure/account):
    
    ```azurecli
    az account list -o table
    ```

    A következő parancs használatával válassza ki az IoT hub és a DPS-erőforrások létrehozásához használni kívánt előfizetést. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Másolja és illessze be a következő parancsokat a CLI-parancssorba. Ezután hajtsa végre a parancsokat az **ENTER** billentyű lenyomásával.
   
    > [!TIP]
    > A parancsok az erőforráscsoport helyét fogják kérni. Az elérhető helyszínek listáját a parancs első futtatásával tekintheti meg:
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

4. A parancsok a következő információk megadását kérik. Adja meg az értékeket, és nyomja le az **ENTER** billentyűt.

    | Paraméter | Leírás |
    | :-------- | :---------- |
    | **Projektnév** | Ennek a paraméternek az értékét fogja használni a rendszer az összes erőforrás tárolására szolgáló erőforráscsoport létrehozásához. A rendszer hozzáadja a karakterláncot az `rg` erőforráscsoport neve értékének végéhez. |
    | **helyen** | Ez az érték az a régió, ahol az összes erőforrást tárolni fogja. |
    | **iotHubName** | Adja meg annak a IoT Hubnek a nevét, amelynek globálisan egyedinek kell lennie a *. Azure-Devices.net* névtérben. A központi telepítés ellenőrzésekor a következő szakaszban kell megadnia a hub nevét. |
    | **provisioningServiceName** | Adja meg az új eszköz-kiépítési szolgáltatás (DPS) erőforrásának nevét. A névnek globálisan egyedinek kell lennie a *. Azure-Devices-Provisioning.net* névtérben. A telepítés ellenőrzésekor a következő szakaszban a DPS neve szükséges. |

    A AzureCLI a sablon üzembe helyezéséhez használható. Az Azure CLI mellett a Azure PowerShell, a Azure Portal és a REST API is használható. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. A központi telepítés ellenőrzéséhez futtassa az alábbi [parancsot az erőforrások listázásához](/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) , és keresse meg az új kiépítési szolgáltatást és az IoT hubot a kimenetben:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Annak ellenőrzéséhez, hogy a hub már csatolva van-e a DPS-erőforráshoz, futtassa a következő [DPS Extension show parancsot](/cli/azure/iot/dps?view=azure-cli-latest#az_iot_dps_show&preserve-view=true).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Figyelje meg a tag-hez csatolt hubokat `iotHubs` .


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a folytatást, a Azure Portal vagy az Azure CLI használatával törölheti az erőforráscsoportot és annak összes erőforrását.

Ha törölni szeretne egy erőforráscsoportot és a Azure Portal összes erőforrását, egyszerűen nyissa meg az erőforráscsoportot, és kattintson az erőforráscsoport és a felső **törlése** elemre.

Az Azure CLI használatával üzembe helyezett erőforráscsoport törlése:

```azurecli
az group delete --name "${projectName}rg"
```

A Azure Portal, a PowerShell vagy a REST API-k használatával is törölhet erőforráscsoportokat és egyedi erőforrásokat, valamint a Azure Resource Managerhoz vagy IoT Hub Device Provisioning Servicehoz közzétett támogatott Platform SDK-kat is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT hubot és egy eszköz kiépítési szolgáltatási példányát, és összekapcsolta a két erőforrást. Ha szeretné megtudni, hogyan használhatja ezt a telepítőt egy eszköz kiépítéséhez, folytassa a gyors üzembe helyezési útmutatóval az eszköz létrehozásához.

> [!div class="nextstepaction"]
> [Eszköz üzembe helyezésének rövid útmutatója](./quick-create-simulated-device-symm-key.md)

