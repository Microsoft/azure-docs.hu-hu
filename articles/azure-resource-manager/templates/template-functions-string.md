---
title: Sablon functions – sztring
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti a karakterláncok használatához.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: e823acc07ce0618c064f30e103ec52b7133cea18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731119"
---
# <a name="string-functions-for-arm-templates"></a>Karakterlánc-függvények ARM-sablonokhoz

A Resource Manager a következő funkciókat biztosítja a karakterláncok használatához a Azure Resource Manager-sablonban (ARM-sablon):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [tartalmaz](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [üres](#empty)
* [endsWith](#endswith)
* [első](#first)
* [formátumban](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [JSON](#json)
* [utolsó](#last)
* [lastIndexOf](#lastindexof)
* [length](#length) (hossz)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [csere](#replace)
* [kihagyása](#skip)
* [felosztása](#split)
* [startsWith](#startswith)
* [sztring](#string)
* [substring](#substring)
* [eltarthat](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [Trim](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

A bemeneti karakterlánc Base64-ábrázolását adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |sztring |Az az érték, amelyet Base64-ábrázolásként kell visszaadni. |

### <a name="return-value"></a>Visszatérési érték

A Base64-ábrázolást tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a Base64 függvény használatát mutatja be.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Objektum | {"One": "a", "kettő": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Base64-leképezést alakít át egy JSON-objektumra.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| Base64value tulajdonsága |Yes |sztring |A JSON-objektumra konvertálandó Base64-ábrázolás. |

### <a name="return-value"></a>Visszatérési érték

Egy JSON-objektum.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToJson függvényt használja egy Base64 érték átalakítására:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Objektum | {"One": "a", "kettő": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Base64-ábrázolást konvertál karakterlánccá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| Base64value tulajdonsága |Yes |sztring |A Base64-ábrázolás, amelyet karakterlánccá kell alakítani. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított Base64-érték karakterlánca.

### <a name="examples"></a>Példák

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a base64ToString függvényt használja egy Base64 érték átalakítására:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| base64Output | Sztring | b25lLCB0d28sIHRocmVl |
| toStringOutput | Sztring | egy két há' |
| toJsonOutput | Objektum | {"One": "a", "kettő": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Több karakterlánc-értéket egyesít, és visszaadja az összefűzött karakterláncot, vagy kombinálja a több tömböt, és visszaadja az összefűzött tömböt.

A karakterlánc-Összefűzés egyszerűsítése érdekében a bicep támogatja a [karakterlánc-interpolációs](https://en.wikipedia.org/wiki/String_interpolation#) szintaxist.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |karakterlánc vagy tömb |Az Összefűzés első karakterlánca vagy tömbje. |
| További argumentumok |No |karakterlánc vagy tömb |További karakterláncok vagy tömbök egymást követő sorrendben az összefűzéshez. |

Ez a függvény tetszőleges számú argumentumot igénybe vehet, és a paraméterekhez karakterláncokat vagy tömböket is elfogadhat. A paraméterekhez azonban nem lehet tömböt és karakterláncot megadni. A karakterláncok csak más karakterláncokkal vannak összefűzve.

### <a name="return-value"></a>Visszatérési érték

Az összefűzött értékek karakterlánca vagy tömbje.

### <a name="examples"></a>Példák

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

vagy

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| concatOutput | Sztring | előtag – 5yj4yjf5mbg72 |

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) bemutatja, hogyan egyesítheti két tömböt.

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

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Ellenőrzi, hogy egy tömb tartalmaz-e értéket, egy objektum tartalmaz-e kulcsot, vagy egy karakterlánc tartalmaz-e alkarakterláncot. A karakterlánc-összehasonlítás megkülönbözteti a kis-és nagybetűket. Ha azonban egy objektum kulcsot tartalmaz, az összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| tároló |Yes |tömb, objektum vagy karakterlánc |A keresendő értéket tartalmazó érték. |
| itemToFind |Yes |karakterlánc vagy int |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha az adott tétel megtalálható; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Egy értéket konvertál egy adaturi-ra.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |sztring |Az adaturi-ra konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

Az adaturi-ként formázott karakterlánc.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy értéket konvertál egy adaturi-ra, és átalakítja az adaturi-t egy karakterlánccá:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | Sztring | adatmező: text/plain; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Sztring | Hello World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Az adaturi-formázott értéket karakterlánccá alakítja át.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |sztring |Az átalakítandó adaturi-érték. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított értéket tartalmazó sztring.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) egy értéket konvertál egy adaturi-ra, és átalakítja az adaturi-t egy karakterlánccá:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| dataUriOutput | Sztring | adatmező: text/plain; charset = UTF8; Base64, SGVsbG8 = |
| toStringOutput | Sztring | Hello World! |

## <a name="empty"></a>üres

`empty(itemToTest)`

Meghatározza, hogy egy tömb, objektum vagy karakterlánc üres-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |tömb, objektum vagy karakterlánc |Az érték, amely alapján ellenőrizhető, hogy üres-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az érték üres. Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterlánc egy értékkel végződik-e. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |sztring |A keresendő elemeket tartalmazó érték. |
| stringToFind |Yes |sztring |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha a sztring utolsó karaktere vagy karaktere megegyezik az értékkel; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a startsWith és a endsWith függvények használatát mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | Logikai | Igaz |
| startsCapTrue | Logikai | Igaz |
| startsFalse | Logikai | Hamis |
| endsTrue | Logikai | Igaz |
| endsCapTrue | Logikai | Igaz |
| endsFalse | Logikai | Hamis |

## <a name="first"></a>első

`first(arg1)`

A karakterlánc első karakterét vagy a tömb első elemét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy karakterlánc |Az első elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb első elemének első karakterének vagy típusának (karakterlánc, int, Array vagy Object) a karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) azt szemlélteti, hogyan használható az első függvény egy tömb és egy karakterlánc használatával.

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

## <a name="format"></a>formátumban

`format(formatString, arg1, arg2, ...)`

Létrehoz egy formázott karakterláncot a bemeneti értékek közül.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| formatString | Yes | sztring | Az összetett formátum karakterlánca |
| arg1 | Yes | string, integer vagy boolean | A formázott sztringbe felvenni kívánt érték. |
| További argumentumok | No | string, integer vagy boolean | A formázott sztringbe foglalandó további értékek. |

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel formázhatja a sablonban található karakterláncot. Ugyanazokat a formázási beállításokat használja, mint a [System. string. Format](/dotnet/api/system.string.format) metódus a .net-ben.

### <a name="examples"></a>Példák

A következő példa a Format függvény használatát mutatja be.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| formatTest | Sztring | Hello, felhasználó. Formázott szám: 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Egy globálisan egyedi azonosító formájában létrehoz egy értéket a paraméterként megadott értékek alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |sztring |A kivonatoló függvényben a GUID létrehozásához használt érték. |
| további paraméterek igény szerint |No |sztring |Az egyediség szintjét meghatározó érték létrehozásához tetszőleges számú karakterláncot adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény akkor hasznos, ha globálisan egyedi azonosító formátumban kell létrehoznia egy értéket. Olyan paramétereket adhat meg, amelyek korlátozzák az eredmény egyediségi hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés számára.

A visszaadott érték nem véletlenszerű karakterlánc, hanem egy kivonatoló függvény eredménye a paramétereknél. A visszaadott érték 36 karakter hosszú. Globálisan nem egyedi. Ha olyan új GUID azonosítót szeretne létrehozni, amely nem a paraméterek kivonatán alapul, használja a [newGuid](#newguid) függvényt.

Az alábbi példák azt mutatják be, hogyan használható a GUID a gyakran használt szintek egyedi értékének létrehozásához.

Az előfizetéshez tartozó egyedi hatókör

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Az erőforráscsoport egyedi hatóköre

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Egy erőforráscsoport üzembe helyezésének egyedi hatóköre

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Visszatérési érték

Egy 36 karaktert tartalmazó karakterlánc, amely globálisan egyedi azonosító formátumban van.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) az eredményeket adja vissza a GUID azonosítóból:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Egy érték első pozícióját adja vissza egy karakterláncon belül. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |sztring |A keresendő elemeket tartalmazó érték. |
| stringToFind |Yes |sztring |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a keresett tétel pozícióját jelöli. Az érték nulla-alapú. Ha az elem nem található, a-1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és a lastIndexOf függvények használatát mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Egy érvényes JSON-karakterláncot alakít át JSON-adattípusra. További információ: JSON- [függvény](template-functions-object.md#json).

## <a name="last"></a>utolsó

`last (arg1)`

A sztring utolsó karakterét vagy a tömb utolsó elemét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb vagy karakterlánc |Az utolsó elem vagy karakter beolvasására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

Egy tömb utolsó elemének utolsó karakterének vagy típusának (karakterlánc, int, Array vagy Object) a karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) azt szemlélteti, hogyan használható az utolsó függvény egy tömb és egy karakterlánc használatával.

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
output stringOutput string = last('One Two Three')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Sztring | három |
| stringOutput | Sztring | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Egy érték utolsó pozícióját adja vissza egy karakterláncon belül. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |sztring |A keresendő elemeket tartalmazó érték. |
| stringToFind |Yes |sztring |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

Egy egész szám, amely a keresendő tétel utolsó pozícióját jelöli. Az érték nulla-alapú. Ha az elem nem található, a-1 értéket adja vissza.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a indexOf és a lastIndexOf függvények használatát mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>hossz

`length(string)`

Egy sztringben szereplő karakterek számát, egy tömb elemeinek vagy egy objektum gyökérszintű tulajdonságait adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tömb, karakterlánc vagy objektum |Az elemek számának beolvasásához használandó tömb, a karakterek számának beolvasásához használandó karakterlánc, vagy a gyökérszintű tulajdonságok számának beolvasásához használandó objektum. |

### <a name="return-value"></a>Visszatérési érték

Egy int.

### <a name="examples"></a>Példák

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

## <a name="newguid"></a>newGuid

`newGuid()`

Egy globálisan egyedi azonosító formájában megadott értéket ad vissza. **Ez a függvény csak a paraméter alapértelmezett értékében használható.**

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékére használhatja egy kifejezésen belül. Ha ezt a funkciót a sablonban bárhol máshol használja, hibaüzenetet ad vissza. A függvény nem engedélyezett a sablon más részeiben, mert minden egyes híváskor más értéket ad vissza. Ugyanazon sablon ugyanazon paraméterekkel való üzembe helyezése nem eredményezi megbízhatóan ugyanazt az eredményt.

A newGuid függvény eltér a [GUID](#guid) függvénytől, mert nem végez paramétereket. Ha ugyanazzal a paraméterrel hívja meg a GUID azonosítót, minden alkalommal ugyanazt az azonosítót adja vissza. Használja a GUID azonosítót, ha megbízhatóan kell létrehoznia egy adott környezethez tartozó GUID azonosítót. Akkor használja a newGuid, ha minden alkalommal eltérő azonosítóra van szükség, például erőforrások üzembe helyezése tesztkörnyezetben.

A newGuid függvény a .NET-keretrendszer [GUID-struktúráját](/dotnet/api/system.guid) használja a globálisan egyedi azonosító létrehozásához.

Ha a [korábbi sikeres központi telepítés újbóli üzembe helyezését](rollback-on-error.md)használja, és a korábbi telepítés newGuid-t használó paramétert tartalmaz, a paraméter nem lett újraértékelve. Ehelyett a korábbi központi telepítés paraméterének értékét a rendszer automatikusan újra felhasználja a visszaállítási telepítésben.

Tesztelési környezetben szükség lehet az olyan erőforrások ismételt üzembe helyezésére, amelyek csak rövid ideig élnek. Az egyedi nevek létrehozása helyett a newGuid és a [uniqueString](#uniquestring) segítségével egyedi neveket hozhat létre.

Ügyeljen arra, hogy egy alapértelmezett érték esetén a newGuid függvényre támaszkodó sablont telepítse újra. Ha újratelepíti, és nem ad meg értéket a paraméterhez, a függvény újraértékelése megtörténik. Ha egy meglévő erőforrást nem új létrehozása helyett szeretne frissíteni, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Visszatérési érték

Egy 36 karaktert tartalmazó karakterlánc, amely globálisan egyedi azonosító formátumban van.

### <a name="examples"></a>Példák

Az alábbi példában szereplő sablon egy új azonosítójú paramétert mutat be.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| guidOutput | sztring | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

A következő példa a newGuid függvényt használja a Storage-fiók egyedi nevének létrehozásához. Ez a sablon olyan tesztkörnyezetben is működhet, ahol a Storage-fiók egy rövid ideig létezik, és nem települ újra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| nameOutput | sztring | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Egy jobbra igazított karakterláncot ad vissza, amely a megadott teljes hossz eléréséig a bal oldali karaktereket adja hozzá.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |karakterlánc vagy int |Az érték jobbra igazítása. |
| totalLength |Yes |int |A visszaadott karakterláncban szereplő karakterek teljes száma. |
| paddingCharacter |No |egyetlen karakter |A bal oldali margóhoz használandó karakter, amíg el nem éri a teljes hosszt. Az alapértelmezett érték egy szóköz. |

Ha az eredeti sztring hosszabb, mint a pad karaktereinek száma, a rendszer nem adja hozzá a karaktereket.

### <a name="return-value"></a>Visszatérési érték

Egy sztring, amely legalább a megadott karakterek számát adja meg.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) azt mutatja be, hogyan lehet a felhasználó által megadott paraméter értékét felvenni a nulla karakter hozzáadásával, amíg el nem éri a karakterek teljes számát.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| stringOutput | Sztring | 0000000123 |

## <a name="replace"></a>csere

`replace(originalString, oldString, newString)`

Egy új karakterláncot ad vissza, amely egy sztring összes példányát egy másik sztring váltja fel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| originalString |Yes |sztring |Egy sztring összes példányát egy másik sztring váltja fel. |
| Régikarakterlánc |Yes |sztring |Az eredeti sztringből eltávolítandó karakterlánc. |
| Újkarakterlánc |Yes |sztring |Az eltávolított karakterlánc helyére felvenni kívánt karakterlánc. |

### <a name="return-value"></a>Visszatérési érték

A lecserélt karaktereket tartalmazó karakterlánc.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) azt mutatja be, Hogyan távolítható el az összes kötőjel a felhasználó által megadott karakterláncból, és hogyan cserélhető le a karakterlánc egy része egy másik karakterláncra.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Sztring | 1231231234 |
| secondOutput | Sztring | 123-123 – XXXX |

## <a name="skip"></a>kihagyása

`skip(originalValue, numberToSkip)`

Egy olyan sztringet ad vissza, amely a megadott számú karakter után az összes karaktert tartalmazza, vagy egy tömböt a megadott számú elem után.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tömb vagy karakterlánc |A kihagyáshoz használandó tömb vagy karakterlánc. |
| numberToSkip |Yes |int |A kihagyni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, az értékben szereplő összes elemet vagy karaktert adja vissza a rendszer. Ha a tömb vagy a karakterlánc hossza nagyobb, akkor a rendszer üres tömböt vagy karakterláncot ad vissza. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) kihagyja a tömbben megadott számú elemet és a megadott számú karaktert egy karakterláncban.

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

## <a name="split"></a>felosztás

`split(inputString, delimiter)`

Karakterláncok tömbjét adja vissza, amely a megadott elválasztó karakterrel tagolt bemeneti sztring alsztringjét tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| inputString |Yes |sztring |A felosztani kívánt karakterlánc. |
| elválasztókarakter |Yes |karakterlánc vagy karakterláncok tömbje |A karakterlánc felosztásához használandó elválasztó karakter. |

### <a name="return-value"></a>Visszatérési érték

Karakterláncok tömbje.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) a bemeneti karakterláncot vesszővel, vesszővel vagy pontosvesszővel osztja szét.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| firstOutput | Tömb | ["egy", "kettő", "három"] |
| secondOutput | Tömb | ["egy", "kettő", "három"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Meghatározza, hogy egy karakterlánc egy értékkel kezdődik-e. Az összehasonlítás megkülönbözteti a kis-és nagybetűket.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |sztring |A keresendő elemeket tartalmazó érték. |
| stringToFind |Yes |sztring |A keresendő érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** , ha a karakterlánc első karaktere vagy karaktere megegyezik az értékkel; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a startsWith és a endsWith függvények használatát mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| startsTrue | Logikai | Igaz |
| startsCapTrue | Logikai | Igaz |
| startsFalse | Logikai | Hamis |
| endsTrue | Logikai | Igaz |
| endsCapTrue | Logikai | Igaz |
| endsFalse | Logikai | False (Hamis) |

## <a name="string"></a>sztring

`string(valueToConvert)`

A megadott értéket karakterlánccá alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Bármelyik |A sztringre konvertálandó érték. Bármilyen típusú érték konvertálható, beleértve az objektumokat és a tömböket is. |

### <a name="return-value"></a>Visszatérési érték

Az átalakított érték karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) bemutatja, hogyan alakíthat át különböző típusú értékeket karakterláncokra:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| objectOutput | Sztring | {"valuea": 10, "valueB": "példa szövege"} |
| arrayOutput | Sztring | ["a", "b", "c"] |
| intOutput | Sztring | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Egy olyan alsztringet ad vissza, amely a megadott karakteres pozíciónál kezdődik, és a megadott számú karaktert tartalmazza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |sztring |Az az eredeti karakterlánc, amelyből a rendszer kinyeri az alkarakterláncot. |
| startIndex |No |int |Az alsztring nulla alapú kiindulási karakterének pozíciója. |
| hossz |No |int |Az alkarakterlánc karaktereinek száma. A karakterláncon belüli helyre kell hivatkoznia. Nulla vagy nagyobb értéknek kell lennie. |

### <a name="return-value"></a>Visszatérési érték

Az alkarakterlánc. Vagy ha a hossz nulla, akkor üres karakterláncot kell megadni.

### <a name="remarks"></a>Megjegyzések

A függvény meghiúsul, ha az alkarakterlánc a karakterlánc végénél hosszabb ideig is kinyúlik, vagy ha a hossz nullánál kisebb. Az alábbi példa a következő hibával meghiúsul: "az index és a Length paraméternek a karakterláncon belüli helyre kell vonatkoznia. Az index paraméter: "0", a Length paraméter: "11", a sztring paraméter hossza: "10". "

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) egy alsztringet kibont egy paraméterből.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| substringOutput | Sztring | kettő |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Egy olyan karakterláncot ad vissza, amely a megadott számú karaktert tartalmazza a karakterlánc elejétől, vagy egy tömböt a tömb elejétől megadott számú elemmel.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tömb vagy karakterlánc |Az elemeket tartalmazó tömb vagy karakterlánc. |
| numberToTake |Yes |int |A felvenni kívánt elemek vagy karakterek száma. Ha ez az érték 0 vagy kevesebb, üres tömböt vagy karakterláncot ad vissza. Ha az érték nagyobb, mint a megadott tömb vagy karakterlánc hossza, a rendszer a tömb vagy karakterlánc összes elemét visszaadja. |

### <a name="return-value"></a>Visszatérési érték

Tömb vagy karakterlánc.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a tömbben szereplő elemek számát és a karakterláncok karaktereit veszi figyelembe.

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
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 2

output arrayOutput array = take(testArray, elementsToSkip)
output stringOutput string = take(testString, charactersToSkip)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| arrayOutput | Tömb | ["egy", "Two"] |
| stringOutput | Sztring | itt: |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

A megadott sztringet kisbetűvé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |sztring |Az kisbetűsre konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

A karakterláncot kisbetűvé alakítja.

### <a name="examples"></a>Példák

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméter értékét kisbetűvé és nagybetűvé alakítja.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | egy két há' |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

A megadott sztringet nagybetűvé alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |sztring |A nagybetűre konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték

A karakterlánc a nagybetűre lett konvertálva.

### <a name="examples"></a>Példák

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) egy paraméter értékét kisbetűvé és nagybetűvé alakítja.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| toLowerOutput | Sztring | egy két há' |
| toUpperOutput | Sztring | egy két há' |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Eltávolítja az összes kezdő és záró szóközöket a megadott karakterláncból.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |sztring |A vágási érték. |

### <a name="return-value"></a>Visszatérési érték

A karakterlánc kezdő és záró szóközök nélkül.

### <a name="examples"></a>Példák

Az alábbi [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) a fehér szóközöket vágja le a paraméterből.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| visszatérési | Sztring | egy két há' |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Létrehoz egy determinisztikus-kivonat karakterláncot a paraméterként megadott értékek alapján.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |sztring |Egy egyedi karakterlánc létrehozásához a kivonatoló függvényben használt érték. |
| további paraméterek igény szerint |No |sztring |Az egyediség szintjét meghatározó érték létrehozásához tetszőleges számú karakterláncot adhat hozzá. |

### <a name="remarks"></a>Megjegyzések

Ez a függvény akkor hasznos, ha egyedi nevet kell létrehoznia egy erőforráshoz. Olyan paramétereket adhat meg, amelyek korlátozzák az eredmény egyediségi hatókörét. Megadhatja, hogy a név egyedi-e az előfizetés, az erőforráscsoport vagy a központi telepítés számára.

A visszaadott érték nem egy véletlenszerű karakterlánc, hanem egy kivonatoló függvény eredménye. A visszaadott érték 13 karakter hosszúságú. Globálisan nem egyedi. Érdemes lehet az értéket az elnevezési konvenció egy előtaggal egyesíteni, hogy olyan nevet hozzon létre, amely értelmes. Az alábbi példában a visszaadott érték formátuma látható. A tényleges érték a megadott paraméterektől függ.

`tcvhiyu5h2o5o`

Az alábbi példák bemutatják, hogyan használhatja a uniqueString-t egyedi érték létrehozására a gyakran használt szintekhez.

Az előfizetéshez tartozó egyedi hatókör

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Az erőforráscsoport egyedi hatóköre

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Egy erőforráscsoport üzembe helyezésének egyedi hatóköre

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

Az alábbi példa bemutatja, hogyan hozhat létre egyedi nevet egy Storage-fiókhoz az erőforráscsoport alapján. Az erőforráscsoport belsejében a név nem egyedi, ha a kiépítés ugyanúgy történik.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Ha minden alkalommal új egyedi nevet kell létrehoznia, amikor sablont telepít, és nem kívánja frissíteni az erőforrást, használhatja a [utcNow](template-functions-date.md#utcnow) függvényt a uniqueString. Ezt a megközelítést tesztkörnyezetben is használhatja. Példaként tekintse meg a következőt: [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Visszatérési érték

13 karaktert tartalmazó sztring.

### <a name="examples"></a>Példák

A következő [példában szereplő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) a uniquestring eredményeit adja vissza:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Abszolút URI-t hoz létre a baseUri és a relativeUri karakterlánc kombinálásával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |sztring |Az alap URI-karakterlánc. Ügyeljen arra, hogy figyelje a záró perjel ("/") kezelésével kapcsolatos viselkedést a táblázatban látható módon.  |
| relativeUri |Yes |sztring |Az alap URI-karakterlánchoz hozzáadandó relatív URI-karakterlánc. |

* Ha a **baseUri** egy záró perjelet ér véget, az eredmény egyszerűen **baseUri** , amelyet a **relativeUri** követ.

* Ha a **baseUri** nem fejeződik be záró perjel, akkor két dolog egyike történik.

   * Ha a **baseUri** nem rendelkezik ferde vonallal (a "//" elől közel), az eredmény egyszerűen **baseUri** , amelyet a **relativeUri** követ.

   * Ha a **baseUri** tartalmaz némi perjelet, de a perjel nem végződik, akkor az utolsó perjeltől kezdve a rendszer eltávolítja a **baseUri** , és az eredményt a **baseUri** , majd a **relativeUri**.

Íme néhány példa:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
A teljes részletesség érdekében a **baseUri** és a **relativeUri** paramétereket a [3986-es RFC-dokumentum 5. szakasza](https://tools.ietf.org/html/rfc3986#section-5)határozza meg.

### <a name="return-value"></a>Visszatérési érték

Az alap és a relatív értékek abszolút URI azonosítóját jelölő sztring.

### <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan hozhat létre egy beágyazott sablonra mutató hivatkozást a fölérendelt sablon értéke alapján.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI kódolása.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |sztring |A kódolni kívánt érték. |

### <a name="return-value"></a>Visszatérési érték

Az URI-kódolású érték karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Egy URI-kódolású érték sztringjét adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |sztring |A karakterláncra konvertálandó URI-kódolású érték. |

### <a name="return-value"></a>Visszatérési érték

URI-kódolt érték dekódolt karakterlánca.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) azt szemlélteti, hogyan használható az URI, a uriComponent és a uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| uriOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Sztring | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Sztring | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
* Több sablon egyesítéséhez tekintse meg a [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](linked-templates.md)című témakört.
* Egy adott típusú erőforrás létrehozásakor a megadott számú alkalommal megismételheti az [erőforrás-iteráció az ARM-sablonokban](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md).
