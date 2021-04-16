---
title: Adattípusok sablonokban
description: A sablonokban elérhető adattípusokat Azure Resource Manager ismerteti.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538059"
---
# <a name="data-types-in-arm-templates"></a>Adattípusok ARM-sablonokban

Ez a cikk a sablonsablonok (ARM-sablonok Azure Resource Manager támogatott adattípusokat ismerteti. A JSON és a Bicep adattípusokat is lefedi.

## <a name="supported-types"></a>Támogatott típusok

Az ARM-sablonon belül a következő adattípusokat használhatja:

* array
* logikai
* int
* object
* secureObject – a Bicepben módosító jelzi
* secureString – módosító jelzi a Bicepben
* sztring

## <a name="arrays"></a>Tömbök

A tömbök bal oldali szögletes zárójellel () kezdődnek, és `[` jobb oldali szögletes zárójellel ( ) `]` végződnek.

A JSON-ban egy tömb deklarálható egyetlen sorban vagy több sorban. Minden elemet vessző választ el egymástól.

A Bicepben egy tömböt több sorban kell deklarálni. Ne használjon vesszőket az értékek között.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Egy tömb elemei azonos típusúak vagy különböző típusok is lehetnek.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Logikai érték

Logikai értékek megadásakor használja a vagy a `true` `false` értéket. Ne vegye idézőjelek közé az értéket.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Egész számok

Egész szám értékek megadásakor ne használjon idézőjeleket.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

A beágyazott paraméterekként átadott egész számokkal az értékek tartományát az üzembe helyezéshez használt SDK vagy parancssori eszköz is korlátozhatja. Ha például a PowerShell használatával telepít egy sablont, az egész típusok a -2147483648 és a 2147483647 tartományba esnek. A korlátozás elkerüléséhez adjon meg nagy egész számértékeket egy [paraméterfájlban.](parameter-files.md) Az erőforrástípusok saját korlátokat alkalmaznak az egész szám típusú tulajdonságokra.

## <a name="objects"></a>Objektumokat

Az objektumok egy bal oldali kapcsos zárójelet ( ) tartalmaznak, és egy jobb oldali kapcsos `{` zárójelet ( ) végződnek. `}` Egy objektum minden tulajdonsága kulcsból és értékből áll. A kulcsot és az értéket kettőspont ( ) választja el `:` egymástól.

# <a name="json"></a>[JSON](#tab/json)

A JSON-ban a kulcs idézőjelek között van. Minden tulajdonságot vessző választ el egymástól.

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

A Bicepben a kulcs nem idézőjelek között van. A tulajdonságok között ne használjon vesszőt.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

A tulajdonság-hozzáférések egy objektum tulajdonságainak elérésére használhatók. Ezek a operátorral vannak `.` összeépítve. Például:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

Az előző deklaráció alapján az x.y.z kifejezés kiértékeli a "Hello" literális sztringet. Hasonlóképpen, az x.q kifejezés a 42-es literál egész számra van kiértékelve.

A tulajdonság-hozzáférési bármilyen objektummal használható. Ide tartoznak az objektumtípusok és az objektum-literálok paraméterei és változói. A tulajdonság-hozzáférés használata nem objektum típusú kifejezésen hiba.

---

## <a name="strings"></a>Sztringek

A JSON-ban a sztringeket dupla idézőjelek jelölik. A Bicepben a sztringek egyszeres idézőjelekkel vannak megjelölve.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Biztonságos sztringek és objektumok

A biztonságos sztring ugyanazt a formátumot használja, mint a sztring, a biztonságos objektum pedig ugyanazt a formátumot használja, mint az objektum. Ha egy paramétert biztonságos sztringre vagy biztonságos objektumra ad meg, a paraméter értéke nem lesz mentve az üzembe helyezési előzményekbe, és nem lesz naplózva. Ha azonban ezt a biztonságos értéket egy olyan tulajdonságra adja meg, amely nem vár biztonságos értéket, az érték nem lesz védve. Ha például egy biztonságos sztringet címkére ad meg, a rendszer egyszerű szövegként tárolja ezt az értéket. Használjon biztonságos sztringeket jelszavakhoz és titkos kulcsokhoz.

A Bicep-hez hozzáadja `@secure()` a módosítót egy sztringhez vagy objektumhoz.

Az alábbi példa két biztonságos paramétert mutat be:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Következő lépések

A sablon szintaxisával kapcsolatos további információkért lásd: [Az ARM-sablonok struktúrája és szintaxisa.](template-syntax.md)
