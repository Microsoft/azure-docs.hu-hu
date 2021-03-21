---
title: Sablon functions – objektumok
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti az objektumok kezeléséhez.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 5e13177db1a7cf2f19a822363cb3884474566add
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920466"
---
# <a name="object-functions-for-arm-templates"></a>ARM-sablonok Object functions

A Resource Manager számos funkciót biztosít az objektumok Azure Resource Manager sablonban való használatához (ARM-sablon):

* [tartalmaz](#contains)
* [createObject](#createobject)
* [üres](#empty)
* [kereszteződés](#intersection)
* [JSON](#json)
* [length](#length) (hossz)
* [null](#null)
* [Union](#union)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, egy objektum tartalmaz-e kulcsot, vagy egy karakterlánc tartalmaz-e alkarakterláncot. A karakterlánc-összehasonlítás megkülönbözteti a kis-és nagybetűket. Ha azonban egy objektum kulcsot tartalmaz, az összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| tároló |Yes |tömb, objektum vagy karakterlánc |A keresendő értéket tartalmazó érték. |
| itemToFind |Yes |karakterlánc vagy int |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha az adott tétel megtalálható; Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) azt mutatja be, hogyan használható a különböző típusú tartalmaz:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringTrue | Logikai | Igaz |
| stringFalse | Logikai | Hamis |
| objectTrue | Logikai | Igaz |
| objectFalse | Logikai | Hamis |
| arrayTrue | Logikai | Igaz |
| arrayFalse | Logikai | Hamis |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

Létrehoz egy objektumot a kulcsok és értékek alapján. A `createObject` bicep nem támogatja a függvényt.  Objektum létrehozása a használatával `{}` .

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| key1 |No |sztring |A kulcs neve. |
| érték1 |No |int, Boolean, string, Object vagy Array |A kulcs értéke. |
| További kulcsok |No |sztring |A kulcsok további nevei. |
| További értékek |No |int, Boolean, string, Object vagy Array |A kulcsok további értékei. |

A függvény csak páros számú paramétert fogad el. Minden kulcsnak egyező értékkel kell rendelkeznie.

### <a name="return-value"></a>Visszatérési érték

Egy olyan objektum, amely minden egyes kulcs-érték párral rendelkezik.

### <a name="example"></a>Példa

A következő példa egy objektumot hoz létre különböző típusú értékekből.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "newObject": {
      "type": "object",
      "value": "[createObject('intProp', 1, 'stringProp', 'abc', 'boolProp', true(), 'arrayProp', createArray('a', 'b', 'c'), 'objectProp', createObject('key1', 'value1'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output newObject object = {
  'intProp': 1
  'stringProp': 'abc'
  'boolProp': true
  'arrayProp': [
    'a'
    'b'
    'c'
  ]
  'objectProp': {
    'key1': 'value1'
  }
}
```

---

Az előző példában szereplő, alapértelmezett értékekkel rendelkező objektum kimenete `newObject` a következő értékkel rendelkező objektum:

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |tömb, objektum vagy karakterlánc |Az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) ellenőrzi, hogy egy tömb, objektum és karakterlánc üres-e.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayEmpty | Logikai | Igaz |
| objectEmpty | Logikai | Igaz |
| stringEmpty | Logikai | Igaz |

## <a name="intersection"></a>kereszteződés

`intersection(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek közös elemeit tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy objektum |Az általános elemek kereséséhez használandó első érték. |
| arg2 |Yes |tömb vagy objektum |A közös elemek kereséséhez használt második érték. |
| További argumentumok |No |tömb vagy objektum |Az általános elemek kereséséhez használandó további értékek. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb vagy objektum a közös elemekkel.

### <a name="example"></a>Példa

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) azt mutatja be, hogyan használhatók az metszetek és objektumok együttes használata:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}
param firstArray array = [
  'one'
  'two'
  'three'
]
param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "három": "c"} |
| arrayOutput | Tömb | ["kettő", "három"] |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Egy érvényes JSON-karakterláncot alakít át JSON-adattípusra.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |sztring |A JSON-ra konvertálandó érték. A karakterláncnak megfelelően formázott JSON-karakterláncnak kell lennie. |

### <a name="return-value"></a>Visszatérési érték

A megadott karakterlánc JSON-adattípusa, vagy **Null** értékű üres érték van megadva.

### <a name="remarks"></a>Megjegyzések

Ha a JSON-objektumban szerepelnie kell egy paraméter értékének vagy változónak, használja a [concat](template-functions-string.md#concat) függvényt a függvénynek átadott karakterlánc létrehozásához.

Null érték beszerzéséhez használhatja a [Null ()](#null) tulajdonságot is.

### <a name="example"></a>Példa

Az alábbi [példában látható sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) a JSON-függvény használatát mutatja be. Figyelje meg, hogy üres objektum esetében **Null értéket** adhat át.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "jsonEmptyObject": {
      "type": "string",
      "defaultValue": "null"
    },
    "jsonObject": {
      "type": "string",
      "defaultValue": "{\"a\": \"b\"}"
    },
    "jsonString": {
      "type": "string",
      "defaultValue": "\"test\""
    },
    "jsonBoolean": {
      "type": "string",
      "defaultValue": "true"
    },
    "jsonInt": {
      "type": "string",
      "defaultValue": "3"
    },
    "jsonArray": {
      "type": "string",
      "defaultValue": "[[1,2,3 ]"
    },
    "concatValue": {
      "type": "string",
      "defaultValue": "demo value"
    }
  },
  "resources": [
  ],
  "outputs": {
    "emptyObjectOutput": {
      "type": "bool",
      "value": "[empty(json(parameters('jsonEmptyObject')))]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[json(parameters('jsonObject'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[json(parameters('jsonString'))]"
    },
    "booleanOutput": {
      "type": "bool",
      "value": "[json(parameters('jsonBoolean'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[json(parameters('jsonInt'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[json(parameters('jsonArray'))]"
    },
    "concatObjectOutput": {
      "type": "object",
      "value": "[json(concat('{\"a\": \"', parameters('concatValue'), '\"}'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param jsonEmptyObject string = 'null'
param jsonObject string = '{\'a\': \'b\'}'
param jsonString string = '\'test\''
param jsonBoolean string = 'true'
param jsonInt string = '3'
param jsonArray string = '[[1,2,3]]'
param concatValue string = 'demo value'

output emptyObjectOutput bool = empty(json(jsonEmptyObject))
output objectOutput object = json(jsonObject)
output stringOutput string =json(jsonString)
output booleanOutput bool = json(jsonBoolean)
output intOutput int = json(jsonInt)
output arrayOutput array = json(jsonArray)
output concatObjectOutput object = json(concat('{"a": "', concatValue, '"}'))
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| emptyObjectOutput | Logikai | Igaz |
| objectOutput | Objektum | {"a": "b"} |
| stringOutput | Sztring | test |
| booleanOutput | Logikai | Igaz |
| intOutput | Egész szám | 3 |
| arrayOutput | Tömb | [ 1, 2, 3 ] |
| concatObjectOutput | Objektum | {"a": "bemutató értéke"} |

## <a name="length"></a>hossz

`length(arg1)`

Egy tömbben lévő elemek számát, egy karakterláncban szereplő karaktereket vagy egy objektum gyökérszintű tulajdonságait adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb, karakterlánc vagy objektum |Az elemek számának beolvasásához használandó tömb, a karakterek számának beolvasásához használandó karakterlánc, vagy a gyökérszintű tulajdonságok számának beolvasásához használandó objektum. |

### <a name="return-value"></a>Visszatérési érték

Egy int.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) azt szemlélteti, hogyan használható a hossz egy tömb és egy karakterlánc használatával:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
      'propD-1': 'sub'
      'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

Null értéket ad vissza. A `null` függvény nem érhető el a bicep-ben. Használja `null` helyette a kulcsszót.

### <a name="parameters"></a>Paraméterek

A null függvény nem fogad el paramétereket.

### <a name="return-value"></a>Visszatérési érték

Olyan érték, amely mindig null értékű.

### <a name="example"></a>Példa

A következő példa a null függvényt használja.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(null())]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output emptyOutput bool = empty(null)
```

---

Az előző példa kimenete a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| emptyOutput | Logikai | Igaz |

## <a name="union"></a>Union

`union(arg1, arg2, arg3, ...)`

Egyetlen tömböt vagy objektumot ad vissza, amely a paraméterek összes elemét tartalmazza. Ismétlődő értékek vagy kulcsok csak egyszer szerepelnek.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy objektum |Az elemek csatlakoztatásának első értéke. |
| arg2 |Yes |tömb vagy objektum |Az elemek csatlakoztatásához használt második érték. |
| További argumentumok |No |tömb vagy objektum |Az elemekhez való csatlakozáshoz használandó további értékek. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy objektum.

### <a name="example"></a>Példa

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) azt mutatja be, hogyan használható a Union a tömbökkel és objektumokkal:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "objectOutput": {
      "type": "object",
      "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Objektum | {"One": "a", "kettő": "b", "három": "C2", "Four": "d", "öt": "e"} |
| arrayOutput | Tömb | ["egy", "kettő", "három", "négy"] |

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
