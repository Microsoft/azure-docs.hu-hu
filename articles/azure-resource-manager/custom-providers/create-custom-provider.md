---
title: Erőforrás-szolgáltató létrehozása
description: Útmutatás erőforrás-szolgáltató létrehozásához és az egyéni erőforrástípusok üzembe helyezéséhez.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 23ae69f49d37a210dd9fa1bfeedeca6c1b461e20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888861"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Gyors útmutató: egyéni szolgáltató létrehozása és egyéni erőforrások üzembe helyezése

Ebben a rövid útmutatóban létrehoz egy saját erőforrás-szolgáltatót, és üzembe helyezi az adott erőforrás-szolgáltató egyéni erőforrás-típusait. További információ az egyéni szolgáltatókról: az [Azure Custom Providers előzetes](overview.md)verziójának áttekintése.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A rövid útmutató lépéseinek elvégzéséhez meg kell hívnia a `REST` műveleteket. [A REST-kérelmek küldésének különböző módjai](/rest/api/azure/)vannak.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Az Azure CLI-példák `az rest` a `REST` kérelmekhez használhatók. További információ: [az Rest](/cli/azure/reference-index#az-rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- A PowerShell-parancsok helyileg futnak a PowerShell 7 vagy újabb verzió és a Azure PowerShell modulok használatával. További információ: [Install Azure PowerShell](/powershell/azure/install-az-ps).
- Ha még nincs eszköz a `REST` műveletekhez, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Ez egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti a Azure Resource Manager API meghívását.
- A **ARMClient** telepítése után a következő beírásával jelenítheti meg a használati adatokat egy PowerShell-parancssorból: `armclient.exe` . Vagy nyissa meg a [ARMClient wikit](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Egyéni szolgáltató üzembe helyezése

Az egyéni szolgáltató beállításához telepítsen egy [példa sablont](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) az Azure-előfizetésre.

A sablon telepítése után az előfizetése a következő erőforrásokkal rendelkezik:

- Függvényalkalmazás az erőforrásokkal és műveletekkel kapcsolatos műveletekkel.
- Az egyéni szolgáltatón keresztül létrehozott felhasználók tárolására szolgáló Storage-fiók.
- Egyéni erőforrás-típusokat és műveleteket definiáló egyéni szolgáltató. A függvény alkalmazás-végpontot használja a kérelmek küldéséhez.
- Egyéni erőforrás az egyéni szolgáltatótól.

Az egyéni szolgáltató üzembe helyezéséhez használja az Azure CLI-t, a PowerShellt vagy a Azure Portal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ez a példa arra kéri, hogy adjon meg egy erőforráscsoportot, egy helyet és egy szolgáltató Function alkalmazásának nevét. A nevek a más parancsokban használt változók szerint vannak tárolva. Az az [Group Create](/cli/azure/group#az-group-create) és az [Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create) parancsok telepítik az erőforrásokat.

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

Ez a példa arra kéri, hogy adjon meg egy erőforráscsoportot, egy helyet és egy szolgáltató Function alkalmazásának nevét. A nevek a más parancsokban használt változók szerint vannak tárolva. A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) és a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsok telepítik az erőforrásokat.

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

A megoldás a Azure Portal is üzembe helyezhető. Válassza az **üzembe helyezés az Azure** -ban gombot a sablon megnyitásához a Azure Portalban.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Egyéni szolgáltató és erőforrás megtekintése

A portálon az egyéni szolgáltató egy rejtett erőforrástípus. Az erőforrás-szolgáltató üzembe helyezésének megerősítéséhez navigáljon az erőforráscsoporthoz. Válassza a **rejtett típusok megjelenítésének** lehetőségét.

![Rejtett erőforrástípusok megjelenítése](./media/create-custom-provider/show-hidden.png)

A telepített egyéni erőforrástípus megtekintéséhez használja a `GET` műveletet az erőforrás-típuson.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

A válasz a következőket kapja:

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

A válasz a következőket kapja:

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

Az egyéni szolgáltató is rendelkezik egy nevű művelettel `ping` . A kérést feldolgozó kód implementálva van a Function alkalmazásban. A `ping` művelet egy üdvözléssel válaszol.

Kérelem küldéséhez `ping` használja a műveletet az `POST` Egyéni szolgáltatón.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

A válasz a következőket kapja:

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

A válasz a következőket kapja:

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

Az egyéni erőforrástípus létrehozásához üzembe helyezheti az erőforrást egy sablonban. Ez a megközelítés az ebben a rövid útmutatóban üzembe helyezett sablonban látható. `PUT`Az erőforrás típusára vonatkozó kérelmet is küldhet.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

A válasz a következőket kapja:

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

A válasz a következőket kapja:

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

Az egyéni [szolgáltatói](/cli/azure/ext/custom-providers/custom-providers/resource-provider) parancsok használatával dolgozhat az egyéni erőforrás-szolgáltatóval.

### <a name="list-custom-resource-providers"></a>Egyéni erőforrás-szolgáltatók listázása

A `list` parancs használatával jelenítheti meg az összes egyéni erőforrás-szolgáltatót egy előfizetésben. Az alapértelmezett érték listázza az aktuális előfizetés egyéni erőforrás-szolgáltatóit, vagy megadhatja a `--subscription` paramétert. Egy erőforráscsoport listázásához használja a (z `--resource-group` ) paramétert.

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

### <a name="show-the-properties"></a>Tulajdonságok megjelenítése

A `show` parancs használatával jelenítse meg az egyéni erőforrás-szolgáltató tulajdonságait. A kimeneti formátum a `list` kimenethez hasonlít.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Új erőforrás létrehozása

`create`Egyéni erőforrás-szolgáltató létrehozásához vagy frissítéséhez használja az parancsot. Ez a példa frissíti a `actions` és a-t `resourceTypes` .

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

### <a name="update-the-providers-tags"></a>A szolgáltató címkéinak frissítése

A `update` parancs csak egy egyéni erőforrás-szolgáltató címkéit frissíti. A Azure Portal az egyéni erőforrás-szolgáltató app Service megjeleníti a címkét.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Egyéni erőforrás-szolgáltató törlése

A `delete` parancs felszólítja, és csak az egyéni erőforrás-szolgáltatót törli. A Storage-fiók, az App Service és az App Service-csomag nem törlődik. A szolgáltató törlése után a rendszer visszaadja a parancsot egy parancssorba.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Következő lépések

Az egyéni szolgáltatók bevezetését a következő cikk ismerteti:

> [!div class="nextstepaction"]
> [Az Azure Custom Providers előzetes verziójának áttekintése](overview.md)
