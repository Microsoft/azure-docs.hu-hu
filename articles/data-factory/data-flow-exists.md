---
title: Létező átalakítás a leképezési adatfolyamban
description: Meglévő sorok ellenőrzése a létező átalakítással Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82982632"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Létező átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A létező transzformáció egy sor-szűrési átalakítás, amely ellenőrzi, hogy az adatai más forrásban vagy adatfolyamban léteznek-e. A kimeneti adatfolyam tartalmazza a bal oldali stream összes olyan sorát, amely vagy létezik, vagy nem létezik a megfelelő streamben. A létező átalakítás hasonló a következőhöz: ```SQL WHERE EXISTS``` és ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Konfiguráció

1. Válassza ki, hogy melyik adatfolyamot kívánja megkeresni a **megfelelő stream** legördülő menüben.
1. Adja meg, hogy szeretné-e, ha a létező vagy nem létező **adattípust** szeretné használni.
1. Válassza ki, hogy szeretne-e **Egyéni kifejezést** használni.
1. Válassza ki, hogy mely fő oszlopokat kívánja összehasonlítani a meglévő feltételekkel. Alapértelmezés szerint az adatforgalom az egyes adatfolyamok egy oszlopa között keresi az egyenlőséget. Számított érték alapján történő összehasonlításhoz vigye az egérmutatót az oszlop legördülő menüjére, és válassza a **számított oszlop** lehetőséget.

![Létező beállítások](media/data-flow/exists.png "létezik 1")

### <a name="multiple-exists-conditions"></a>Több meglévő feltétel

Ha több oszlopot szeretne összehasonlítani az egyes adatfolyamokból, adjon hozzá egy új létező feltételt egy meglévő sor melletti plusz ikonra kattintva. Minden további feltétel egy "és" utasítással csatlakozik. A két oszlop összehasonlítása megegyezik a következő kifejezéssel:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Egyéni kifejezés

Ha olyan szabad formátumú kifejezést szeretne létrehozni, amely a "és a" és az "egyenlő" operátort is tartalmazza, válassza az **egyéni kifejezés** mezőt. Adjon meg egy egyéni kifejezést az adatfolyam-kifejezés-szerkesztőn keresztül a kék mezőre kattintva.

![Meglévő egyéni beállítások](media/data-flow/exists1.png "létezik egyéni")

## <a name="broadcast-optimization"></a>Szórás optimalizálása

![Szórásos csatlakozás](media/data-flow/broadcast.png "Szórásos csatlakozás")

Az illesztések, a keresések és a meglévő átalakítások esetében, ha az egyik vagy mindkét adatfolyam a feldolgozó csomóponti memóriába illeszkedik, a **szórás** engedélyezésével optimalizálhatja a teljesítményt. Alapértelmezés szerint a Spark-motor automatikusan eldönti, hogy az egyik oldalt kívánja-e közvetíteni. A közvetíteni kívánt oldal manuális kiválasztásához válassza a **rögzített** lehetőséget.

Nem ajánlott letiltani a szórást a **kikapcsolási** lehetőséggel, kivéve, ha az illesztések időtúllépési hibákkal futnak.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű létező átalakítás, `checkForChanges` amely a streamet `NameNorm2` és a jobb oldali streamet veszi át `TypeConversions` .  A létező feltétel az a kifejezés `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` , amely igaz értéket ad vissza, ha az `EMPID` `Region` egyes streamek és az egyes adatfolyamok mindkét oszlopa megegyezik. Mivel a létezés ellenőrzése folyamatban van, `negate` hamis. Nem engedélyezzük az optimalizálás lapon a szórást, így az `broadcast` érték van `'none'` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Létezik példa](media/data-flow/exists-script.png "Létezik példa")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Következő lépések

Hasonló átalakítások [Keresés](data-flow-lookup.md) és [Csatlakozás](data-flow-join.md).
