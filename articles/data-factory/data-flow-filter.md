---
title: Átalakítás szűrése a leképezési adatfolyamban
description: Sorok kiszűrése a szűrő átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84112807"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Átalakítás szűrése a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A szűrő átalakítások lehetővé teszik a sorok szűrését egy adott feltétel alapján. A kimeneti adatfolyam tartalmazza a szűrési feltételnek megfelelő összes sort. A szűrő átalakítása hasonló a WHERE záradékhoz az SQL-ben.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfiguráció

A szűrési feltétel kifejezésének megadásához használja az adatfolyam-Kifejezésszerkesztő kifejezést. A Kifejezésszerkesztő megnyitásához kattintson a kék mezőre. A szűrési feltételnek logikai típusúnak kell lennie. A kifejezések létrehozásával kapcsolatos további tudnivalókért tekintse meg a Kifejezésszerkesztő [dokumentációját](concepts-data-flow-expression-builder.md) .

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű szűrő-átalakítás, `FilterBefore1960` amely a bejövő adatfolyamot veszi igénybe `CleanData` . A szűrési feltétel a kifejezés `year <= 1960` .

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Következő lépések

Oszlopok kiszűrése az [átalakítás kiválasztása](data-flow-select.md)
