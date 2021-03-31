---
title: Felhasználói felületi definíció matematikai függvények létrehozása
description: A matematikai műveletek végrehajtásakor használandó függvények leírása.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096946"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition matematikai függvények

A függvények lehetővé teszik a matematikai műveletek végrehajtását.

## <a name="add"></a>add

Két számot ad vissza, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `3` :

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

A megadott számnál nagyobb vagy azzal egyenlő legnagyobb egész számot adja vissza.

Az alábbi példa a következőt adja vissza `4` :

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Az első számot felosztja a második számra, és visszaadja az eredményt. Az eredmény mindig egész szám.

Az alábbi példa a következőt adja vissza `2` :

```json
"[div(6, 3)]"
```

## <a name="floor"></a>padló

A legnagyobb olyan egész számot adja vissza, amely kisebb vagy egyenlő, mint a megadott szám.

Az alábbi példa a következőt adja vissza `3` :

```json
"[floor(3.14)]"
```

## <a name="max"></a>max.

A két szám nagyobb számát adja vissza.

Az alábbi példa a következőt adja vissza `2` :

```json
"[max(1, 2)]"
```

## <a name="min"></a>p

A két szám kisebb értékét adja vissza.

Az alábbi példa a következőt adja vissza `1` :

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

Az első szám felosztása a második szám alapján, és a maradékot adja vissza.

Az alábbi példa a következőt adja vissza `0` :

```json
"[mod(6, 3)]"
```

Az alábbi példa a következőt adja vissza `2` :

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Összeszoroz két számot, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `6` :

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>Rand

Egy véletlenszerű, a megadott tartományon belüli egész számot ad vissza. Ez a függvény nem állít elő kriptográfiailag biztonságos véletlenszerű számokat.

A következő példa visszatérhet `42` :

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>tartomány

A megadott tartományon belüli, egész számokból álló sorozatot hoz létre.

Az alábbi példa a következőt adja vissza `[1,2,3]` :

```json
"[range(1, 3)]"
```

## <a name="sub"></a>Sub

Kivonja a második számot az első számból, és visszaadja az eredményt.

Az alábbi példa a következőt adja vissza `1` :

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
