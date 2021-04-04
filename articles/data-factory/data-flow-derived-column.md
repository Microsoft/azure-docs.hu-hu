---
title: Származtatott oszlop átalakítása a leképezési adatfolyamban
description: Megtudhatja, hogyan alakíthatja át az adatmennyiséget a Azure Data Factory a leképezési adatfolyam származtatott oszlopainak átalakításával.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532030"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Származtatott oszlop átalakítása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A származtatott oszlop átalakításával új oszlopokat hozhatja ki az adatfolyamban, vagy módosíthatja a meglévő mezőket.

## <a name="create-and-update-columns"></a>Oszlopok létrehozása és frissítése

Származtatott oszlop létrehozásakor létrehozhat egy új oszlopot, vagy frissítheti a meglévőket. Az **oszlop** szövegmezőbe írja be a létrehozandó oszlopot. A séma meglévő oszlopának felülbírálásához használhatja az oszlop legördülő menüt. A származtatott oszlop kifejezésének létrehozásához kattintson a **kifejezés megadása** szövegmezőre. Megkezdheti a kifejezés beírását, vagy megnyithatja a kifejezés-szerkesztőt a logika létrehozásához.

![Származtatott oszlop beállításai](media/data-flow/create-derive-column.png "Származtatott oszlop beállításai")

További származtatott oszlopok hozzáadásához kattintson a **Hozzáadás** az oszlopok fölé, vagy a meglévő származtatott oszlop melletti plusz ikonra. Válassza az **oszlop hozzáadása** vagy az **oszlop hozzáadása mintát**.

![Új származtatott oszlop kiválasztása](media/data-flow/add-derived-column.png "Új származtatott oszlop kiválasztása")

### <a name="column-patterns"></a>Oszlopminták

Olyan esetekben, ahol a séma nincs explicit módon definiálva, vagy ha nagy mennyiségű oszlopot szeretne frissíteni, érdemes lehet oszlop-mintázatokat létrehozni. Az oszlopok mintázatai lehetővé teszik az oszlopok egyeztetését az oszlop metaadatainak alapján, és származtatott oszlopok létrehozása az egyes egyező oszlopokhoz. További információért olvassa el, [hogyan hozhat létre oszlopos mintákat](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) a származtatott oszlop-átalakításban.

![Oszlopminták](media/data-flow/column-pattern-derive.png "Oszlopminták")

## <a name="building-schemas-using-the-expression-builder"></a>Sémák kiépítése a Expression Builder használatával

A leképezési adatfolyam [-Kifejezésszerkesztő](concepts-data-flow-expression-builder.md)használatakor **a származtatott oszlopok szakaszban** hozhatja létre, szerkesztheti és kezelheti a származtatott oszlopokat. Az átalakításban létrehozott vagy módosított összes oszlop fel van sorolva. Az oszlop nevére kattintva interaktív módon kiválaszthatja, hogy melyik oszlopot vagy mintázatot kívánja szerkeszteni. További oszlop hozzáadásához válassza az **új létrehozása** lehetőséget, és adja meg, hogy egyetlen oszlopot vagy mintát kíván-e hozzáadni.

![Új oszlop létrehozása](media/data-flow/derive-add-column.png "Új oszlop létrehozása")

Összetett oszlopok használatakor aloszlopokat is létrehozhat. Ehhez kattintson a plusz ikonra bármely oszlop mellett, majd válassza az **aloszlop hozzáadása** lehetőséget. Az összetett típusok az adatforgalomban való kezelésével kapcsolatos további információkért lásd: [JSON-kezelés a leképezési folyamatokban](format-json.md#mapping-data-flow-properties).

![Aloszlop hozzáadása](media/data-flow/derive-add-subcolumn.png "Aloszlop hozzáadása")

Az összetett típusok az adatforgalomban való kezelésével kapcsolatos további információkért lásd: [JSON-kezelés a leképezési folyamatokban](format-json.md#mapping-data-flow-properties).

![Összetett oszlop hozzáadása](media/data-flow/derive-complex-column.png "Oszlopok hozzáadása")

### <a name="locals"></a>Figyelőpontjaival

Ha több oszlop között osztja meg a logikát, vagy a logikát szeretné felosztják, a származtatott oszlopok átalakításán belül is létrehozhat helyi beállításokat. A helyi olyan logikai halmaz, amely nem lesz propagálva a következő átalakításra. A helyi beállítások a kifejezés-szerkesztőben hozhatók létre, ha a **kifejezés elemek elemre** kattint, és kiválasztja a **területi beállításokat**. Hozzon létre egy újat az **új létrehozása** lehetőség kiválasztásával.

![Helyi létrehozása](media/data-flow/create-local.png "Helyi létrehozása")

A területi beállítások hivatkozhatnak bármely kifejezés elemre egy származtatott oszlopból, beleértve a függvényeket, a bemeneti sémát, a paramétereket és az egyéb területi beállításokat is. Ha más területi beállításokra hivatkozik, a sorrend nem számít, mivel a hivatkozott helyi verziónak a jelenleginek kell lennie.

![Helyi 2 létrehozása](media/data-flow/create-local-2.png "Helyi 2 létrehozása")

Ha egy származtatott oszlopban lévő helyire szeretne hivatkozni, kattintson a helyi **elemre a kifejezés elemek** nézetből, vagy hivatkozzon rá a neve előtt található kettőspontra. Például egy Helyi1 nevű helyi hivatkozásra a következő hivatkozik: `:local1` . Helyi definíció szerkesztéséhez vigye a kurzort a kifejezés elemei nézetben, és kattintson a ceruza ikonra.

![Helyi beállítások használata](media/data-flow/using-locals.png "Helyi beállítások használata")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű származtatott oszlop `CleanData` , amely egy bejövő adatfolyamot használ `MoviesYear` , és két származtatott oszlopot hoz létre. Az első származtatott oszlop a `Rating` minősítés értékének egész típusként való lecserélése az oszlopra. A második származtatott oszlop egy minta, amely minden olyan oszlopnak megfelel, amelynek a neve "Movies" karakterrel kezdődik. Minden egyező oszlop esetében egy olyan oszlopot hoz létre, `movie` amely megegyezik az "movie_" előtaggal ellátott egyező oszlop értékével. 

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Példa származtatása](media/data-flow/derive-script.png "Példa származtatása")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Következő lépések

- További információ a [leképezési adatfolyam kifejezésének nyelvéről](data-flow-expression-functions.md).
