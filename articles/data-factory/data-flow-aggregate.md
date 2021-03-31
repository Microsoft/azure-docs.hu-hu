---
title: Összesített átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan összesítheti a Azure Data Factory méretezési adatokat a leképezési adatfolyam összesített átalakításával.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531923"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Összesített átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az összesített átalakítás az adatstreamek oszlopainak összesítését határozza meg. A Kifejezésszerkesztő használatával különböző típusú összesítéseket adhat meg, például a SUM, a MIN, a MAX és a COUNT függvényt meglévő vagy számított oszlopok szerint csoportosítva.

## <a name="group-by"></a>Csoportosítási szempont

Válasszon ki egy meglévő oszlopot, vagy hozzon létre egy új számított oszlopot, amelyet Group By záradékként kíván használni az összesítéshez. Meglévő oszlop használatához válassza ki azt a legördülő listából. Új számított oszlop létrehozásához vigye a kurzort a záradék fölé, és kattintson a **számított oszlop** elemre. Ekkor megnyílik az [adatfolyam-kifejezés szerkesztője](concepts-data-flow-expression-builder.md). Miután létrehozta a számított oszlopot, adja meg a kimeneti oszlop nevét a **név** mezőben. Ha további Group By záradékot szeretne hozzáadni, vigye a kurzort egy meglévő záradék fölé, és kattintson a plusz ikonra.

![Összesítő transzformációs csoport beállítások szerint](media/data-flow/agg.png "Összesítő transzformációs csoport beállítások szerint")

Group By záradék használata nem kötelező egy összesített átalakításban.

## <a name="aggregate-columns"></a>Összesítő oszlopok

Az összesítési kifejezések létrehozásához nyissa meg az **összesítések** lapot. Egy meglévő oszlopot felülírhat összesítéssel, vagy létrehozhat egy új mezőt új névvel. Az összesítési kifejezés az oszlopnév-választó melletti jobb oldali mezőben van megadva. A kifejezés szerkesztéséhez kattintson a szövegmezőre, majd nyissa meg a Kifejezésszerkesztő párbeszédpanelt. További összesítő oszlopok hozzáadásához kattintson a **Hozzáadás** az oszlopok fölé, vagy a meglévő összesítő oszlop melletti plusz ikonra. Válassza az **oszlop hozzáadása** vagy az **oszlop hozzáadása mintát**. Minden összesítési kifejezésnek legalább egy összesítő függvényt tartalmaznia kell.

![Összesítési beállítások](media/data-flow/aggregate-columns.png "Összesítési beállítások")

> [!NOTE]
> Hibakeresési módban a Kifejezésszerkesztő nem hozhat létre összesítő függvényekből származó adat-előnézeteket. Az összesített átalakításokhoz tartozó adatelőnézetek megtekintéséhez a Kifejezésszerkesztő bezárásához és az adatelőnézet lapon megtekintheti az adatokat.

### <a name="column-patterns"></a>Oszlopminták

Az [oszlopos minták](concepts-data-flow-column-pattern.md) használatával azonos összesítést alkalmazhat az oszlopok egy halmazára. Ez akkor hasznos, ha a bemeneti séma sok oszlopát szeretné megtartani, mivel azok alapértelmezés szerint el vannak dobva. Használjon egy heurisztikus, például a `first()` bemeneti oszlopok megőrzését az összesítésen keresztül.

## <a name="reconnect-rows-and-columns"></a>Sorok és oszlopok újracsatolása

Az összesített átalakítások hasonlóak az SQL aggregált választó lekérdezésekhez. A GROUP BY záradékban vagy az összesítő függvények között nem szereplő oszlopok nem áramlanak át az összesített átalakítás kimenetére. Ha más oszlopokat is szeretne szerepeltetni az összesített kimenetben, hajtsa végre az alábbi módszerek egyikét:

* Használjon egy összesítő függvényt, `last()` például `first()` a vagy a lehetőséget a további oszlop felvételéhez.
* Csatlakoztassa újra az oszlopokat a kimeneti adatfolyamhoz a [saját illesztési minta](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)használatával.

## <a name="removing-duplicate-rows"></a>Ismétlődő sorok eltávolítása

Az összesített átalakítás általános használata eltávolítja vagy azonosítja a forrásadatok ismétlődő bejegyzéseit. Ez a folyamat deduplikált néven ismert. A kulcsok csoportosítása alapján, a kiválasztott ismétlődő sorok meghatározásához használja a választott heurisztikus lehetőséget. Gyakori heurisztikus:, `first()` , `last()` `max()` és `min()` . Használjon [oszlopos mintákat](concepts-data-flow-column-pattern.md) a szabály minden oszlopra való alkalmazásához, kivéve a csoportosítási oszlopokat.

![Deduplikáció](media/data-flow/agg-dedupe.png "Deduplikáció")

A fenti példában az oszlopok `ProductID` és a `Name` csoportosításhoz használják őket. Ha két sor ugyanazokkal az értékekkel rendelkezik a két oszlophoz, azok ismétlődésnek tekintendők. Ebben az összesített átalakításban a rendszer megőrzi az első sor egyezésének értékeit, és az összes többi el fog dobni. Az oszlop mintájának szintaxisa alapján az összes olyan oszlop, amelynek a neve nem, `ProductID` és le `Name` van képezve a meglévő oszlop nevére, és az első megfeleltetett sorok értéke. A kimeneti séma megegyezik a bemeneti sémával.

Az adatérvényesítési forgatókönyvek esetében a `count()` függvény használatával megszámolható, hogy hány ismétlődés van.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példában a bejövő adatfolyam `MoviesYear` és a csoportok oszlop szerint vannak sorba rendezve `year` . Az átalakítás egy összesített oszlopot hoz létre `avgrating` , amely kiértékeli az oszlop átlagát `Rating` . Ez az összesített átalakítás neve `AvgComedyRatingsByYear` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Csoportosítás példa alapján](media/data-flow/agg-script1.png "Csoportosítás példa alapján")

![Példa összesítésre](media/data-flow/agg-script2.png "Példa összesítésre")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Összesített adatfolyam-parancsfájl](media/data-flow/aggdfs1.png "Összesített adatfolyam-parancsfájl")

```MoviesYear```: Származtatott oszlop, amely az év és a cím oszlopokat definiálja ```AvgComedyRatingByYear``` : a vígjátékok átlagos minősítésének összesített átalakítása év szerint csoportosítva ```avgrating``` : az összesített érték tárolásához létrehozott új oszlop neve.

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Következő lépések

* Ablak alapú összesítés definiálása az ablak- [transzformáció](data-flow-window.md) használatával
