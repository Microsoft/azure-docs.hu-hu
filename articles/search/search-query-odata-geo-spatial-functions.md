---
title: OData geo-térbeli függvények referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a OData geo-térbeli függvények, a Geo. Distance és a Geo. metszetek használatáról az Azure Cognitive Search lekérdezésekben.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934837"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData geo-térbeli függvények az Azure Cognitive Searchban – `geo.distance` és `geo.intersects`

Az Azure Cognitive Search támogatja a Geo-térbeli lekérdezéseket a [OData-szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) a `geo.distance` és `geo.intersects` függvények használatával. A `geo.distance` függvény két pont közötti távolságot adja vissza, amelyek közül az egyik egy mező-vagy tartomány-változó, és a szűrő részeként egy konstans lett átadva. A `geo.intersects` függvény akkor adja vissza `true` , ha egy adott pont egy adott sokszögen belül van, ahol a pont egy mező-vagy tartomány-változó, és a sokszög a szűrő részeként megadott konstansként van megadva.

A `geo.distance` függvény a [ **$OrderBy** paraméterben](search-query-odata-orderby.md) is használható a keresési eredmények rendezésére egy adott pont távolsága alapján. A `geo.distance` **$OrderBy** szintaxisa megegyezik a **$Filter**. A `geo.distance` **$OrderBy** használatakor a mezőnek, amelyre az alkalmazás vonatkozik, típusnak kell lennie, `Edm.GeographyPoint` és azt is **rendezve** kell lennie.

> [!NOTE]
> A `geo.distance` **$OrderBy** paraméter használatakor a függvénynek átadott mezőnek csak egyetlen földrajzi pontot kell tartalmaznia. Más szóval a típusnak és nem típusúnak kell lennie `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` . Az Azure Cognitive Searchban nem rendezhető a gyűjtemény mezői.

## <a name="syntax"></a>Syntax

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) meghatározza a és a függvények nyelvtanát `geo.distance` , valamint `geo.intersects` azokat a Geo-térbeli értékeket, amelyeken működnek:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

### <a name="geodistance"></a>földrajzi távolság

A `geo.distance` függvény két típusú paramétert fogad `Edm.GeographyPoint` , és olyan értéket ad vissza, `Edm.Double` amely a kettő közötti távolság. Ez különbözik az olyan egyéb szolgáltatásokkal, amelyek támogatják a OData geo-térbeli műveleteit, amelyek jellemzően a mért távolságokat adják vissza a mérőórákban.

Az egyik paraméternek `geo.distance` a földrajzi pont állandójának kell lennie, és a másiknak mező elérési útnak kell lennie (vagy egy tartományban lévő változónak kell lennie, ha a szűrő egy típusú mezőnél megismétli a mezőt `Collection(Edm.GeographyPoint)` ). A paraméterek sorrendje nem számít.

A földrajzi pont állandója az űrlap `geography'POINT(<longitude> <latitude>)'` , ahol a hosszúság és a szélesség numerikus konstans.

> [!NOTE]
> Szűrőben való használat esetén `geo.distance` össze kell hasonlítani a függvény által visszaadott távolságot állandó használatával, `lt` , `le` `gt` vagy `ge` . Az operátorok `eq` és a `ne` távolságok összehasonlítása nem támogatott. Ez például a következő helyes használata `geo.distance` : `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` .

### <a name="geointersects"></a>Geo. metszetek

A `geo.intersects` függvény egy típus és egy állandó változót használ, `Edm.GeographyPoint` `Edm.GeographyPolygon` és egy értéket ad vissza `Edm.Boolean`  --  `true` , ha a pont a sokszög határain belül van, `false` ellenkező esetben.

A sokszög egy kétdimenziós felület, amely egy határoló gyűrűt definiáló pontok sorozata (lásd az alábbi [példákat](#examples) ). A sokszöget le kell zárni, ami azt jelenti, hogy az első és az utolsó pont készletének meg kell egyeznie. [A sokszögben lévő pontoknak nem megfelelő sorrendben kell lenniük](/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-térbeli lekérdezések és sokszögek, amelyek a 180th meridiánt átívelően

Számos, a 180th Meridian-t (a Dátumvonali közelében) tartalmazó lekérdezést alkotó geo-térbeli lekérdezési függvénytár esetében vagy nem korlátozza a korlátot, vagy megkerülő megoldásra van szükség, például a sokszög felosztása két, a meridián egyik oldalán.

Az Azure Cognitive Search-ban a 180 fokos hosszúságú földrajzi térbeli lekérdezések a várt módon fognak működni, ha a lekérdezési alakzat téglalap alakú, és a koordináták a hosszúság és a szélesség (például:) rácsos elrendezésére vannak igazítva `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` . Ellenkező esetben a nem téglalap alakú vagy nem igazított alakzatok esetében vegye figyelembe a felosztott sokszög megközelítését.  

### <a name="geo-spatial-functions-and-null"></a>Geo-térbeli függvények és `null`

Az Azure Cognitive Search összes többi nem gyűjteményéhez hasonlóan a típusú mezők `Edm.GeographyPoint` is tartalmazhatnak `null` értékeket. Amikor az Azure Cognitive Search kiértékel `geo.intersects` egy mezőt `null` , az eredmény mindig a következő lesz: `false` . Ebben az esetben a viselkedése a `geo.distance` kontextustól függ:

- A szűrők `geo.distance` `null` területen egy mező eredményét jeleníti meg `null` . Ez azt jelenti, hogy a dokumentum nem fog megegyezni, mert a `null` nem null értékhez képest kiértékeli a értéket `false` .
- Ha az eredményeket **$OrderBy** használatával rendezi, a `geo.distance` mező a `null` lehető legnagyobb távolságot eredményezi. Az ilyen mezőkkel rendelkező dokumentumok a rendezési irány használatakor alacsonyabbak lesznek, mint az összes többinél `asc` (az alapértelmezett érték), és a többinél nagyobb, mint az irány `desc` .

## <a name="examples"></a>Példák

### <a name="filter-examples"></a>Példák szűrőkre

Az összes Hotel megkeresése egy adott hivatkozási ponttól számított 10 kilométeren belül (ahol a hely egy típusú mező `Edm.GeographyPoint` ):

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

A megadott nézetablakban lévő összes Hotel megkeresése sokszögként (ahol a hely egy típusú mező `Edm.GeographyPoint` ). Vegye figyelembe, hogy a sokszög le van zárva (az első és az utolsó pontnak azonosnak kell lennie), és [a pontoknak](/rest/api/searchservice/supported-data-types#Anchor_1)a következő sorrendben kell szerepelniük.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Rendezési példák

Rendezheti a szállodákat csökkenő sorrendben `rating` , majd a megadott koordináták távolsága alapján:

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Rendezheti a szállodákat csökkenő sorrendben `search.score` , a és a alapján `rating` , majd növekvő sorrendben, a megadott koordináták távolsága alapján, hogy két, azonos minősítéssel rendelkező, egymással azonos minősítéssel rendelkező Hotel között szerepeljenek a legközelebb:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)