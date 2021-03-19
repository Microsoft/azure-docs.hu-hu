---
title: Kimutatás-átalakítás a leképezési adatfolyamban
description: Sorokból oszlopokból származó adatok kimutatása Azure Data Factory leképezési adatforgalom pivot-transzformációjának használatával
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086659"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Kimutatás-átalakítás a leképezési adatfolyamban


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A pivot transzformáció használatával több oszlopot hozhat létre egyetlen oszlop egyedi soraiból. A pivot egy aggregációs átalakítás, ahol kiválaszthatja a Group By Columns és az [összesítő függvények](data-flow-expression-functions.md#aggregate-functions)használatával létrehozott pivot oszlopokat.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Konfiguráció

A pivot-átalakításhoz három különböző bemenet szükséges: csoportosítás oszlopok szerint, a pivot kulcsa, valamint a felváltott oszlopok előállítása

### <a name="group-by"></a>Csoportosítási szempont

![Csoportosítás beállítások szerint](media/data-flow/pivot2.png "Csoportosítás beállítások szerint")

Válassza ki, hogy mely oszlopok legyenek összesítve az elforgatott oszlopok felett. A kimeneti adatok egyetlen sorba csoportosítják az összes sort az azonos csoportba tartozó értékek alapján. A kimutatott oszlopban végzett összesítés az egyes csoportokon keresztül történik.

Ez a szakasz nem kötelező. Ha nincs kiválasztva csoportosítási oszlop, a rendszer összesíti a teljes adatfolyamot, és csak egy sort fog kijelölni.

### <a name="pivot-key"></a>Pivot kulcsa

![Pivot kulcsa](media/data-flow/pivot3.png "Pivot kulcsa")

A pivot kulcs az az oszlop, amelynek a soraiban az új oszlopok bekerülnek. Alapértelmezés szerint a pivot-átalakítás új oszlopot hoz létre minden egyes egyedi sor értékhez.

Az érték feliratú szakaszban megadhatja a **tagolni** kívánt sorok értékeit. A rendszer csak az ebben a szakaszban megadott sor értékeket fogja kijelölni. A **Null érték** engedélyezésekor a rendszer az oszlopban lévő null értékek esetében egy elforgatott oszlopot hoz létre.

### <a name="pivoted-columns"></a>Elforgatott oszlopok

![Elforgatott oszlopok](media/data-flow/pivot4.png "Elforgatott oszlopok")

Minden olyan egyedi pivot-kulcs értéknél, amely egy oszlopra változik, az egyes csoportok összesített sorát kell kiállítania. Kimutatási kulcsban több oszlopot is létrehozhat. Minden egyes pivot oszlopnak legalább egy [összesítő függvényt](data-flow-expression-functions.md#aggregate-functions)tartalmaznia kell.

**Oszlop neve minta:** Válassza ki, hogyan szeretné formázni az egyes pivot oszlopok oszlopának nevét. A megadott oszlop neve a pivot kulcs értékének, az oszlop előtagja és a választható előtag, a megfelelő és a középső karakter kombinációja lesz. 

**Oszlop elrendezése:** Ha kimutatási kulcsban több pivot oszlopot is létrehoz, válassza ki, hogyan szeretné megrendelni az oszlopok sorrendjét. 

**Oszlop előtagja:** Ha kimutatási kulcsban több pivot oszlopot is létrehoz, adja meg az egyes oszlopok oszlop-előtagját. Ez a beállítás nem kötelező, ha csak egy elforgatott oszlop van.

## <a name="help-graphic"></a>Súgó grafikus

Az alábbi Súgó ábrán látható, hogy a különböző pivot-összetevők hogyan hatnak egymással

![A pivot súgójának grafikája](media/data-flow/pivot5.png "Pivot Súgó ábrája")

## <a name="pivot-metadata"></a>Kimutatás metaadatainak

Ha nem ad meg értéket a pivot kulcs konfigurációjában, a rendszer futási időben dinamikusan létrehozza a tagolt oszlopokat. A felforgatott oszlopok száma megegyezik az egyedi pivot kulcs értékeinek számával, szorozva a pivot oszlopainak számával. Mivel ez lehet változó szám, az UX nem jeleníti meg az oszlop metaadatait az **ellenőrzés** lapon, és nem lesz oszlop-propagálás. Ezeknek [az oszlopoknak](concepts-data-flow-column-pattern.md) az átalakításához használja az adatfolyamatok leképezési funkcióit. 

Ha meghatározott pivot-kulcs értékek vannak megadva, a rendszer a kimutatásban szereplő oszlopokat a metaadatokban fogja megjeleníteni. Az oszlopok nevei a vizsgálat és a fogadó leképezésében lesznek elérhetők.

### <a name="generate-metadata-from-drifted-columns"></a>Metaadatok előállítása az elsodródott oszlopokból

A pivot a sorok értékei alapján dinamikusan generálja az új oszlopnevek nevét. Ezeket az új oszlopokat felveheti azokba a metaadatokba, amelyeket később az adatfolyamban lehet hivatkozni. Ehhez használja az [elsodródott](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) gyors műveletet az adatelőnézetben. 

![Kimutatás oszlopok alapján](media/data-flow/newpivot1.png "Elsodródott pivot oszlopok leképezése")

### <a name="sinking-pivoted-columns"></a>Elforgatott oszlopok

Bár a csuklós oszlopok dinamikusak, továbbra is beírhatók a célhely adattárba. Engedélyezze a **séma drift használatát** a fogadó beállításaiban. Ez lehetővé teszi, hogy olyan oszlopokat írjon, amelyek nem szerepelnek a metaadatokban. Az új dinamikus nevek nem jelennek meg az oszlop metaadataiban, de a séma drift beállítás lehetővé teszi az adatok kirakodását.

### <a name="rejoin-original-fields"></a>Eredeti mezők újracsatlakoztatása

A pivot átalakítás csak a Group By és a csuklós oszlopokat fogja feltervezni. Ha azt szeretné, hogy a kimeneti adatok más bemeneti oszlopokat is tartalmazzon, használjon [saját illesztési](data-flow-join.md#self-join) mintát.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Példa

A konfiguráció szakaszban látható képernyők a következő adatfolyam-szkripttel rendelkeznek:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Következő lépések

Az oszlopok értékeinek sorokba való bekapcsolásához próbálja meg az [unpivot transzformációt](data-flow-unpivot.md) . 
