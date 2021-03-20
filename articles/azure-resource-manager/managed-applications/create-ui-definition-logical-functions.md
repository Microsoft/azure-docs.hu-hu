---
title: Felhasználói felületi definíció logikai függvények létrehozása
description: Leírja a logikai műveletek végrehajtásához szükséges függvényeket.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096952"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition logikai függvények

Ezek a függvények feltételes kifejezésekben is használhatók. Előfordulhat, hogy egyes függvények nem támogatják az összes JSON-adattípust.

## <a name="and"></a>és

A értéket adja vissza `true` , ha az összes paraméter ki van értékelve `true` . Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a következőt adja vissza `true` :

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>összefonódik

Az első nem null paraméter értékét adja vissza. Ez a függvény az összes JSON-adattípust támogatja.

A feltételezések `element1` és `element2` nincsenek meghatározva. Az alábbi példa a következőt adja vissza `"Contoso"` :

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

Ez a függvény különösen olyan választható hívás kontextusában hasznos, amely az oldal betöltése után felhasználói beavatkozás miatt történik. Ilyen például, ha a felhasználói felületen egy mezőre helyezett megkötések egy másik, **kezdetben nem látható** mező aktuálisan kijelölt értékével függenek. Ebben az esetben `coalesce()` felhasználható arra, hogy a függvényt az oldal betöltési ideje alatt szintaktikai módon érvényes legyen, miközben a felhasználó a mezővel folytatott kommunikációhoz szükséges hatást gyakorolja.

Vegye figyelembe `DropDown` , hogy ez lehetővé teszi, hogy a felhasználó több különböző típusú adatbázis közül válasszon:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Ha egy másik mező műveletét szeretné felvenni a mező aktuálisan kiválasztott értékére, használja a parancsot az `coalesce()` itt látható módon:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Erre azért van szükség, mert az `databaseType` kezdetben nem látható, ezért nem rendelkezik értékkel. Ennek hatására a teljes kifejezés kiértékelése nem megfelelő.

## <a name="equals"></a>egyenlő

Azt adja vissza `true` , hogy mindkét paraméter ugyanazzal a típussal és értékkel rendelkezik-e. Ez a függvény az összes JSON-adattípust támogatja.

Az alábbi példa a következőt adja vissza `true` :

```json
"[equals(0, 0)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[equals('web', 'web')]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

Azt adja vissza `true` , hogy az első paraméter szigorúan nagyobb-e, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `false` :

```json
"[greater(1, 2)]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

Visszaadja `true` , ha az első paraméter nagyobb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

Egy értéket ad vissza, attól függően, hogy a feltétel igaz vagy hamis. Az első paraméter a teszt feltétele. A második paraméter a visszatérési érték, ha a feltétel igaz. A harmadik paraméter a visszatérési érték, ha a feltétel hamis.

A következő minta visszaadása `yes` .

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

Azt adja vissza `true` , hogy az első paraméter szigorúan kisebb-e a második paraméternél. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[less(1, 2)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

Visszaadja `true` , ha az első paraméter kisebb vagy egyenlő, mint a második paraméter. Ez a függvény csak szám és karakterlánc típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

Visszaadja `true` , ha a paraméter kiértékelése a következőre történik: `false` . Ez a függvény csak logikai típusú paramétereket támogat.

Az alábbi példa a következőt adja vissza `true` :

```json
"[not(false)]"
```

Az alábbi példa a következőt adja vissza `false` :

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>vagy

Azt adja vissza `true` , hogy a paraméterek közül legalább az egyik a következőre van-e kiértékelve: `true` . Ez a függvény két vagy több paramétert támogat, amelyek csak logikai típusúak.

Az alábbi példa a következőt adja vissza `true` :

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

Az alábbi példa a következőt adja vissza `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>Következő lépések

* A Azure Resource Manager bevezetését lásd: [Azure Resource Manager áttekintése](../management/overview.md).
