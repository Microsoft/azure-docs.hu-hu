---
title: Sablon functions – tömbök
description: Ismerteti a Azure Resource Manager-sablonban (ARM-sablonban) használt függvényeket a tömbökkel való munkához.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 40a6815bb10ce9725405d68498b9a554706f3af8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920540"
---
# <a name="array-functions-for-arm-templates"></a>ARM-sablonok tömb függvények

A Resource Manager számos funkciót biztosít a tömbök Azure Resource Manager sablonban való használatához (ARM-sablon):

* [array](#array)
* [concat](#concat)
* [tartalmaz](#contains)
* [createArray](#createarray)
* [üres](#empty)
* [első](#first)
* [kereszteződés](#intersection)
* [utolsó](#last)
* [length](#length) (hossz)
* [Max](#max)
* [p](#min)
* [tartomány](#range)
* [kihagyása](#skip)
* [eltarthat](#take)
* [Union](#union)

Ha egy értékkel tagolt karakterlánc-értékeket szeretne kapni, tekintse meg a következőt: [Split](template-functions-string.md#split).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="array"></a>array

`array(convertToArray)`

Az értéket egy tömbre konvertálja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |Yes |int, string, Array vagy Object |Egy tömbre konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Tömb.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható a Array függvény különböző típusokkal.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "intToConvert": {
      "type": "int",
      "defaultValue": 1
    },
    "stringToConvert": {
      "type": "string",
      "defaultValue": "efgh"
    },
    "objectToConvert": {
      "type": "object",
      "defaultValue": {
        "a": "b",
        "c": "d"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "intOutput": {
      "type": "array",
      "value": "[array(parameters('intToConvert'))]"
    },
    "stringOutput": {
      "type": "array",
      "value": "[array(parameters('stringToConvert'))]"
    },
    "objectOutput": {
      "type": "array",
      "value": "[array(parameters('objectToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| intOutput | Tömb |  [1] |
| stringOutput | Tömb | ["efgh"] |
| objectOutput | Tömb | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Több tömböt egyesít, és visszaadja az összefűzött tömböt, vagy kombinálja a több sztringet, és visszaadja az összefűzött karakterláncot.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy karakterlánc |Az Összefűzés első tömbje vagy karakterlánca. |
| További argumentumok |No |tömb vagy karakterlánc |További tömbök vagy karakterláncok egymást követő sorrendben az összefűzéshez. |

Ez a függvény tetszőleges számú argumentumot igénybe vehet, és a paraméterekhez karakterláncokat vagy tömböket is elfogadhat. A paraméterekhez azonban nem lehet tömböt és karakterláncot megadni. A tömbök csak más tömbökkel vannak összefűzve.

### <a name="return-value"></a>Visszatérési érték

Az összefűzött értékek karakterlánca vagy tömbje.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan egyesítheti a két tömböt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| visszatérési | Tömb | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) bemutatja, hogyan egyesítheti a két karakterláncot, és hogyan adhat vissza összefűzött karakterláncot.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| concatOutput | Sztring | előtag – 5yj4yjf5mbg72 |

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

Az alábbi példa azt szemlélteti, hogyan használható a különböző típusú tartalmaz:

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

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Létrehoz egy tömböt a paraméterekből. A `createArray` bicep nem támogatja a függvényt.  Egy tömbből álló literálot a használatával hozhat létre `[]` .

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| args |No |Karakterlánc, egész szám, tömb vagy objektum |A tömb értékei. |

### <a name="return-value"></a>Visszatérési érték

Tömb. Ha nincsenek megadva paraméterek, üres tömböt ad vissza.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használhatja a createArray különböző típusú típusokkal:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    "stringArray": {
      "type": "array",
      "value": "[createArray('a', 'b', 'c')]"
    },
    "intArray": {
      "type": "array",
      "value": "[createArray(1, 2, 3)]"
    },
    "objectArray": {
      "type": "array",
      "value": "[createArray(parameters('objectToTest'))]"
    },
    "arrayArray": {
      "type": "array",
      "value": "[createArray(parameters('arrayToTest'))]"
    },
    "emptyArray": {
      "type": "array",
      "value": "[createArray()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `createArray()` nem támogatja a bicep.  Egy tömbből álló literálot a használatával hozhat létre `[]` .

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringArray | Tömb | ["a", "b", "c"] |
| intArray | Tömb | [1, 2, 3] |
| objectArray | Tömb | [{"One": "a", "Two": "b", "három": "c"}] |
| arrayArray | Tömb | [["egy", "kettő", "három"]] |
| emptyArray | Tömb | [] |

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |tömb, objektum vagy karakterlánc |Az az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="example"></a>Példa

A következő példa ellenőrzi, hogy egy tömb, objektum és karakterlánc üres-e.

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

## <a name="first"></a>első

`first(arg1)`

A tömb első elemét vagy a karakterlánc első karakterét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy karakterlánc |Az első elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb első elemének típusa (karakterlánc, int, tömb vagy objektum) vagy egy karakterlánc első karaktere.

### <a name="example"></a>Példa

Az alábbi példa azt szemlélteti, hogyan használható az első függvény egy tömb és egy karakterlánc használatával.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
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

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Sztring | egy |
| stringOutput | Sztring | O |

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

Az alábbi példa azt szemlélteti, hogyan használhatók az metszetek és objektumok együttes használata:

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

## <a name="last"></a>utolsó

`last (arg1)`

A tömb utolsó elemét vagy a karakterlánc utolsó karakterét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb utolsó elemének típusa (karakterlánc, int, tömb vagy objektum) vagy egy karakterlánc utolsó karaktere.

### <a name="example"></a>Példa

Az alábbi példa azt szemlélteti, hogyan használható az utolsó függvény egy tömb és egy karakterlánc használatával.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
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

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two three')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Sztring | három |
| stringOutput | Sztring | e |

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

Az alábbi példa azt szemlélteti, hogyan használható a Length tömb és karakterlánc használatával:

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

A függvényt egy tömb használatával adhatja meg az erőforrások létrehozásakor megjelenő ismétlések számát. A következő példában a **siteNames** paraméter a webhelyek létrehozásakor használandó nevek sorára hivatkozik.

# <a name="json"></a>[JSON](#tab/json)

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> A hurkok még nincsenek implementálva a bicep.  Lásd: [hurkok](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

A függvény tömbhöz való használatával kapcsolatos további információkért lásd: [erőforrás-iteráció az ARM-sablonokban](copy-resources.md).

## <a name="max"></a>max.

`max(arg1)`

A maximális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény, amely a maximális értéket kapja. |

### <a name="return-value"></a>Visszatérési érték

A maximális értéket jelölő int.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható a Max egy tömbvel és egy egész számokból álló listával:

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

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>p

`min(arg1)`

A minimális értéket adja vissza egész számok tömbje vagy az egész számok vesszővel tagolt listája alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |egész számok tömbje vagy egész számok vesszővel tagolt listája |A gyűjtemény a minimális érték beolvasásához. |

### <a name="return-value"></a>Visszatérési érték

A minimális értéket jelölő int.

### <a name="example"></a>Példa

Az alábbi példa azt szemlélteti, hogyan használható a min egy tömbvel és egy egész számokból álló listával:

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

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>tartomány

`range(startIndex, count)`

Egész számok tömbjét hozza létre egy kezdő egész számból, és több elemet tartalmaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| startIndex |Yes |int |A tömb első egésze. A startIndex és a darabszám összege nem lehet nagyobb, mint 2147483647. |
| count |Yes |int |A tömbben lévő egész számok száma. Legfeljebb 10000-ig nem negatív egész számnak kell lennie. |

### <a name="return-value"></a>Visszatérési érték

Egész számok tömbje.

### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan használható a Range függvény:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "startingInt": {
      "type": "int",
      "defaultValue": 5
    },
    "numberOfElements": {
      "type": "int",
      "defaultValue": 3
    }
  },
  "resources": [],
  "outputs": {
    "rangeOutput": {
      "type": "array",
      "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| rangeOutput | Tömb | [5, 6, 7] |

## <a name="skip"></a>kihagyása

`skip(originalValue, numberToSkip)`

Egy tömböt ad vissza az összes elemmel a tömbben megadott szám után, vagy egy karakterláncot ad vissza, amely az összes karaktert tartalmazza a karakterláncban megadott szám után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| numberToSkip |Yes |int |A kihagyni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az értékben szereplő összes elemet vagy karaktert adja vissza a rendszer. Ha a tömb vagy a karakterlánc hossza nagyobb, akkor a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő példa kihagyja a tömbben megadott számú elemet és a karakterláncban megadott számú karaktert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["három"] |
| stringOutput | Sztring | 2 3 |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Egy tömböt ad vissza a megadott számú elemmel a tömb elejétől, vagy egy olyan karakterláncot, amely a karakterlánc elejétől megadott számú karakterből áll.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tömb vagy karakterlánc |Az elemeket tartalmazó tömb vagy karakterlánc. |
| numberToTake |Yes |int |A felvenni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, üres tömböt vagy karakterláncot ad vissza. Ha az érték nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer a tömb vagy karakterlánc összes elemét adja vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="example"></a>Példa

A következő példa a tömbben szereplő elemek megadott számú elemét és a karakterláncok karaktereit veszi figyelembe.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["egy", "Two"] |
| stringOutput | Sztring | itt: |

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

Az alábbi példa bemutatja, hogyan használható a Union a tömbökkel és objektumokkal:

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
