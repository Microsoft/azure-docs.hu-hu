---
title: Feltételes felosztású átalakítás a leképezési adatfolyamban
description: Adatfelosztás különböző streamekre a feltételes felosztás átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83800088"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Feltételes felosztású átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A feltételes felosztott átalakítás az adatsorokat különböző streamekre irányítja a megfelelő feltételek alapján. A feltételes felosztott átalakítás a programozási nyelv eset döntési struktúrájához hasonlít. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján a megadott adatfolyamra irányítja az adatsort.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Konfiguráció

A **felosztás bekapcsolva** beállítás határozza meg, hogy az adatsorok az első egyező adatfolyamra vagy minden adatfolyamra vonatkoznak-e.

Az adatfolyam-Kifejezésszerkesztő használatával adjon meg egy kifejezést a felosztott feltételhez. Új feltétel hozzáadásához kattintson a plusz ikonra egy meglévő sorban. Egy alapértelmezett adatfolyamot is hozzáadhat a feltételnek nem megfelelő sorokhoz.

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű feltételes felosztású átalakítás, `SplitByYear` amely a bejövő adatfolyamot veszi át `CleanData` . Ez a transzformáció két felosztott feltételt `year < 1960` és `year > 1980` . `disjoint` hamis, mert az adatértékek az első egyező feltételnek felelnek meg. Minden, az első feltételnek megfelelő sor a kimeneti adatfolyamba kerül `moviesBefore1960` . A második feltételnek megfelelő összes fennmaradó sor a kimeneti adatfolyamra mutat `moviesAFter1980` . Az összes többi sor az alapértelmezett adatfolyamon keresztül folyik `AllOtherMovies` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Következő lépések

A feltételes felosztással használt közös adatfolyam-átalakítások az [összekapcsolási transzformáció](data-flow-join.md), a [keresési átalakítás](data-flow-lookup.md)és az [átalakítás kiválasztása](data-flow-select.md)
