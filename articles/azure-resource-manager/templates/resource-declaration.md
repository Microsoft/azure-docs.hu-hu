---
title: Erőforrások deklarálása a sablonokban
description: Útmutatás a Azure Resource Manager-sablonban (ARM-sablon) telepítendő erőforrások bevallásához.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745649"
---
# <a name="resource-declaration-in-arm-templates"></a>Erőforrás-deklaráció ARM-sablonokban

Ha Azure Resource Manager sablonon keresztül szeretne üzembe helyezni egy erőforrást (ARM-sablon), vegyen fel egy erőforrás-deklarációt. Használja a `resources` tömböt a JSON-sablonhoz vagy a `resource` bicep kulcsszót.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Erőforrás típusának és verziójának beállítása

Ha hozzáad egy erőforrást a sablonhoz, először állítsa be az erőforrástípus és az API-verziót. Ezek az értékek határozzák meg az erőforráshoz elérhető egyéb tulajdonságokat.

Az alábbi példa bemutatja, hogyan állíthatja be egy Storage-fiók erőforrás-típusát és API-verzióját. A példa nem mutatja a teljes erőforrás deklarációját.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

A bicep értéknél meg kell határoznia az erőforrás szimbolikus nevét. Az előző példában a szimbolikus név a (z `sa` ). Bármilyen értéket használhat a szimbolikus név neveként, de nem egyezhet meg a sablonban szereplő másik erőforrással, paraméterrel vagy változóval. A szimbolikus név nem azonos az erőforrás nevével. A szimbolikus név használatával egyszerűen hivatkozhat az erőforrásra a sablon más részeiben.

## <a name="set-resource-name"></a>Erőforrás nevének beállítása

Minden erőforrásnak van neve. Az erőforrás nevének beállításakor ügyeljen az [erőforrásnevek szabályaira és korlátozására](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Hely beállítása

Számos erőforráshoz hely szükséges. Megadhatja, hogy az erőforrásnak szüksége van-e az IntelliSense-vagy [sablon-referencián](/azure/templates/)keresztüli helyre. A következő példa a Storage-fiókhoz használt Location paramétert adja meg.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

További információ: [az erőforrás helyének beállítása az ARM-sablonban](resource-location.md).

## <a name="set-tags"></a>Címkék beállítása

Az üzembe helyezés során címkéket is alkalmazhat az erőforrásokra. A címkék segítségével logikailag rendszerezheti a telepített erőforrásokat. A címkék megadásának különböző módjairól a [ARM-sablonok címkéi](../management/tag-resources.md#arm-templates)című témakörben talál példákat.

## <a name="set-resource-specific-properties"></a>Erőforrás-specifikus tulajdonságok beállítása

Az előző tulajdonságok a legtöbb erőforrástípus általános jellegűek. Ezeknek az értékeknek a beállítása után be kell állítania a központilag telepíteni kívánt erőforrástípus jellemző tulajdonságait.

Az IntelliSense vagy a [sablon hivatkozás](/azure/templates/) használatával határozza meg, hogy mely tulajdonságok érhetők el, és melyek szükségesek. A következő példa egy Storage-fiók hátralévő tulajdonságait állítja be.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Következő lépések

* Az erőforrás feltételesen történő üzembe helyezéséhez lásd: [feltételes üzembe helyezés ARM-sablonokban](conditional-resource-deployment.md).
* Az erőforrás-függőségek beállításával kapcsolatban lásd: [erőforrások üzembe helyezési sorrendjének meghatározása az ARM-sablonokban](define-resource-dependency.md).
