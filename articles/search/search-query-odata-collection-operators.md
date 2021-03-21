---
title: OData-gyűjtemény kezelői referenciája
titleSuffix: Azure Cognitive Search
description: Ha szűrési kifejezéseket hoz létre az Azure Cognitive Search lekérdezésekben, használja a lambda kifejezések "any" és "összes" operátorát, ha a szűrő gyűjtemény vagy összetett gyűjtemény mezőn található.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934905"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData-gyűjtemény operátorok az Azure Cognitive Searchban – `any` és `all`

Ha egy [OData-szűrési kifejezést](query-odata-filter-orderby-syntax.md) ír az Azure Cognitive Search használatával, gyakran hasznos a begyűjtési mezők szűrése. Ezt a és a operátorok használatával érheti el `any` `all` .

## <a name="syntax"></a>Syntax

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a vagy a-t használó OData kifejezés nyelvtanát határozza meg `any` `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A gyűjtemények három formája létezik.

- Az első két iteráció egy gyűjtemény mező fölé, amely egy lambda kifejezés formájában megadott predikátumot alkalmaz a gyűjtemény minden elemére.
  - A `all` visszatérést használó kifejezés `true` , ha a predikátum a gyűjtemény minden eleme esetében igaz.
  - A visszatérést használó kifejezés, `any` `true` Ha a predikátum a gyűjtemény legalább egy eleménél igaz.
- A gyűjtési szűrő harmadik formája `any` lambda kifejezés nélkül működik annak teszteléséhez, hogy egy gyűjtemény mező üres-e. Ha a gyűjteménynek bármilyen eleme van, a függvény visszaadja `true` . Ha a gyűjtemény üres, a függvény visszaadja `false` .

A gyűjtemény szűrőben lévő **lambda kifejezés** olyan, mint egy programozási nyelvben lévő hurok törzse. Definiál egy változót, amelynek neve a **Range változó**, amely a gyűjtemény aktuális elemét tárolja az iteráció során. Egy másik logikai kifejezést is definiál, amely a gyűjtemény egyes elemeinél a tartomány változóra alkalmazandó szűrési feltétel.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyek `tags` mezője pontosan a "WiFi" karakterláncot tartalmazza:

```text
tags/any(t: t eq 'wifi')
```

Olyan dokumentumok egyeztetése, amelyekben a mező minden eleme `ratings` 3 és 5 közé esik, beleértve a következőket:

```text
ratings/all(r: r ge 3 and r le 5)
```

Olyan dokumentumok egyeztetése, amelyekben a mező egyik földrajzi koordinátái `locations` a megadott sokszögen belül vannak:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Olyan dokumentumok egyeztetése, amelyekben a `rooms` mező üres:

```text
not rooms/any()
```

A dokumentumok egyeztetése az összes szobában, a `rooms/amenities` mező a "TV" kifejezést tartalmazza, és `rooms/baseRate` kevesebb, mint 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Korlátozások

A szűrési kifejezések nem minden funkciója érhető el egy lambda kifejezés törzsében. A korlátozások a szűrni kívánt gyűjtési mező adattípusától függően eltérőek. Az alábbi táblázat a korlátozásokat foglalja össze.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

További információ ezekről a korlátozásokról és példákról: [gyűjtési szűrők hibaelhárítása Az Azure Cognitive Searchban](search-query-troubleshoot-collection-filters.md). További részletes információk a korlátozások létezéséről: a [gyűjtemény szűrőinek ismertetése az Azure Cognitive Searchban](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)