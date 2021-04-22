---
title: Rövid útmutató – Logikaialkalmazás-munkafolyamat létrehozása és üzembe helyezése Azure Resource Manager sablonokkal
description: Logikai alkalmazás létrehozása és üzembe helyezése Azure Resource Manager sablonokkal
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 04/01/2021
ms.openlocfilehash: a177cf48c479f2e2afb0d6d23b1f4695ed57cfe4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875610"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Rövid útmutató: Logikaialkalmazás-munkafolyamat létrehozása és üzembe helyezése ARM-sablonnal

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) egy felhőszolgáltatás, amely több száz összekötőt választva segít olyan automatizált munkafolyamatok létrehozásában és futtatásában, amelyek adatokat, alkalmazásokat, felhőalapú szolgáltatásokat és helyszíni rendszereket [integrálnak.](/connectors/connector-reference/connector-reference-logicapps-connectors) Ez a rövid útmutató az Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát összpontosítja egy alapszintű logikai alkalmazás létrehozásához, amely óránkénti ütemezés szerint ellenőrzi az Azure állapotát. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes Azure-fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Ez a rövid útmutató [**a Logikai**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) alkalmazás létrehozása sablont használja, amely az [Azure](https://azure.microsoft.com/resources/templates) gyorsindítási sablonok katalógusában található, de itt nem látható. Ehelyett áttekintheti a gyorsindítási sablon ["fájlon azuredeploy.js"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) elemét a sablonkatatárban.

A gyorsindítási sablon létrehoz egy logikaialkalmazás-munkafolyamatot, amely az Ismétlődés eseményindítót használja, amely óránkénti futtatásra van beállítva, valamint egy beépített HTTP-műveletet, amely egy [ *URL-címet*](../connectors/built-in.md)hív meg, amely az Azure állapotát adja vissza. A beépített művelet natív a Azure Logic Apps platformon.

Ez a sablon a következő Azure-erőforrást hozza létre:

* [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows), amely egy logikai alkalmazás munkafolyamatát hozza létre.

Ha további gyorsindítási sablonokat Azure Logic Apps, tekintse át a katalógusBan található [Microsoft.Logic-sablonokat.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic)

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a gyorsindítási sablon telepítéséhez használni kívánt beállítást:

| Beállítás | Leírás |
|--------|-------------|
| [Azure Portal](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Ha az Azure-környezet megfelel az előfeltételeknek, és ismeri az ARM-sablonok használatának menetét, ezekkel a lépésekkel közvetlenül bejelentkezhet az Azure-ba, és megnyithatja a gyorsindítási sablont a Azure Portal. További információ: Erőforrások üzembe [helyezése ARM-sablonokkal és Azure Portal.](../azure-resource-manager/templates/deploy-portal.md) |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Az Azure parancssori felület (Azure CLI) az Azure-erőforrások létrehozására és kezelésére szolgáló parancsok készlete. A parancsok futtatásához az Azure CLI 2.6-os vagy újabb verziójára lesz szükség. A CLI verziójának ellenőrzéshez írja be a következőt: `az --version` . További információt az alábbi témakörökben talál: <p><p>- [Mi az az Azure CLI?](/cli/azure/what-is-azure-cli) <br>- [Az Azure CLI használatának első lépések](/cli/azure/get-started-with-azure-cli) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az Azure Resource Manager modellt használják az Azure-erőforrások kezeléséhez. További információt az alábbi témakörökben talál: <p><p>- [Azure PowerShell áttekintés](/powershell/azure/azurerm/overview) <br>- [Az Azure PowerShell Az modul bevezetése](/powershell/azure/new-azureps-module-az) <br>- [Első lépések a Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure Resource Management REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Az Azure Representational State Transfer (REST) API-kat biztosít, amelyek olyan szolgáltatásvégpontokat tartalmaznak, amelyek támogatják a szolgáltatás-erőforrásokhoz való hozzáférés létrehozásához, lekéréséhez, frissítéséhez vagy törléséhez használt HTTP-műveleteket (metódusokat). További információ: [Az Azure REST API.](/rest/api/azure/) |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza ki a következő képet az Azure-fiókjával való bejelentkezéshez, és nyissa meg a gyorsindítási sablont a Azure Portal:

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. A portálon a Logikai alkalmazás létrehozása sablon használatával lapon adja meg vagy válassza ki **a** következő értékeket:

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés neve*> | A használni használt Azure-előfizetés neve |
   | **Erőforráscsoport** | <*Azure-erőforráscsoport neve*> | Egy új vagy meglévő Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   | **Régió** | <*Azure-régió*> | A logikai alkalmazás használatára használt Azure-adatközpont régiója. Ez a példa a következőt használja: `West US`. |
   | **Logikai alkalmazás neve** | <*logic-app-name*> | A logikai alkalmazáshoz használt név. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
   | **URI tesztelése** | <*test-URI*> | A szolgáltatás által egy adott ütemezés alapján hívott URI. Ez a példa `https://status.azure.com/en-us/status/` a et használja, amely az Azure állapotlapja. |
   | **Hely** |  <*Azure-régió az összes erőforráshoz*> | Az összes erőforráshoz használni szükséges Azure-régió, ha eltér az alapértelmezett értéktől. Ez a példa az alapértelmezett értéket () `[resourceGroup().location]` használja, amely az erőforráscsoport helye. |
   ||||

   Az oldal így néz ki az ebben a példában használt értékekkel:

   ![Adja meg a gyorsindítási sablon adatait](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Ha végzett, válassza az Áttekintés **+ létrehozás lehetőséget.**

1. Folytassa az Üzembe helyezett erőforrások [áttekintése lépésekkel.](#review-deployed-resources)

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

* [Azure CLI: az deployment group](/cli/azure/deployment/group)
* [Erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-val](../azure-resource-manager/templates/deploy-cli.md)

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

1. Ha nem szeretne meglévő Azure-erőforráscsoportot használni, hozzon létre egy új erőforráscsoportot. Ehhez a szintaxist kell követnie a Resource Management-erőforráscsoportnak küldött REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Érték | Leírás |
   |-------|-------------|
   | `subscriptionId`| A használni kívánt Azure-előfizetés GUID-ja |
   | `resourceGroupName` | A létrehozni szükséges Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   |||

   Például:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   További információt az alábbi témakörökben talál:

   * [Azure REST API-referencia – Az Azure REST API-k meghívása](/rest/api/azure/)
   * [Erőforrás-REST API: Erőforráscsoportok – Létrehozás vagy frissítés.](/rest/api/resources/resourcegroups/createorupdate)

1. A gyorsindítási sablon erőforráscsoportban való üzembe helyezéséhez kövesse az alábbi szintaxist a Resource Management-erőforráscsoportnak küldött REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Érték | Leírás |
   |-------|-------------|
   | `subscriptionId`| A használni kívánt Azure-előfizetés GUID-ja |
   | `resourceGroupName` | A használni használt Azure-erőforráscsoport neve. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
   | `deploymentName` | Az üzemelő példányhoz használni szükséges név. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
   |||

   Például:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   További információ: [Resource Management REST API: Üzemelő példányok – Létrehozás vagy frissítés.](/rest/api/resources/deployments/createorupdate)

1. Az üzembe helyezéshez használni kívánt értékek , például az Azure-régió, [](../azure-resource-manager/templates/template-parameters.md)valamint a gyorsindítási sablonra és a paraméterfájlra mutató hivatkozások megadva, amely az üzembe helyezéskor használni kívánt gyorsindítási sablon értékeit tartalmazza, kövesse az alábbi szintaxist a Resource Management-erőforráscsoportnak küldött kérelem törzséhez REST API:

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
   | `location`| <*Azure-régió*> | Az üzembe helyezéshez használt Azure-régió. Ez a példa a következőt használja: `West US`. |
   | `templateLink` : `uri` | <*quickstart-template-URL*> | A gyorsindítási sablon üzembe helyezéshez használt URL-címe: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*quickstart-template-parameter-file-URL*> | A gyorsindítási sablon üzembe helyezéshez használt paraméterfájljának URL-címe: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>A paraméterfájllal kapcsolatos további Resource Manager az alábbi témakörökben található: <p><p>- [Paraméterfájl Resource Manager létrehozása](../azure-resource-manager/templates/parameter-files.md) <br>- [Oktatóanyag: Paraméterfájlok használata az ARM-sablon üzembe helyezéséhez](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*üzembe helyezési mód*> | Futtatassa a növekményes frissítést vagy a teljes frissítést. Ez a példa `Incremental` a értéket használja, amely az alapértelmezett érték. További információkért lásd az [üzembehely Azure Resource Manager módokat.](../azure-resource-manager/templates/deployment-modes.md) |
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
* [Erőforrások üzembe helyezése ARM-sablonokkal és Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

A logikai alkalmazás megtekintéséhez használhatja a Azure Portal, futtathat egy, az Azure CLI vagy a Azure PowerShell használatával létrehozott szkriptet, vagy használhatja a Logic App REST API.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresőmezőbe írja be a logikai alkalmazás nevét, amely ebben a `Check-Azure-Status-LA` példában található. Az eredmények listájából válassza ki a logikai alkalmazást.

1. A Azure Portal keresse meg és válassza ki a logikai alkalmazást, amely ebben a `Check-Azure-Status-RG` példában található.

1. Amikor megnyílik a Logikaialkalmazás-tervező, tekintse át a gyorsindítási sablon által létrehozott logikai alkalmazást.

1. A logikai alkalmazás teszteléséhez a tervező eszköztárán válassza a **Futtatás lehetőséget.**

### <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

További információ: [Azure CLI: az logic workflow show](/cli/azure/logic/workflow#az_logic_workflow_show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

További információ: [Azure PowerShell Get-AzLogicApp.](/powershell/module/az.logicapp/get-azlogicapp)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Érték | Leírás |
|-------|-------------|
| `subscriptionId`| Annak az Azure-előfizetésnek a GUID-ja, amelyben üzembe helyezette a gyorsindítási sablont. |
| `resourceGroupName` | Annak az Azure-erőforráscsoportnak a neve, amelyben üzembe helyezette a gyorsindítási sablont. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
| `workflowName` | Az üzembe helyezett logikai alkalmazás neve. Ez a példa a következőt használja: `Check-Azure-Status-LA`. |
|||

Például:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

További információ: [Logic Apps REST API: Munkafolyamatok – Bej.](/rest/api/logic/workflows/get)

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő rövid útmutatóval és oktatóanyagokkal dolgozik tovább, érdemes megtartani ezeket az erőforrásokat. Ha már nincs szüksége a logikai alkalmazásra, törölje az erőforráscsoportot a Azure Portal, az Azure CLI, Azure PowerShell vagy a Resource Management REST API.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Azure Portal keresse meg és válassza ki a törölni kívánt erőforráscsoportot, amely ebben a `Check-Azure-Status-RG` példában található.

1. Az erőforráscsoport menüjében válassza az **Áttekintés lehetőséget,** ha még nincs kiválasztva. Az áttekintési oldalon válassza az **Erőforráscsoport törlése lehetőséget.**

1. A megerősítéshez adja meg az erőforráscsoport nevét.

További információ: [Erőforráscsoport törlése.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)

### <a name="cli"></a>[Parancssori felület](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

További információ: [Azure CLI: az group delete](/cli/azure/group#az_group_delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

További információ: [Azure PowerShell Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Érték | Leírás |
|-------|-------------|
| `subscriptionId`| Annak az Azure-előfizetésnek a GUID-ja, ahol üzembe helyezett egy gyorsindítási sablont. |
| `resourceGroupName` | Annak az Azure-erőforráscsoportnak a neve, amelyben üzembe helyezett egy gyorsindítási sablont. Ez a példa a következőt használja: `Check-Azure-Status-RG`. |
|||

Például:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

További információ: [Resource Management REST API: Erőforráscsoportok – Törlés.](/rest/api/resources/resourcegroups/delete)

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
