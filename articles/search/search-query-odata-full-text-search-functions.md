---
title: OData teljes szöveges keresési funkciójának referenciája
titleSuffix: Azure Cognitive Search
description: OData teljes szöveges keresési függvények, Search. ismatch és Search. ismatchscoring, az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935789"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData teljes szöveges keresési függvények az Azure Cognitive Searchban – `search.ismatch` és `search.ismatchscoring`

Az Azure Cognitive Search támogatja a teljes szöveges keresést a OData- [szűrési kifejezések](query-odata-filter-orderby-syntax.md) `search.ismatch` és függvények használatával `search.ismatchscoring` . Ezek a függvények lehetővé teszik, hogy a teljes szöveges keresést szigorú logikai szűréssel kombinálja olyan módokon, amelyek nem csak a `search` [Search API](/rest/api/searchservice/search-documents)legfelső szintű paraméterével használhatók.

> [!NOTE]
> A `search.ismatch` és a `search.ismatchscoring` függvények csak a [Search API](/rest/api/searchservice/search-documents)-ban lévő szűrőkben támogatottak. Az [ajánlott](/rest/api/searchservice/suggestions) és az [automatikus kiegészítési](/rest/api/searchservice/autocomplete) API-k nem támogatják őket.

## <a name="syntax"></a>Syntax

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a és függvények nyelvtanát határozza meg `search.ismatch` `search.ismatchscoring` :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

### <a name="searchismatch"></a>Keresés. ismatch

A `search.ismatch` függvény egy teljes szöveges keresési lekérdezést értékel ki egy szűrési kifejezés részeként. A keresési lekérdezésnek megfelelő dokumentumokat a rendszer az eredményhalmazban adja vissza. A függvény következő túlterhelései érhetők el:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

A paraméterek a következő táblázatban vannak meghatározva:

| Paraméter neve | Típus | Description |
| --- | --- | --- |
| `search` | `Edm.String` | A keresési lekérdezés ( [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) Lucene lekérdezési szintaxis). |
| `searchFields` | `Edm.String` | A kereshető mezők vesszővel tagolt listája a kereséshez; az alapértelmezett érték az index összes kereshető mezőjénél. Ha a paraméterben a [mezőn belüli keresés](query-lucene-syntax.md#bkmk_fields) szerepel `search` , a Lucene lekérdezés mezőiben szereplő mezők felülbírálják az ebben a paraméterben megadott mezőket. |
| `queryType` | `Edm.String` | `'simple'` vagy `'full'` ; alapértelmezett érték: `'simple'` . Megadja, hogy melyik lekérdezési nyelvet használták a `search` paraméter. |
| `searchMode` | `Edm.String` | `'any'` vagy `'all'` alapértelmezett értéke: `'any'` . Azt jelzi, hogy a paraméterben szereplő keresési kifejezések bármelyike vagy mindegyike egyezik-e a `search` dokumentum egyezésként való megszámlálásához. Az [Lucene logikai operátorok](query-lucene-syntax.md#bkmk_boolean) használatakor a `search` paraméter elsőbbséget élvez a paraméterrel szemben. |

A fenti paraméterek a [keresési API megfelelő keresési kérelmi paramétereinek](/rest/api/searchservice/search-documents)felelnek meg.

A `search.ismatch` függvény egy típusú értéket ad vissza `Edm.Boolean` , amely lehetővé teszi, hogy más szűrő alkifejezésekkel kombinálja a logikai [logikai operátorok](search-query-odata-logical-operators.md)használatával.

> [!NOTE]
> Az Azure Cognitive Search nem támogatja a `search.ismatch` lambda kifejezések használatát és a belső használatot `search.ismatchscoring` . Ez azt jelenti, hogy nem lehet szűrőket írni olyan objektumok gyűjteményei között, amelyek összekapcsolhatók a teljes szöveges keresési egyezésekkel az azonos objektumon lévő szigorú szűrési egyezésekkel. A korlátozással és a példákkal kapcsolatos további információkért lásd: [gyűjtési szűrők hibaelhárítása Az Azure Cognitive Searchban](search-query-troubleshoot-collection-filters.md). A korlátozás létezésével kapcsolatos további információkért lásd: a [gyűjtemény szűrőinek ismertetése az Azure Cognitive Searchban](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Keresés. ismatchscoring

A függvény `search.ismatchscoring` , például a `search.ismatch` függvény, visszaadja `true` azokat a dokumentumokat, amelyek megfelelnek a paraméterként átadott teljes szöveges keresési lekérdezésnek. Ezek közötti különbség az, hogy a lekérdezésnek megfelelő dokumentumok relevanciás pontszáma `search.ismatchscoring` hozzájárul a teljes dokumentum pontszámához, míg a esetében `search.ismatch` a dokumentum pontszáma nem változik. A függvény alábbi túlterhelései a következőhöz hasonló paraméterekkel érhetők el `search.ismatch` :

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

A `search.ismatch` és a `search.ismatchscoring` függvények is ugyanabban a szűrő kifejezésben használhatók.

## <a name="examples"></a>Példák

Keresse meg a "Waterfront" szót tartalmazó dokumentumokat. Ez a szűrési lekérdezés megegyezik egy [keresési kérelemmel](/rest/api/searchservice/search-documents) a következővel: `search=waterfront` .

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Keresse meg a "Hostel" szót tartalmazó dokumentumokat, vagy a 4-es vagy annál nagyobb minősítést, vagy a "Motel" szót tartalmazó dokumentumokat, valamint az 5 értékű minősítést. Megjegyzés: Ez a kérés nem fejezhető be a `search.ismatchscoring` függvény nélkül.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Dokumentumok keresése a "luxus" szó nélkül.

```odata-filter-expr
    not search.ismatch('luxury')
```

Megkeresheti az "Ocean View" kifejezéssel vagy 5 értékkel egyenlő minősítéssel rendelkező dokumentumokat. A `search.ismatchscoring` lekérdezés csak a mezőkre és a-ra lesz végrehajtva `HotelName` `Rooms/Description` .

Azok a dokumentumok, amelyek megfelelnek a kivonásnak, csak a második záradékkal lesznek visszaadva `Rating` . Annak egyértelművé tétele érdekében, hogy ezek a dokumentumok nem egyeznek a kifejezés egyik pontján sem, a pontszám értéke nulla lesz.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Megkeresheti azokat a dokumentumokat, amelyekben a "Hotel" és a "repülőtér" kifejezés a szálloda leírásában szereplő 5 szóból áll, és ahol a dohányzás nem engedélyezett legalább néhány szobában. Ez a lekérdezés a [teljes Lucene lekérdezési nyelvet](query-lucene-syntax.md)használja.

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)