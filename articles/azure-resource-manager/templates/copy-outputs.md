---
title: Egy kimeneti érték több példányának meghatározása
description: A másolási műveletet egy Azure Resource Manager sablonban (ARM-sablon) használva többször is megismételheti, amikor egy központi telepítésből értéket ad vissza.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385734"
---
# <a name="output-iteration-in-arm-templates"></a>Kimeneti iteráció az ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy kimenethez a Azure Resource Manager-sablonban (ARM-sablon). A másolási hurok a sablon kimenetek szakaszába való felvételével több elemet is dinamikusan visszaadhat az üzembe helyezés során.

A másolási hurkot használhatja [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [változókkal](copy-variables.md)is.

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adja hozzá az `copy` elemet a sablon output (kimenet) szakaszához, hogy több elemet adjon vissza. A másolási elem a következő általános formátumú:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

A `count` tulajdonság megadja a kimeneti értékhez használni kívánt ismétlések számát.

A `input` tulajdonság a megismételni kívánt tulajdonságokat adja meg. A tulajdonság értéke alapján létrehozott elemek tömbjét hozza létre `input` . Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

# <a name="bicep"></a>[Bicep](#tab/bicep)

A hurkok használatával számos elemet lehet visszaadni az üzembe helyezés során:

- Iteráció egy tömbben:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Egy tömb elemeinek megismétlése

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Hurok-index használata

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha az Azure CLI, a PowerShell vagy a REST API legújabb verziójával telepíti a sablont, akkor nulla lehet. Pontosabban a következőket kell használnia:

- Azure PowerShell **2,6** vagy újabb
- Azure CLI- **2.0.74** vagy újabb
- REST API **2019-05-10** -es vagy újabb verzió
- A [csatolt központi telepítéseknek](linked-templates.md) a telepítési erőforrástípus **2019-05-10** -es vagy újabb API-verzióját kell használniuk

A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="outputs-iteration"></a>Kimenetek iterációja

Az alábbi példa egy változó számú Storage-fiókot hoz létre, és az egyes Storage-fiókok végpontját adja vissza:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

Az előző sablon egy tömböt ad vissza a következő értékekkel:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

A következő példa az új Storage-fiókok három tulajdonságát adja vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

Az előző példa egy tömböt ad vissza a következő értékekkel:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Következő lépések

- Az oktatóanyag lépéseinek megismeréséhez tekintse meg [az oktatóanyag: több erőforrás-példány létrehozása ARM-sablonokkal](template-tutorial-create-multiple-instances.md)című témakört.
- A másolási hurok egyéb felhasználási módjaiért lásd:
  - [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
  - [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
  - [Változó iteráció az ARM-sablonokban](copy-variables.md)
- Ha szeretne többet megtudni a sablonok részeiről, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című szakaszt.
- A sablon üzembe helyezésének megismeréséhez tekintse meg az [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShellával](deploy-powershell.md)foglalkozó témakört.
