---
title: Webalkalmazás üzembe Azure Cache for Redis
description: A Azure Resource Manager webalkalmazás üzembe helyezéséhez a Azure Cache for Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 089d7d7990f0f94135f629a5cd7a461700aa3433
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830771"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Webalkalmazás és Azure Cache for Redis sablon használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebből a témakörből megtudhatja, hogyan hozhat létre olyan Azure Resource Manager, amely üzembe helyez egy Azure-webalkalmazást a Azure Cache for Redis. Megtudhatja, hogyan határozhatja meg az üzembe helyezett erőforrásokat, és hogyan határozhatja meg az üzembe helyezés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

További információ a sablonok létrehozásáról: [Authoring Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md)(Sablonok Azure Resource Manager létrehozása). A gyorsítótár-erőforrástípusok JSON-szintaxisával és tulajdonságaival kapcsolatban lásd: [Microsoft.Cache erőforrástípusok.](/azure/templates/microsoft.cache/allversions)

A teljes sablont lásd: Webalkalmazás Azure Cache for Redis [sablonnal.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)

## <a name="what-you-will-deploy"></a>Az üzembe helyezendő
Ebben a sablonban a következőt fogja üzembe helyezni:

* Azure-webalkalmazás
* Azure Cache for Redis

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Megadhatja a paramétereket
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Változók a nevekhez
Ez a sablon változókat használ az erőforrások nevének felépítéséhez. A [uniqueString függvénnyel](../azure-resource-manager/templates/template-functions-string.md#uniquestring) hoz létre értéket az erőforráscsoport azonosítója alapján.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Létrehozza Azure Cache for Redis webalkalmazással használt sablont. A gyorsítótár neve a **cacheName változóban van megadva.**

A sablon az erőforráscsoporttal azonos helyen hozza létre a gyorsítótárat.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Webalkalmazás
Létrehozza a webalkalmazást a **webSiteName változóban megadott névvel.**

Figyelje meg, hogy a webalkalmazás olyan alkalmazásbeállítási tulajdonságokkal van konfigurálva, amelyek lehetővé teszik a webalkalmazás Azure Cache for Redis. Ezek az alkalmazásbeállítások az üzembe helyezés során megadott értékek alapján dinamikusan létrejönnek.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
