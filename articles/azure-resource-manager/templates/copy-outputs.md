---
title: Egy kimeneti érték több példányának meghatározása
description: A másolási műveletet egy Azure Resource Manager sablonban (ARM-sablon) használva többször is megismételheti, amikor egy központi telepítésből értéket ad vissza.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905944"
---
# <a name="output-iteration-in-arm-templates"></a>Kimeneti iteráció az ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egynél több értéket egy kimenethez a Azure Resource Manager-sablonban (ARM-sablon). Ha hozzáadja az `copy` elemet a sablon outputs (kimenetek) szakaszához, akkor az üzembe helyezés során dinamikusan visszatérhet az elemek számával.

A másolást [erőforrásokkal](copy-resources.md), [erőforrásokkal](copy-properties.md)és [változókkal](copy-variables.md)is elvégezheti.

## <a name="syntax"></a>Syntax

A másolási elem a következő általános formátumú:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

A `count` tulajdonság megadja a kimeneti értékhez használni kívánt ismétlések számát.

A `input` tulajdonság a megismételni kívánt tulajdonságokat adja meg. A tulajdonság értéke alapján létrehozott elemek tömbjét hozza létre `input` . Ez lehet egy tulajdonság (például egy karakterlánc) vagy egy olyan objektum, amely több tulajdonsággal rendelkezik.

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha az Azure CLI, a PowerShell vagy a REST API legújabb verziójával telepíti a sablont, akkor nulla lehet. Pontosabban a következőket kell használnia:

* Azure PowerShell **2,6** vagy újabb
* Azure CLI- **2.0.74** vagy újabb
* REST API **2019-05-10** -es vagy újabb verzió
* A [csatolt központi telepítéseknek](linked-templates.md) a telepítési erőforrástípus **2019-05-10** -es vagy újabb API-verzióját kell használniuk

A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

## <a name="outputs-iteration"></a>Kimenetek iterációja

Az alábbi példa egy változó számú Storage-fiókot hoz létre, és az egyes Storage-fiókok végpontját adja vissza:

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

Az előző sablon egy tömböt ad vissza a következő értékekkel:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

A következő példa az új Storage-fiókok három tulajdonságát adja vissza.

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

* Az oktatóanyag lépéseinek megismeréséhez tekintse meg [az oktatóanyag: több erőforrás-példány létrehozása ARM-sablonokkal](template-tutorial-create-multiple-instances.md)című témakört.
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
  * [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
  * [Változó iteráció az ARM-sablonokban](copy-variables.md)
* Ha szeretne többet megtudni a sablonok részeiről, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című szakaszt.
* A sablon üzembe helyezésének megismeréséhez tekintse meg az [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShellával](deploy-powershell.md)foglalkozó témakört.
