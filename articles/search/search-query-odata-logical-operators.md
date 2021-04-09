---
title: OData logikai operátor referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a OData logikai operátorok, valamint, illetve, illetve az Azure Cognitive Search-lekérdezések használata esetén.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917873"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logikai operátorok az Azure-ban Cognitive Search- `and` , `or` , `not`

Az Azure Cognitive Search [OData](query-odata-filter-orderby-syntax.md) olyan logikai kifejezések, amelyek kiértékelése `true` vagy `false` . Az összetett szűrőket úgy írhatja be, hogy az [egyszerűbb szűrők](search-query-odata-comparison-operators.md) sorozatát írja le, és a logikai operátorok segítségével készíti el őket a logikai [algebra](https://en.wikipedia.org/wiki/Boolean_algebra)használatával:

- `and`: Egy bináris operátor, amely kiértékeli, hogy a `true` bal és a jobb oldali alkifejezések is kiértékelésre kerülnek-e `true` .
- `or`: Egy bináris operátor, amely kiértékeli, hogy a `true` bal vagy a jobb oldali alkifejezések valamelyike kiértékeli-e `true` .
- `not`: Egy egyoperandusú operátor, amely kiértékeli, hogy az `true` alkifejezés kiértékelése `false` és fordítva.

Ezek a [gyűjtemény-operátorokkal együtt `any` , `all` és ](search-query-odata-collection-operators.md)lehetővé teszik olyan szűrők összeállítását, amelyek nagyon összetett keresési feltételeket tudnak kifejezni.

## <a name="syntax"></a>Syntax

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a logikai operátorokat használó OData-kifejezések nyelvtanát határozza meg.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A logikai kifejezések két formája létezik: bináris ( `and` / `or` ), ahol két alkifejezés létezik, és az unáris ( `not` ), ahol csak egy szerepel. Az alkifejezések bármely típusú logikai kifejezés lehet:

- Mezők vagy tartomány típusú változók `Edm.Boolean`
- Függvények, amelyek típusú értékeket adnak vissza `Edm.Boolean` , például `geo.intersects` vagy `search.ismatch`
- [Összehasonlító kifejezések](search-query-odata-comparison-operators.md), például `rating gt 4`
- [Gyűjtési kifejezések](search-query-odata-collection-operators.md), például `Rooms/any(room: room/Type eq 'Deluxe Room')`
- A logikai literálok `true` vagy `false` .
- Egyéb, a, és a használatával létrehozott logikai kifejezések `and` `or` `not` .

> [!IMPORTANT]
> Vannak olyan helyzetek, amelyekben nem használhatók az alkifejezések `and` / `or` , különösen lambda kifejezéseken belül. Részletekért lásd: [OData Collection Operators in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logikai operátorok és `null`

A legtöbb logikai kifejezés, például a függvények és az összehasonlítások nem hozhatnak létre `null` értékeket, és a logikai operátorok nem alkalmazhatók `null` közvetlenül a konstansra (például `x and null` nem megengedett). A logikai mezők azonban lehetnek `null` , ezért tisztában kell lennie azzal, hogy a `and` , a `or` és a `not` operátorok hogyan működnek a nullák jelenlétében. Ezt a következő táblázat foglalja össze, ahol a `b` egy típusú mező `Edm.Boolean` :

| Expression | Eredmény `b` , ha `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Ha egy logikai mező `b` önmagában egy szűrési kifejezésben jelenik meg, akkor úgy viselkedik, mintha megírták volna `b eq true` , tehát ha `b` van `null` , a kifejezés kiértékeli a következőt: `false` . Hasonlóképpen, `not b` úgy viselkedik `not (b eq true)` , hogy kiértékeli a következőt: `true` . Így a `null` mezők ugyanúgy viselkednek, mint `false` . Ez összhangban van azzal, ahogyan a és a használatával más kifejezésekkel kombinálva `and` `or` , ahogy az a fenti táblázatban is látható. Ennek ellenére a közvetlen összehasonlítás `false` ( `b eq false` ) továbbra is kiértékeli a következőt: `false` . Más szóval, `null` nem egyenlő a `false` (z) értékkel, még akkor is, ha a logikai kifejezésekben hasonlóan viselkedik.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyekben a `rating` mező 3 és 5 közötti, beleértve a következőket:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Olyan dokumentumok egyeztetése, amelyekben a mező minden eleme `ratings` kisebb, mint 3, vagy több mint 5:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Egyezteti a dokumentumokat `location` , ahol a mező a megadott sokszögen belül van, és a dokumentum nem tartalmazza a "Public" kifejezést.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

A következőhöz tartozó dokumentumok egyeztetése: Vancouver, Kanada, ahol van egy Deluxe szoba, amelynek alapdíja 160-nál kisebb:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)