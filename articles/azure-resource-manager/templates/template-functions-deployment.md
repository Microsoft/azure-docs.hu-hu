---
title: Sablon functions – üzembe helyezés
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti az üzembe helyezési adatok lekéréséhez.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a9a073284c62efac4e77f8f9b35e8730c350e5f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722721"
---
# <a name="deployment-functions-for-arm-templates"></a>Az ARM-sablonok üzembe helyezési funkciói

A Resource Manager a következő függvényeket biztosítja a Azure Resource Manager-sablon (ARM-sablon) aktuális üzembe helyezéséhez kapcsolódó értékek beolvasásához:

* [telepítési](#deployment)
* [környezet](#environment)
* [paraméterek](#parameters)
* [változók](#variables)

Erőforrások, erőforráscsoportok vagy előfizetések értékeinek lekéréséhez lásd: [Resource functions](template-functions-resource.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="deployment"></a>üzembe helyezés

`deployment()`

A jelenlegi telepítési műveletre vonatkozó adatokat adja vissza.

### <a name="return-value"></a>Visszatérési érték

Ez a függvény az üzembe helyezés során átadott objektumot adja vissza. A visszaadott objektum tulajdonságai eltérnek attól függően, hogy:

* sablon vagy sablon üzembe helyezése spec.
* olyan sablon telepítése, amely helyi fájl, vagy olyan sablon központi telepítése, amely egy URI-n keresztül elért távoli fájl.
* egy erőforráscsoport üzembe helyezése vagy az egyik másik hatókör ([Azure-előfizetés](deploy-to-subscription.md), [felügyeleti csoport](deploy-to-management-group.md)vagy [bérlő](deploy-to-tenant.md)) üzembe helyezése.

Helyi sablon erőforráscsoporthoz való telepítésekor: a függvény a következő formátumot adja vissza:

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Távoli sablon erőforráscsoporthoz való telepítésekor: a függvény a következő formátumot adja vissza:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

A sablon specifikációjának erőforráscsoporthoz való telepítésekor: a függvény a következő formátumot adja vissza:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Ha Azure-előfizetésre, felügyeleti csoportra vagy bérlőre végez üzembe helyezést, a Return objektum tartalmaz egy `location` tulajdonságot. Helyi sablon vagy külső sablon telepítésekor a Location tulajdonság is szerepel. A formátum:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>Megjegyzések

A központi telepítés () használatával egy másik sablonra lehet hivatkozni a fölérendelt sablon URI-ja alapján.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var sharedTemplateUrl = uri(deployment().prperties.templateLink.uri, 'shared-resources.json')
```

---

Ha a portálon lévő központi telepítési előzményekből telepít újra egy sablont, a sablon helyi fájlként lesz telepítve. A `templateLink` tulajdonságot a rendszer nem adja vissza a telepítési függvényben. Ha a sablon `templateLink` egy másik sablonra mutató hivatkozást hoz létre, ne használja a portált az újbóli üzembe helyezéshez. Ehelyett használja a sablon eredeti üzembe helyezéséhez használt parancsokat.

### <a name="example"></a>Példa

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) a központi telepítési objektumot adja vissza:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "deploymentOutput": {
      "type": "object",
      "value": "[deployment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output deploymentOutput object = deployment()
```

---

Az előző példa a következő objektumot adja vissza:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>környezet

`environment()`

Információt ad vissza az üzembe helyezéshez használt Azure-környezetről.

### <a name="return-value"></a>Visszatérési érték

Ez a függvény az aktuális Azure-környezet tulajdonságait adja vissza. Az alábbi példa a globális Azure tulajdonságait mutatja be. A szuverén felhők némileg eltérő tulajdonságokat adhatnak vissza.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Példa

A következő példában szereplő sablon a környezeti objektumot adja vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "environmentOutput": {
      "type": "object",
      "value": "[environment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output environmentOutput object = environment()
```

---

Az előző példa a következő objektumot adja vissza a globális Azure-ba való üzembe helyezéskor:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Egy paraméter értékét adja vissza. A megadott paraméter nevét meg kell adni a sablon paraméterek szakaszában.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |sztring |A visszaadni kívánt paraméter neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott paraméter értéke.

### <a name="remarks"></a>Megjegyzések

Általában paraméterekkel állíthatja be az erőforrás-értékeket. A következő példa beállítja a webhely nevét az üzembe helyezés során átadott paraméterérték értékére.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string

resource mySite 'Microsoft.Web/Sites@2016-08-01' = {
  name: siteName
  ...
}
```

---

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) a Parameters függvény egyszerűsített használatát mutatja be.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    },
    "crossParameter": {
      "type": "string",
      "defaultValue": "[parameters('stringParameter')]"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "stringOutput": {
      "value": "[parameters('stringParameter')]",
      "type": "string"
    },
    "intOutput": {
      "value": "[parameters('intParameter')]",
      "type": "int"
    },
    "objectOutput": {
      "value": "[parameters('objectParameter')]",
      "type": "object"
    },
    "arrayOutput": {
      "value": "[parameters('arrayParameter')]",
      "type": "array"
    },
    "crossOutput": {
      "value": "[parameters('crossParameter')]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringParameter string = 'option 1'
param intParameter int = 1
param objectParameter object = {
  'one': 'a'
  'two': 'b'
}
param arrayParameter array = [
  1
  2
  3
]
param crossParameter string = stringParameter

output stringOutput string = stringParameter
output intOutput int = intParameter
output objectOutput object = objectParameter
output arrayOutput array = arrayParameter
output crossOutput string = crossParameter
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Sztring | 1. lehetőség |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"One": "a", "kettő": "b"} |
| arrayOutput | Tömb | [1, 2, 3] |
| crossOutput | Sztring | 1. lehetőség |

A paraméterek használatával kapcsolatos további információkért lásd: [paraméterek az ARM-sablonokban](template-parameters.md).

## <a name="variables"></a>változók

`variables(variableName)`

A változó értékét adja vissza. A megadott változó nevét meg kell adni a sablon változók szakaszában.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| variableName |Igen |Sztring |A visszaadni kívánt változó neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott változó értéke.

### <a name="remarks"></a>Megjegyzések

Általában változók használatával egyszerűsítheti a sablont úgy, hogy csak egyszer hozza létre az összetett értékeket. A következő példa egy egyedi nevet hoz létre egy Storage-fiókhoz.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  ...
}

resource myVm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

---

### <a name="example"></a>Példa

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) eltérő változó értékeket ad vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "var1": "myVariable",
    "var2": [ 1, 2, 3, 4 ],
    "var3": "[ variables('var1') ]",
    "var4": {
      "property1": "value1",
      "property2": "value2"
    }
  },
  "resources": [],
  "outputs": {
    "exampleOutput1": {
      "value": "[variables('var1')]",
      "type": "string"
    },
    "exampleOutput2": {
      "value": "[variables('var2')]",
      "type": "array"
    },
    "exampleOutput3": {
      "value": "[variables('var3')]",
      "type": "string"
    },
    "exampleOutput4": {
      "value": "[variables('var4')]",
      "type": "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var var1 = 'myVariable'
var var2 = [
  1
  2
  3
  4
]
var var3 = var1
var var4 = {
  'property1': 'value1'
  'property2': 'value2'
}

output exampleOutput1 string = var1
output exampleOutput2 array = var2
output exampleOutput3 string = var3
output exampleOutput4 object = var4
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| exampleOutput1 | Sztring | myVariable |
| exampleOutput2 | Tömb | [1, 2, 3, 4] |
| exampleOutput3 | Sztring | myVariable |
| exampleOutput4 |  Objektum | {"Tulajdonság1": "érték1", "property2": "érték2"} |

A változók használatával kapcsolatos további információkért lásd: [változók az ARM-sablonban](template-variables.md).

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
