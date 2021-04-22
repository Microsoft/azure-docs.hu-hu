---
title: Erőforrás-szolgáltató létrehozása
description: Ismerteti, hogyan hozhat létre erőforrás-szolgáltatót, és hogyan helyezheti üzembe az egyéni erőforrástípusokat.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 915856e3f9875c0a3318bc19138eaae8742bf72f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871740"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Rövid útmutató: Egyéni szolgáltató létrehozása és egyéni erőforrások üzembe helyezése

Ebben a rövid útmutatóban saját erőforrás-szolgáltatót hoz létre, és egyéni erőforrástípusokat helyez üzembe ehhez az erőforrás-szolgáltatóhoz. További információ az egyéni szolgáltatókról: [Azure Custom Providers Preview overview (Az Azure Custom Providers előzetes verzió áttekintése).](overview.md)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az ebben a rövid útmutatóban található lépések végrehajtásához meg kell hívnia a `REST` műveleteket. A [REST-kérések küldésének különböző módjai vannak.](/rest/api/azure/)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Az Azure CLI-példák `az rest` `REST` kérések esetén használhatók. További információ: [az rest](/cli/azure/reference-index#az_rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- A PowerShell-parancsok helyileg futnak a PowerShell 7-es vagy újabb, valamint a Azure PowerShell modulokkal. További információ: [Install Azure PowerShell.](/powershell/azure/install-az-ps)
- Ha még nem rendelkezik műveleti `REST` eszközzel, telepítse az [ARMClient eszközt.](https://github.com/projectkudu/ARMClient) Ez egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti a Azure Resource Manager API hívását.
- Az **ARMClient telepítése** után a következő beírásával jelenítheti meg egy PowerShell-parancssor használati adatait: `armclient.exe` . Vagy ugrás az [ARMClient wikire.](https://github.com/projectkudu/ARMClient/wiki)

---

## <a name="deploy-custom-provider"></a>Egyéni szolgáltató üzembe helyezése

Az egyéni szolgáltató beállításához üzembe kell helyeznie egy [példasablont](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) az Azure-előfizetésben.

A sablon üzembe helyezése után az előfizetés a következő erőforrásokkal rendelkezik:

- Függvényalkalmazás az erőforrások és műveletek műveleteivel.
- Tárfiók az egyéni szolgáltatón keresztül létrehozott felhasználók tárolásához.
- Egyéni szolgáltató, amely meghatározza az egyéni erőforrástípusokat és műveleteket. A függvényalkalmazás végpontját használja a kérések küldésére.
- Egyéni erőforrás az egyéni szolgáltatótól.

Az egyéni szolgáltató üzembe helyezéséhez használja az Azure CLI-t, a PowerShellt vagy a Azure Portal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ez a példa arra kéri, hogy adja meg az erőforráscsoportot, a helyet és a szolgáltató függvényalkalmazásának nevét. A nevek más parancsokban használt változókban vannak tárolva. Az [az group create és](/cli/azure/group#az_group_create) az az deployment group create [parancsok](/cli/azure/deployment/group#az_deployment_group_create) üzembe helyezik az erőforrásokat.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a példa arra kéri, hogy adja meg az erőforráscsoportot, a helyet és a szolgáltató függvényalkalmazásának nevét. A nevek más parancsokban használt változókban vannak tárolva. A [New-AzResourceGroup és](/powershell/module/az.resources/new-azresourcegroup) [a New-AzResourceGroupDeployment parancsok](/powershell/module/az.resources/new-azresourcegroupdeployment) üzembe helyezik az erőforrásokat.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

A megoldást az alkalmazásból is Azure Portal. Az Üzembe **helyezés az Azure-ban** gombra kattintva nyissa meg a sablont a Azure Portal.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Egyéni szolgáltató és erőforrás megtekintése

A portálon az egyéni szolgáltató egy rejtett erőforrástípus. Az erőforrás-szolgáltató üzembe helyezésének megerősítéséhez lépjen az erőforráscsoporthoz. Válassza a Rejtett **típusok megjelenítése lehetőséget.**

![Rejtett erőforrástípusok megjelenítése](./media/create-custom-provider/show-hidden.png)

Az üzembe helyezett egyéni erőforrástípust az erőforrástípuson `GET` a művelettel láthatja.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

A következő választ kapja:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

A következő választ kapja:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Hívási művelet

Az egyéni szolgáltatónak van egy nevű művelete `ping` is. A kérést feldolgozó kód a függvényalkalmazásban van megvalósítva. A `ping` művelet egy üdvözléssel válaszol.

Kérés `ping` elküldését az egyéni `POST` szolgáltatóra vonatkozó művelettel küldheti el.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

A következő választ kapja:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

A következő választ kapja:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Erőforrástípus létrehozása

Az egyéni erőforrástípus létrehozásához üzembe helyezheti az erőforrást egy sablonban. Ez a megközelítés az ebben a rövid útmutatóban üzembe helyezett sablonban látható. Az erőforrástípusra `PUT` vonatkozó kérést is küldhet.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

A következő választ kapja:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

A következő választ kapja:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Egyéni erőforrás-szolgáltatói parancsok

Az [egyéni erőforrás-szolgáltatóval való](/cli/azure/custom-providers/resource-provider) munkához használja a custom-providers parancsokat.

### <a name="list-custom-resource-providers"></a>Egyéni erőforrás-szolgáltatók listása

Az `list` paranccsal jelenítse meg az előfizetésben az összes egyéni erőforrás-szolgáltatót. Az alapértelmezett érték az aktuális előfizetés egyéni erőforrás-szolgáltatóit listázza, vagy megadhatja a `--subscription` paramétert. Egy erőforráscsoport listához használja a `--resource-group` paramétert.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>A tulajdonságok megjelenítése

Az `show` paranccsal jelenítse meg az egyéni erőforrás-szolgáltató tulajdonságait. A kimeneti formátum a kimenethez `list` hasonló.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Új erőforrás létrehozása

Az `create` paranccsal hozzon létre vagy frissítsen egy egyéni erőforrás-szolgáltatót. Ez a példa frissíti a és `actions` a `resourceTypes` et.

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>A szolgáltató címkéinek frissítése

A `update` parancs csak egy egyéni erőforrás-szolgáltató címkéit frissíti. A Azure Portal az egyéni erőforrás-szolgáltató App Service-szolgáltatása megjeleníti a címkét.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató törlése

A `delete` parancs felszólítja, és csak az egyéni erőforrás-szolgáltatót törli. A tárfiók, az App Service és az App Service-csomag nem törlődik. A szolgáltató törlése után a rendszer visszaküldi a parancssorba.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Következő lépések

Az egyéni szolgáltatók bemutatásáért tekintse meg a következő cikket:

> [!div class="nextstepaction"]
> [Az Azure Custom Providers előzetes verzió áttekintése](overview.md)
