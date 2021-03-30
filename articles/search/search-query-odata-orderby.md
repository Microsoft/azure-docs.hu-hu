---
title: OData rendelés – hivatkozás alapján
titleSuffix: Azure Cognitive Search
description: Szintaxissal és nyelvi dokumentációval az Azure Cognitive Search-lekérdezésekben használt Order by használatával.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88926830"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby szintaxis az Azure-ban Cognitive Search

 A [OData **$OrderBy** paraméterrel](query-odata-filter-orderby-syntax.md) egyéni rendezési sorrendet alkalmazhat az Azure Cognitive Search keresési eredményeire. Ez a cikk részletesen ismerteti **$OrderBy** szintaxisát. A keresési eredmények megjelenítésével kapcsolatos további általános információkért lásd: a keresési eredmények **$OrderBy** használata [Az Azure-ban Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

A **$OrderBy** paraméter legfeljebb 32 **ORDER-BY záradék** vesszővel tagolt listáját fogadja el. Az ORDER-BY záradék szintaxisát a következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) írja le:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

Az egyes záradékok rendezési feltételekkel rendelkeznek, opcionálisan egy rendezési irány után ( `asc` növekvő vagy `desc` csökkenő sorrendben). Ha nem ad meg irányt, az alapértelmezett érték a növekvő. Ha a mezőben null értékek vannak, akkor először a null értékek jelennek meg, ha a rendezés `asc` és az utolsó, ha a rendezés `desc` .

A rendezési feltétel lehet egy mező elérési útja `sortable` vagy a [`geo.distance`](search-query-odata-geo-spatial-functions.md) vagy a függvények egyik hívása is [`search.score`](search-query-odata-search-score-function.md) .

Ha több dokumentumra ugyanazok a rendezési feltételek tartoznak, és a `search.score` függvény nincs használatban (például ha egy numerikus mező alapján rendezi a sort, `Rating` és a három dokumentum minősítése 4), a kapcsolatok a dokumentum pontszáma szerint csökkenő sorrendben lesznek megszakítva. Ha a dokumentumok pontszámai megegyeznek (például ha nincs megadva teljes szöveges keresési lekérdezés a kérelemben), akkor a kötött dokumentumok relatív sorrendje nem kötelező.

Több rendezési feltételt is megadhat. A kifejezések sorrendje határozza meg a végső rendezési sorrendet. Például a pontszám csökkenő rendezéséhez, amelyet a minősítés követ, a szintaxis a következő: `$orderby=search.score() desc,Rating desc` .

A `geo.distance` **$OrderBy** szintaxisa megegyezik a **$Filter**. `geo.distance` **$OrderBy** használatakor a mezőnek, amelyre az vonatkozik, típusnak kell lennie, `Edm.GeographyPoint` és azt is tartalmaznia kell `sortable` .

A $orderby szintaxisa a következő `search.score`  : `search.score()` . A függvény `search.score` nem végez paramétereket.

## <a name="examples"></a>Példák

A hotelek növekvő sorrendbe állítása alapdíj alapján:

```odata-filter-expr
    $orderby=BaseRate asc
```

Rendezheti a szállodákat a minősítés alapján csökkenő sorrendben, majd az alaparány szerint növekvő értéket (ne feledje, hogy az emelkedő az alapértelmezett):

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

Rendezheti a szállodákat a minősítés alapján csökkenő sorrendben, majd a megadott koordináták távolsága alapján:

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

A szállodákat csökkenő sorrendbe rendezheti kereséssel. pontszám és értékelés, majd növekvő sorrendben, a megadott koordináták távolsága alapján. A legközelebb álló pontszámok és minősítések két, egymással azonos vonatkozású, a legközelebb található:

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Következő lépések  

- [Keresési eredmények használata az Azure-ban Cognitive Search](search-pagination-page-layout.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)