---
title: Felhasználói felület definíciójának gyűjteménye függvények létrehozása
description: A gyűjtemények, például tömbök és objektumok használatakor használandó függvények leírása.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096964"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition-gyűjtési függvények

Ezek a függvények gyűjtemények, például JSON-karakterláncok, tömbök és objektumok használatával használhatók.

## <a name="contains"></a>contains

Azt adja vissza `true` , hogy egy karakterlánc tartalmazza-e a megadott alkarakterláncot, egy tömb tartalmazza a megadott értéket, vagy egy objektum tartalmazza a megadott kulcsot.

### <a name="example-string-contains"></a>Példa: karakterlánc tartalmaz

Az alábbi példa a következőt adja vissza `true` :

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Példa: tömb tartalmazza

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `false` :

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Példa: az objektum tartalmaz

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>üres

Azt adja vissza, `true` hogy a karakterlánc, tömb vagy objektum null értékű vagy üres-e.

### <a name="example-string-empty"></a>Példa: üres karakterlánc

Az alábbi példa a következőt adja vissza `true` :

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Példa: üres tömb

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Példa: üres objektum

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Példa: null és nem definiált

`element1`A feltételezés `null` vagy nincs meghatározva. Az alábbi példa a következőt adja vissza `true` :

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter (szűrő)

Egy új tömböt ad vissza, miután alkalmazta a lambda függvényként megadott szűrési logikát. Az első paraméter a szűréshez használandó tömb. A második paraméter a lambda függvény, amely meghatározza a szűrési logikát.

A következő minta a tömböt adja vissza `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>első

A megadott karakterlánc első karakterét adja vissza. a megadott tömb első értéke; vagy a megadott objektum első kulcsa és értéke.

### <a name="example-string-first"></a>Példa: először a sztring

Az alábbi példa a következőt adja vissza `"c"` :

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Példa: először a tömb

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `1` :

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Példa: először az objektum

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `{"key1": "Linux"}` :

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>utolsó

A megadott karakterlánc utolsó karakterét adja vissza, a megadott tömb utolsó értékét, vagy a megadott objektum utolsó kulcsát és értékét.

### <a name="example-string-last"></a>Példa: utolsó sztring

Az alábbi példa a következőt adja vissza `"o"` :

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Példa: a tömb utolsó

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `3` :

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Példa: az objektum utolsó

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `{"key2": "Windows"}` :

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>hossz

Egy sztringben szereplő karakterek számát, egy tömbben lévő értékek számát, illetve egy objektum kulcsainak számát adja vissza.

### <a name="example-string-length"></a>Példa: karakterlánc hossza

Az alábbi példa a következőt adja vissza `7` :

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Példa: tömb hossza

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `3` :

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Példa: objektum hossza

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

Egy új tömböt ad vissza, miután meghívott egy lambda függvényt egy megadott tömbben. Az első paraméter a lambda függvényhez használandó tömb. A második paraméter a lambda függvény.

A következő minta egy új tömböt ad vissza, amely minden érték duplájára van. Az eredmény: `[2, 4, 6]` .

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

Az alábbi minta egy új tömböt ad vissza `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>kihagyása

Megkerüli a gyűjtemény megadott számú elemét, majd visszaadja a többi elemet.

### <a name="example-string-skip"></a>Példa: karakterlánc kihagyása

Az alábbi példa a következőt adja vissza `"app"` :

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Például: tömb kihagyása

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `[3]` :

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Példa: objektum kihagyása

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
Az alábbi példa a következőt adja vissza `{"key2": "Windows"}` :

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>felosztás

Olyan karakterláncok tömbjét adja vissza, amelyek az elválasztó által körülhatárolt alsztringeket tartalmazzák.

A következő minta a tömböt adja vissza `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Megadott számú folytonos karaktert ad vissza a karakterlánc elejétől, a tömb elejétől megadott számú folytonos értékből, vagy az objektum elejétől megadott számú folytonos kulcsból és értékből.

### <a name="example-string-take"></a>Példa: string Take

Az alábbi példa a következőt adja vissza `"web"` :

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Példa: Array Take

Visszaadott `element1` érték `[1, 2, 3]` . Az alábbi példa a következőt adja vissza `[1, 2]` :

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Példa: objektum Take

Visszaadott `element1` érték:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Az alábbi példa a következőt adja vissza `{"key1": "Linux"}` :

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
