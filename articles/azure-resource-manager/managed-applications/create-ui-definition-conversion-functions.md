---
title: Felhasználói felületi definíció átalakítási funkcióinak létrehozása
description: Az adattípusok és a kódolások közötti értékek konvertálásakor használandó függvények leírása.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096959"
---
# <a name="createuidefinition-conversion-functions"></a>CreateUiDefinition átalakítási függvények

Ezek a függvények a JSON-adattípusok és a kódolások közötti értékek átalakítására használhatók.

## <a name="bool"></a>logikai

A paramétert logikai értékre alakítja. Ez a függvény a következő típusú paramétereket támogatja: number, string és Boolean. A JavaScript logikai értékeihez hasonlóan bármilyen érték, kivéve `0` vagy `'false'` visszaadja `true` .

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool(1)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[bool(0)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool(true)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

A paraméter dekódolása egy Base-64 kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"Contoso"` :

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

A paraméter dekódolása egy URL-kódolású karakterláncból. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

A paramétert egy Base-64 kódolású karakterláncba kódolja. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"Q29udG9zbw=="` :

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Kódolja a paramétert egy URL-kódolású karakterláncba. Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

A paramétert egy lebegőpontos pontra konvertálja. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a következőt adja vissza `1.0` :

```json
"[float('1.0')]"
```

Az alábbi példa a következőt adja vissza `2.9` :

```json
"[float(2.9)]"
```

## <a name="int"></a>int

Egy egész számra konvertálja a paramétert. Ez a függvény a Number és a String típusú paramétereket támogatja.

Az alábbi példa a következőt adja vissza `1` :

```json
"[int('1')]"
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

A paramétert natív típusra konvertálja. Más szóval ez a függvény a inverze `string()` . Ez a függvény csak karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `1` :

```json
"[parse('1')]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[parse('true')]"
```

Az alábbi példa a következőt adja vissza `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

Az alábbi példa a következőt adja vissza `{"type":"webapp"}` :

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>sztring

A paramétert karakterlánccá alakítja. Ez a függvény az összes JSON-adattípus paramétereit támogatja.

Az alábbi példa a következőt adja vissza `"1"` :

```json
"[string(1)]"
```

Az alábbi példa a következőt adja vissza `"2.9"` :

```json
"[string(2.9)]"
```

Az alábbi példa a következőt adja vissza `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

Az alábbi példa a következőt adja vissza `"{"type":"webapp"}"` :

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
