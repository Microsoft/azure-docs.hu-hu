---
title: Webalkalmazás kiépítése az Azure cache Redis
description: Azure Resource Manager sablonnal üzembe helyezheti a webalkalmazást az Azure cache használatával a Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: ec8d4f5611425734974d07ae6ee7008b10b9b406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833774"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Webalkalmazás és Azure cache létrehozása a Redis sablon használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a témakörben megtudhatja, hogyan hozhat létre olyan Azure Resource Manager-sablont, amely egy Azure-webalkalmazást telepít az Azure cache Redis. Megtudhatja, hogyan határozhatja meg, hogy mely erőforrások legyenek telepítve, és Hogyan határozható meg a központi telepítés végrehajtásakor megadott paraméterek. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

További információ a sablonok létrehozásáról: [Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md)készítése. A gyorsítótár-erőforrástípusok JSON-szintaxisának és-tulajdonságainak megismeréséhez tekintse meg a [Microsoft. cache típusú erőforrástípusok](/azure/templates/microsoft.cache/allversions)című témakört.

A teljes sablon: [webalkalmazás az Azure cache for Redis sablonban](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Üzembe helyezés
Ebben a sablonban a következőket fogja telepíteni:

* Azure-webalkalmazás
* Azure Cache for Redis

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Megadható paraméterek
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Nevekre vonatkozó változók
Ez a sablon változók használatával hozza létre az erőforrások nevét. A [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) függvény használatával hoz létre értéket az erőforráscsoport azonosítója alapján.

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
Létrehozza az Azure cache-t a webalkalmazáshoz használt Redis. A gyorsítótár neve a **cacheName** változóban van megadva.

A sablon ugyanazon a helyen hozza létre a gyorsítótárat, mint az erőforráscsoportot.

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
Létrehozza a **webSiteName** változóban megadott nevű webalkalmazást.

Figyelje meg, hogy a webalkalmazás az Alkalmazásbeállítások tulajdonságaival van konfigurálva, amelyek lehetővé teszik, hogy együttműködjön a Redis készült Azure cache-sel. Az alkalmazás beállításai dinamikusan jönnek létre az üzembe helyezés során megadott értékek alapján.

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
