---
title: Adattípusok a sablonokban
description: A Azure Resource Manager-sablonokban elérhető adattípusokat ismerteti.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125140"
---
# <a name="data-types-in-arm-templates"></a>Adattípusok az ARM-sablonokban

Ez a cikk Azure Resource Manager-sablonok (ARM-sablonok) által támogatott adattípusokat ismerteti. A JSON-és a bicep-adattípusokat is tartalmazza.

## <a name="supported-types"></a>Támogatott típusok

Az ARM-sablonon belül a következő adattípusokat használhatja:

* array
* logikai
* int
* object
* a secureObject által jelzett módosító a bicep
* a secureString által jelzett módosító a bicep
* sztring

## <a name="arrays"></a>Tömbök

A tömbök bal oldali szögletes zárójeltel () kezdődnek, `[` és a jobb oldali szögletes zárójel () végére állnak `]` .

A JSON-ban egy tömb deklarálható egyetlen sorban vagy több sorban is. Minden elemet vesszővel kell elválasztani.

A bicep-ban a tömbnek több sorban kell deklarálnia. Ne használjon vesszőt az értékek között.

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

Egy tömb elemei azonos típusúak vagy különböző típusok lehetnek.

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

Logikai értékek megadásakor használja a vagy a értéket `true` `false` . Ne az idézőjelekkel rendelkező értéket vegye körül.

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

Egész értékek megadásakor ne használjon idézőjeleket.

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

A beágyazott paraméterekként átadott egész számok esetében az értékek tartományát az SDK vagy az üzembe helyezéshez használt parancssori eszköz korlátozza. Ha például a PowerShell használatával telepít egy sablont, az egész szám típusú érték-2147483648 és 2147483647 között lehet. Ha el szeretné kerülni ezt a korlátozást, adjon meg nagyméretű egész értékeket egy [paraméter fájljában](parameter-files.md). Az erőforrástípusok a saját korlátait alkalmazzák az egész tulajdonságok esetében.

## <a name="objects"></a>Objektumokat

Az objektumok bal oldali kapcsos zárójeltel () kezdődnek, `{` és a végén egy jobb oldali kapcsos zárójel ( `}` ) áll. Egy objektum minden tulajdonsága kulcsból és értékből áll. A kulcsot és az értéket kettőspont () választja el egymástól `:` .

A JSON-ban a kulcs idézőjelek közé kerül. Minden tulajdonság vesszővel elválasztva.

A bicep-ban a kulcs nem idézőjelek közé van csatolva. Ne használjon vesszőket a tulajdonságok között.

# <a name="json"></a>[JSON](#tab/json)

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

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Sztringek

A JSON-ban a karakterláncok idézőjelekkel vannak megjelölve. A bicep-ban a karakterláncok egyféle idézőjelekkel vannak megjelölve.

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

## <a name="secure-strings-and-objects"></a>Biztonságos karakterláncok és objektumok

A biztonságos karakterlánc ugyanazt a formátumot használja, mint a karakterlánc, és a Secure objektum ugyanazt a formátumot használja, mint az objektum. Ha egy paramétert biztonságos sztringre vagy biztonságos objektumra állít be, a paraméter értéke nem kerül a telepítési előzményekbe, és nincs naplózva. Ha azonban olyan tulajdonságra állítja be a biztonságos értéket, amely nem vár biztonságos értéket, az érték nem védett. Ha például egy biztonságos karakterláncot állít be egy címkére, az értéket egyszerű szövegként tárolja a rendszer. Biztonságos karakterláncok használata jelszavakhoz és titkos kulcsokhoz.

A bicep használatával hozzáadhatja a `@secure()` módosítót egy karakterlánchoz vagy objektumhoz.

A következő példa két biztonságos paramétert mutat be:

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

A sablon szintaxisának megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című témakört.
