---
title: Sablon függvények – numerikus
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti a számok használatához.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: f3687581d94f80cc923614a0655da1813bd5c97b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359710"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM-sablonok numerikus függvények

A Resource Manager a következő függvényeket biztosítja a Azure Resource Manager sablonban (ARM-sablon) található egész számok használatához:

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [p](#min)
* [mod](#mod)
* [mul](#mul)
* [Sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="add"></a>add

`add(operand1, operand2)`

A két megadott egész szám összegét adja vissza. A `add` függvény nem támogatott a bicep. Használja `+` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
|operand1 |Yes |int |A hozzáadandó első szám. |
|operand2 |Yes |int |A hozzáadandó második szám. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a paraméterek összegét tartalmazza.

### <a name="example"></a>Példa

Az alábbi [sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) két paramétert hoz létre.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Egy iterációs hurok indexét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| loopName | No | sztring | Az iteráció megszerzéséhez használt hurok neve. |
| offset |No |int |A nulla alapú iterációs értékhez hozzáadandó szám. |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt mindig egy **másolási** objektummal használja a rendszer. Ha nincs megadva érték az **eltoláshoz**, a rendszer az aktuális iterációs értéket adja vissza. Az iteráció értéke nullával kezdődik.

A **loopName** tulajdonság lehetővé teszi annak megadását, hogy a copyIndex erőforrás-iterációra vagy tulajdonság-iterációra hivatkozik-e. Ha a **loopName** nem ad meg értéket, a rendszer az aktuális erőforrástípus iterációját használja. Adjon meg egy értéket a **loopName** , amikor a tulajdonságot megismétli.

A másolás használatával kapcsolatos további információkért lásd:

* [Erőforrás-iteráció az ARM-sablonokban](copy-resources.md)
* [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
* [Változó iteráció az ARM-sablonokban](copy-variables.md)
* [Kimeneti iteráció az ARM-sablonokban](copy-outputs.md)

### <a name="example"></a>Példa

A következő példa egy másolási ciklust és a névben szereplő index értékét mutatja be.

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Hurkok, és `copyIndex` még nincsenek implementálva a bicep-ban.  Lásd: [hurkok](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely az iteráció aktuális indexét jelöli.

## <a name="div"></a>div

`div(operand1, operand2)`

A két megadott egész szám egészének osztását adja vissza. A `div` függvény nem támogatott a bicep. Használja `/` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A felosztott szám. |
| operand2 |Yes |int |A felosztáshoz használt szám. Nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

A osztást jelképező egész szám.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) egy paramétert oszt szét egy másik paraméterrel.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Az értéket egy lebegőpontos számra konvertálja. Ezt a függvényt csak akkor használja, ha egyéni paramétereket adunk át egy alkalmazásnak, például egy logikai alkalmazásnak. A `float` függvény nem támogatott a bicep.  Lásd: a [32 bites egész számokból származó numerikus típusok támogatása](https://github.com/Azure/bicep/issues/486).

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |karakterlánc vagy int |A lebegőpontos számra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Egy lebegőpontos szám.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható az úszó a paraméterek logikai alkalmazásba való átadásához:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> A `float` függvény nem támogatott a bicep.  Lásd: a [32 bites egész számokból származó numerikus típusok támogatása](https://github.com/Azure/bicep/issues/486).

---

## <a name="int"></a>int

`int(valueToConvert)`

Egy egész számra konvertálja a megadott értéket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes |karakterlánc vagy int |Az egész számra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított érték egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a felhasználó által megadott paraméter értékét egész értékre konvertálja.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max.

`max (arg1)`

A maximális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény, amely a maximális értéket kapja. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a gyűjteményből származó maximális értéket jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) azt szemlélteti, hogyan használható a Max egy tömbvel és egy egész számokból álló listával:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>p

`min (arg1)`

A minimális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény a minimális érték beolvasásához. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a gyűjtemény minimális értékét jelöli.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) azt szemlélteti, hogyan használható a min egy tömbvel és egy egész számokból álló listával:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Az egész szám többit adja vissza a két megadott egész szám használatával. A `mod` függvény nem támogatott a bicep. Használja `%` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A felosztott szám. |
| operand2 |Yes |int |A felosztáshoz használt szám nem lehet 0. |

### <a name="return-value"></a>Visszatérési érték

A maradékot jelölő egész szám.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) egy másik paraméterrel való osztásának hátralévő részét adja vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

A két megadott egész szám szorzását adja vissza. A `mul` függvény nem támogatott a bicep. Használja `*` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A szorzáshoz használandó első szám. |
| operand2 |Yes |int |A szorzáshoz használt második szám. |

### <a name="return-value"></a>Visszatérési érték

A szorzást jelölő egész szám.

### <a name="example"></a>Példa

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) egy paramétert szorozunk egy másik paraméterrel.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>Sub

`sub(operand1, operand2)`

A két megadott egész szám kivonását adja vissza. A `sub` függvény nem támogatott a bicep. Használja `-` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |A következőből kivont szám. |
| operand2 |Yes |int |A kivonni kívánt szám. |

### <a name="return-value"></a>Visszatérési érték

A kivonást jelképező egész szám.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) egy paramétert kivonja egy másik paraméterből.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| aleredmény | Int | 4 |

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
* Egy adott típusú erőforrás létrehozásakor a megadott számú alkalommal megismételheti az [erőforrás-iteráció az ARM-sablonokban](copy-resources.md)című témakört.
