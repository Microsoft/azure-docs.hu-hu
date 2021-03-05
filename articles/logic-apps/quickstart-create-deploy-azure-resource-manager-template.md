---
title: Rövid útmutató – Logic app-munkafolyamatok létrehozása és üzembe helyezése Azure Resource Manager sablonok használatával
description: Logikai alkalmazások létrehozása és üzembe helyezése Azure Resource Manager-sablonok használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 06/30/2020
ms.openlocfilehash: 8cee79efd9be53ddb9d450f643c04f21ae5e7fca
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213997"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Gyors útmutató: logikai alkalmazások munkafolyamatainak létrehozása és üzembe helyezése ARM-sablon használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) egy felhőalapú szolgáltatás, amellyel olyan automatizált munkafolyamatokat hozhat létre és futtathat, amelyek az adatok, az alkalmazások, a felhőalapú szolgáltatások és a helyszíni rendszerek integrálását teszik lehetővé [több száz összekötő](/connectors/connector-reference/connector-reference-logicapps-connectors)kiválasztásával. Ez a rövid útmutató egy Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát tárgyalja egy olyan alapszintű logikai alkalmazás létrehozásához, amely az Azure-t óránkénti időrendben ellenőrzi. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>A sablon áttekintése

Ez a rövid útmutató a [**logikai alkalmazás létrehozása**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) sablont használja, amelyet az Azure rövid útmutató [sablonjai](https://azure.microsoft.com/resources/templates) között talál, de túl hosszú az itt való megjelenítéshez. Ehelyett áttekintheti a gyors üzembe helyezési sablon ["azuredeploy.jsfájlján"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) című részét a sablonok galériában.

A gyors üzembe helyezési sablon egy olyan logikai alkalmazás-munkafolyamatot hoz létre, amely az ismétlődési eseményindítót használja, amely óránkénti futásra van beállítva, valamint egy HTTP [ *beépített* művelet](../connectors/apis-list.md#connector-types), amely egy olyan URL-címet hív meg, amely az Azure állapotát adja vissza. A beépített művelet natív a Azure Logic Apps platformon.

Ez a sablon a következő Azure-erőforrást hozza létre:

* [**Microsoft. Logic/munkafolyamatok**](/azure/templates/microsoft.logic/workflows), amely létrehoz egy logikai alkalmazás munkafolyamatát.

Ha további rövid útmutató-sablonokat szeretne Azure Logic Appshoz, tekintse át a [Microsoft. Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) sablonokat a katalógusban.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a Gyorsindítás sablon üzembe helyezéséhez használni kívánt beállítást:

| Beállítás | Leírás |
|--------|-------------|
| [Azure Portal](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Ha az Azure-környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, ezek a lépések segítenek közvetlenül az Azure-ba bejelentkezni, és megnyitják a gyors üzembe helyezési sablont a Azure Portal. További információ: [erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Az Azure parancssori felülete (Azure CLI) az Azure-erőforrások létrehozására és kezelésére szolgáló parancsok halmaza. A parancsok futtatásához az Azure CLI 2,6-es vagy újabb verziójára lesz szükség. A CLI verziójának megadásához írja be a következőt: `az --version` . További információt az alábbi témakörökben talál: <p><p>- [Mi az az Azure CLI?](/cli/azure/what-is-azure-cli) <br>- [Ismerkedés az Azure CLI-vel](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az Azure Resource Manager modellt használják az Azure-erőforrások kezeléséhez. További információt az alábbi témakörökben talál: <p><p>- [Azure PowerShell áttekintése](/powershell/azure/azurerm/overview) <br>- [A Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az) <br>- [Ismerkedés a Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure erőforrás-kezelési REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Az Azure olyan reprezentációs állapot-átadási (REST) API-kat biztosít, amelyek olyan szolgáltatási végpontok, amelyek támogatják a szolgáltatás erőforrásaihoz való hozzáférés létrehozására, lekérésére, frissítésére vagy törlésére használt HTTP-műveleteket (metódusokat). További információ: Ismerkedés [Az Azure Rest APIval](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki az alábbi rendszerképet az Azure-fiókjával való bejelentkezéshez, majd nyissa meg a rövid útmutató sablont a Azure Portalban:

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. A portálon a **logikai alkalmazás létrehozása sablon** alapján lapon adja meg vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés – név*> | A használni kívánt Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport-név*> | Egy új vagy meglévő Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   | **Régió** | <*Azure-régió*> | A logikai alkalmazás használatára szolgáló Azure Datacenter-régió. Ez a példa a következőt használja: `West US`. |
   | **Logikai alkalmazás neve** | <*logikai alkalmazás neve*> | A logikai alkalmazáshoz használandó név. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
   | **Teszt URI-ja** | <*teszt-URI*> | Egy adott ütemterv alapján hívni kívánt szolgáltatás URI-ja. Ez a példa `https://status.azure.com/en-us/status/` Az Azure status oldalát használja. |
   | **Hely** |  <*Azure-régió-for-all-erőforrások*> | Az összes erőforráshoz használandó Azure-régió, ha eltér az alapértelmezett értéktől. Ez a példa az alapértelmezett értéket használja, `[resourceGroup().location]` amely az erőforráscsoport helye. |
   ||||

   Itt látható, hogyan néz ki az oldal az ebben a példában használt értékekkel:

   ![Információk megadása a gyors üzembe helyezési sablonhoz](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

1. Folytassa az [üzembe helyezett erőforrások áttekintése](#review-deployed-resources)című témakör lépéseit.

#### <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

További információt az alábbi témakörökben talál:

* [Azure CLI: az üzembe helyezési csoport](/cli/azure/deployment/group)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

További információt az alábbi témakörökben talál:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[REST API](#tab/rest-api)

1. Ha nem szeretne meglévő Azure-erőforráscsoportot használni, hozzon létre egy új erőforráscsoportot az erőforrás-kezelés REST API küldött kérelemhez a következő szintaxissal:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Érték | Leírás |
   |-------|-------------|
   | `subscriptionId`| A használni kívánt Azure-előfizetés GUID azonosítója |
   | `resourceGroupName` | A létrehozandó Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   |||

   Például:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   További információt az alábbi témakörökben talál:

   * [Azure REST API-referenciák – az Azure REST API-k meghívása](/rest/api/azure/)
   * [Erőforrás-kezelési REST API: erőforráscsoportok – létrehozás vagy frissítés](/rest/api/resources/resourcegroups/createorupdate).

1. A gyors üzembe helyezési sablon az erőforráscsoporthoz való telepítéséhez kövesse ezt a szintaxist az erőforrás-kezelés REST API küldött kérelemnél:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Érték | Leírás |
   |-------|-------------|
   | `subscriptionId`| A használni kívánt Azure-előfizetés GUID azonosítója |
   | `resourceGroupName` | A használni kívánt Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   | `deploymentName` | A telepítéshez használandó név. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
   |||

   Például:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   További információ [: erőforrás-kezelés REST API: központi telepítések – létrehozás vagy frissítés](/rest/api/resources/deployments/createorupdate).

1. Ha meg szeretné adni a központi telepítéshez használandó értékeket, például az Azure-régiót, valamint a gyors üzembe helyezési sablonra és a [paraméterre](../azure-resource-manager/templates/template-parameters.md)mutató hivatkozásokat, amelyek a telepítéskor használandó gyorsindítási sablon értékeit tartalmazzák, kövesse ezt a szintaxist az erőforrás-kezelési REST API küldött kérelem törzse számára:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | `location`| <*Azure-régió*> | Az üzembe helyezéshez használandó Azure-régió. Ez a példa a következőt használja: `West US`. |
   | `templateLink` : `uri` | <*gyors útmutató – sablon-URL*> | A központi telepítéshez használandó rövid útmutató sablonjának URL-címe: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*gyors útmutató – sablon-paraméter-fájl-URL*> | A rövid útmutató sablonjának a központi telepítéshez használandó paraméter-fájljának URL-címe: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>További információ a Resource Manager-paraméter fájljáról: <p><p>- [Resource Manager-paraméter fájljának létrehozása](../azure-resource-manager/templates/parameter-files.md) <br>- [Oktatóanyag: paraméterek használata az ARM-sablon üzembe helyezéséhez](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*központi telepítés – üzemmód*> | Futtasson növekményes frissítést, vagy fejezze be a frissítést. Ez a példa `Incremental` az alapértelmezett értéket használja. További információ: [Azure Resource Manager telepítési módok](../azure-resource-manager/templates/deployment-modes.md). |
   |||

   Például:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

További információt az alábbi témakörökben talál:

* [Erőforrás-kezelési REST API](/rest/api/resources/)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Resource Manager-REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A logikai alkalmazás megtekintéséhez használhatja a Azure Portal, futtathatja az Azure CLI-vel vagy Azure PowerShell-vel létrehozott parancsfájlt, vagy használhatja a logikai alkalmazás REST API.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresőmezőbe írja be a logikai alkalmazás nevét, amely ebben a példában szerepel `Check-Azure-Status-LA` . Az eredmények listából válassza ki a logikai alkalmazást.

1. A Azure Portal keresse meg és válassza ki a logikai alkalmazást, amely `Check-Azure-Status-RG` ebben a példában található.

1. A Logic app Designer megnyitása után tekintse át a Gyorsindítás sablon által létrehozott logikai alkalmazást.

1. A logikai alkalmazás teszteléséhez a tervező eszköztárán válassza a **Futtatás** lehetőséget.

### <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

További információt az [Azure CLI: az Logic workflow show](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show)című témakörben talál.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

További információ [: Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Érték | Leírás |
|-------|-------------|
| `subscriptionId`| Annak az Azure-előfizetésnek a GUID azonosítója, amelybe a gyors üzembe helyezési sablont telepítette. |
| `resourceGroupName` | Annak az Azure-erőforráscsoportnak a neve, amelybe a gyors üzembe helyezési sablont telepítette. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
| `workflowName` | A központilag telepített logikai alkalmazás neve. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
|||

Például:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

További információ [: Logic Apps REST API: munkafolyamatok – Get](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, érdemes megtartania ezeket az erőforrásokat. Ha már nincs szüksége a logikai alkalmazásra, törölje az erőforráscsoportot a Azure Portal, az Azure CLI, az Azure PowerShell vagy az erőforrás-kezelés REST API használatával.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresse meg és válassza ki a törölni kívánt erőforráscsoportot, amely `Check-Azure-Status-RG` ebben a példában található.

1. Az erőforráscsoport menüben válassza az **Áttekintés** lehetőséget, ha még nincs bejelölve. Az Áttekintés lapon válassza az **erőforráscsoport törlése** elemet.

1. A megerősítéshez adja meg az erőforráscsoport nevét.

További információ: [erőforráscsoport törlése](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

További információt az [Azure CLI: az Group delete](/cli/azure/group#az-group-delete)című témakörben talál.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

További információt a [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)című témakörben talál.

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Érték | Leírás |
|-------|-------------|
| `subscriptionId`| Annak az Azure-előfizetésnek a GUID azonosítója, amelybe a gyors üzembe helyezési sablont telepítette. |
| `resourceGroupName` | Annak az Azure-erőforráscsoportnak a neve, amelybe a gyors üzembe helyezési sablont telepítette. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
|||

Például:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

További információ [: erőforrás-kezelés REST API: erőforráscsoportok – törlés](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
