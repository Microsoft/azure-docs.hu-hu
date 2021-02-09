---
title: Sablon functions – összehasonlítás
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti az értékek összehasonlításához.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 95655a4c92a1de9bb7a7faebcdaa83fb0fa75696
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834000"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM-sablonok összehasonlító függvények

A Resource Manager számos funkciót biztosít az összehasonlítások megadásához a Azure Resource Manager-sablonban (ARM-sablon):

* [összefonódik](#coalesce)
* [egyenlő](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [kisebb](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>összefonódik

`coalesce(arg1, arg2, arg3, ...)`

A paraméterekből származó első nem null értéket adja vissza. Az üres karakterláncok, üres tömbök és üres objektumok nem null értékűek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, Array vagy Object |A Null érték tesztelésének első értéke. |
| További argumentumok |Nem |int, string, Array vagy Object |A Null érték tesztelésére szolgáló további értékek. |

### <a name="return-value"></a>Visszatérési érték

Az első nem null paraméterek értéke, amely lehet karakterlánc, int, tömb vagy objektum. NULL, ha az összes paraméter null értékű.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) az egyesítés különböző felhasználási eredményeiből származó kimenetet jeleníti meg.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
    },
    "intOutput": {
      "type": "int",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
    },
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.string
output intOutput int = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.int
output objectOutput object = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.object
output arrayOutput array = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.array
output emptyOutput bool =empty(objectToTest.null1 ?? objectToTest.null2)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Sztring | alapértelmezett |
| intOutput | Int | 1 |
| objectOutput | Objektum | {"első": "default"} |
| arrayOutput | Tömb |  [1] |
| emptyOutput | Logikai | Igaz |

## <a name="equals"></a>egyenlő

`equals(arg1, arg2)`

Ellenőrzi, hogy két érték egyenlő-e egymással. A `equals` függvény nem támogatott a bicep. Használja `==` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int, string, Array vagy Object |Az egyenlőség keresésének első értéke. |
| arg2 |Igen |int, string, Array vagy Object |A második érték az egyenlőség kereséséhez. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az értékek egyenlőek; Ellenkező esetben **hamis**.

### <a name="remarks"></a>Megjegyzések

Az Equals függvényt gyakran használják az `condition` elemmel annak teszteléséhez, hogy van-e üzembe helyezett erőforrás.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` még nincsenek implementálva a bicep. Tekintse meg a [feltételeket](https://github.com/Azure/bicep/issues/186).

---

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) különböző típusú értékeket keres az egyenlőséghez. Az összes alapértelmezett érték igaz értéket ad vissza.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | Igaz |
| checkStrings | Logikai | Igaz |
| checkArrays | Logikai | Igaz |
| checkObjects | Logikai | Igaz |

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a sablon [nem](template-functions-logical.md#not) **egyenlő**.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Az előző példa kimenete a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | Igaz |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e, mint a második érték. A `greater` függvény nem támogatott a bicep. Használja `>` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) ellenőrzi, hogy az egyik érték nagyobb-e, mint a másik.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | Hamis |
| checkStrings | Logikai | Igaz |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték nagyobb-e vagy egyenlő-e a második értékkel. A `greaterOrEquals` függvény nem támogatott a bicep. Használja `>=` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A nagyobb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az első érték nagyobb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) ellenőrzi, hogy az egyik érték nagyobb-e vagy egyenlő-e a másikkal.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | Hamis |
| checkStrings | Logikai | Igaz |

## <a name="less"></a>less

`less(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e a második értéknél. A `less` függvény nem támogatott a bicep. Használja `<` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kevésbé hasonlítható összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az első érték kisebb, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) ellenőrzi, hogy az egyik érték kisebb-e, mint a másik.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | Igaz |
| checkStrings | Logikai | Hamis |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Ellenőrzi, hogy az első érték kisebb-e vagy egyenlő-e a második értékkel. A `lessOrEquals` függvény nem támogatott a bicep. Használja `<=` helyette a kezelőt.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás első értéke. |
| arg2 |Igen |int vagy sztring |A kisebb vagy egyenlő összehasonlítás második értéke. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az első érték kisebb vagy egyenlő, mint a második érték; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

Az alábbi [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) ellenőrzi, hogy az egyik érték kisebb-e vagy egyenlő-e a másikkal.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkInts | Logikai | Igaz |
| checkStrings | Logikai | Hamis |

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
