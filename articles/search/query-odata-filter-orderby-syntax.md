---
title: A OData nyelvének áttekintése
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search-lekérdezések szűrésére, kiválasztására és rendezésére szolgáló OData nyelvének áttekintése.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923398"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Az `$filter` `$orderby` `$select` Azure Cognitive Search OData nyelvének áttekintése

Az Azure Cognitive Search a OData Expression szintaxisának egy részhalmazát támogatja **$Filter**, **$OrderBy** és **$Select** kifejezésekhez. A szűrési kifejezéseket a rendszer kiértékeli a lekérdezések elemzése során, megtiltja a keresést adott mezőkre, vagy az index vizsgálata során használt egyezési feltételeket ad hozzá. A sorrend szerinti kifejezéseket a rendszer utólagos feldolgozás utáni lépésként alkalmazza a visszaadott dokumentumok rendezéséhez. Válassza ki azokat a kifejezéseket, amelyek meghatározzák, hogy mely dokumentum mezők szerepeljenek az eredményhalmazban. Ezeknek a kifejezéseknek a szintaxisa különbözik a **keresési** paraméterben használt [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezési szintaxistól, bár átfedésben van a mezők hivatkozó szintaxisa.

Ez a cikk áttekintést nyújt a Filters, Order by és Select kifejezésekben használt OData kifejezés nyelvéről. A nyelv "bottom-up" (alapszintű), a legalapvetőbb elemektől kezdve, és azokra épül. Az egyes paraméterek legfelső szintű szintaxisát külön cikk ismerteti:

- [$filter szintaxis](search-query-odata-filter.md)
- [$orderby szintaxis](search-query-odata-orderby.md)
- [$select szintaxis](search-query-odata-select.md)

A OData-kifejezések egyszerűen és nagyon összetettek, de minden közös elemet megosztanak. Az Azure Cognitive Search OData-kifejezésének legalapvetőbb részei a következők:

- A **mező elérési útja**, amely az index adott mezőire hivatkozik.
- **Konstansok**, amelyek egy bizonyos adattípushoz tartozó literál értékeket tartalmaznak.

> [!NOTE]
> Az Azure Cognitive Search terminológiája néhány módon különbözik a [OData standardtól](https://www.odata.org/documentation/) . Az Azure Cognitive Search egyik **mezőjét** nevezzük a OData **tulajdonságának** , és hasonlóképpen a **mező elérési** útja és a **tulajdonság elérési útja** között. Az Azure Cognitive Searchban található **dokumentumokat** tartalmazó **indexet** a OData általánosabban az **entitásokat** tartalmazó **entitásként** említik. Ebben a hivatkozásban az Azure Cognitive Search-terminológiát használjuk.

## <a name="field-paths"></a>Mező elérési útjai

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a mezők elérési útjának nyelvtanát határozza meg.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A mező elérési útja egy vagy több, ferde vonallal elválasztott **azonosítóból** áll. Minden azonosító egy olyan karaktersorozat, amelynek egy ASCII betűvel vagy aláhúzással kell kezdődnie, és csak ASCII betűt, számjegyet vagy aláhúzást tartalmazhat. A betűk lehetnek a felső vagy a kisbetűs karakterek.

Az azonosítók egy adott mező nevére vagy egy, a szűrőben lévő [gyűjtemény kifejezés](search-query-odata-collection-operators.md) (vagy) kontextusában lévő **Range változóra** is hivatkozhatnak `any` `all` . A tartomány változó a gyűjtemény aktuális elemét jelképező hurok-változóhoz hasonlít. Összetett gyűjtemények esetén ez a változó egy objektumot jelöl, ezért a mező elérési útjaival a változó almezőire hivatkozhat. Ez hasonló a dot-jelölésekhez számos programozási nyelven.

A következő táblázat példákat mutat be a mezők elérési útjaira:

| Mező elérési útja | Description |
| --- | --- |
| `HotelName` | Az index legfelső szintű mezőjére hivatkozik. |
| `Address/City` | Az `City` index összetett mezőjének almezőjét jelöli. `Address` ebben a példában a típus `Edm.ComplexType` |
| `Rooms/Type` | Az `Type` indexben található összetett gyűjtemény mező almezőjét jelöli. ebben a `Rooms` példában a következő típusú `Collection(Edm.ComplexType)` : |
| `Stores/Address/Country` | Az `Country` `Address` indexben található összetett gyűjtemény mezőjének almezőjét jelöli, `Stores` `Collection(Edm.ComplexType)` és a `Address` `Edm.ComplexType` példában szereplő típusú. |
| `room/Type` | A `Type` tartomány változójának almezőjére hivatkozik `room` , például a Filter kifejezésben. `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | A `Country` `Address` tartomány változó almezőjét jelöli `store` , például a szűrő kifejezésben. `Stores/any(store: store/Address/Country eq 'Canada')` |

A mező elérési útjának jelentése a környezettől függően eltérő. A szűrők területen a mező elérési útja az aktuális dokumentumban lévő mező egy *példányának* értékére hivatkozik. Más környezetekben, például **$OrderBy**, **$Select** vagy a [teljes Lucene szintaxisban található, mezőn belüli keresésben](query-lucene-syntax.md#bkmk_fields)a mező elérési útja magára a mezőre hivatkozik. Ez a különbség bizonyos következményekkel jár, hogy miként használhatók a mezők elérési útjai a szűrőkben.

Vegye figyelembe a mező elérési útját `Address/City` . Egy szűrőben ez az aktuális dokumentum egyetlen városára vonatkozik, például: "San Francisco". Ezzel szemben `Rooms/Type` a `Type` sok szoba (például a "standard", az első szoba, a "Deluxe" a második Teremnél stb.) almezőjét jelenti. Mivel `Rooms/Type` a nem az almező *egyetlen példányára* hivatkozik `Type` , nem használható közvetlenül szűrőben. Ehelyett a szobatípus szűréséhez egy tartomány-változót használó [lambda kifejezést](search-query-odata-collection-operators.md) kellene használni, például:

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

Ebben a példában a tartomány változó `room` megjelenik a `room/Type` mező elérési útján. Így az aktuális `room/Type` dokumentumban található aktuális helyiség típusára hivatkozik. Ez az almező egyetlen példánya `Type` , így közvetlenül a szűrőben is használható.

### <a name="using-field-paths"></a>Mezők elérési útjának használata

A mező elérési útjait az [Azure Cognitive Search REST API](/rest/api/searchservice/)-k számos paramétere használja. A következő táblázat felsorolja az összes felhasználható helyet, valamint a használattal kapcsolatos korlátozásokat is:

| API | Paraméter neve | Korlátozások |
| --- | --- | --- |
| Index [létrehozása](/rest/api/searchservice/create-index) vagy [frissítése](/rest/api/searchservice/update-index) | `suggesters/sourceFields` | Nincsenek |
| Index [létrehozása](/rest/api/searchservice/create-index) vagy [frissítése](/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Csak **kereshető** mezőkre hivatkozhat |
| Index [létrehozása](/rest/api/searchservice/create-index) vagy [frissítése](/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Csak **szűrhető** mezőkre hivatkozhat |
| [Search](/rest/api/searchservice/search-documents) | `search` Ha `queryType` a `full` | Csak **kereshető** mezőkre hivatkozhat |
| [Search](/rest/api/searchservice/search-documents) | `facet` | Csak a **sokrétű** mezőkre hivatkozhat |
| [Search](/rest/api/searchservice/search-documents) | `highlight` | Csak **kereshető** mezőkre hivatkozhat |
| [Search](/rest/api/searchservice/search-documents) | `searchFields` | Csak **kereshető** mezőkre hivatkozhat |
| [Javaslat](/rest/api/searchservice/suggestions) és [automatikus kiegészítés](/rest/api/searchservice/autocomplete) | `searchFields` | Csak a [javaslat](index-add-suggesters.md) részét képező mezőkre hivatkozhat. |
| [Keresés](/rest/api/searchservice/search-documents), [javaslat](/rest/api/searchservice/suggestions)és [automatikus kiegészítés](/rest/api/searchservice/autocomplete) | `$filter` | Csak **szűrhető** mezőkre hivatkozhat |
| [Keresés](/rest/api/searchservice/search-documents) és [javaslat](/rest/api/searchservice/suggestions) | `$orderby` | Csak **rendezhető** mezőkre hivatkozhat |
| [Keresés](/rest/api/searchservice/search-documents), [javaslat](/rest/api/searchservice/suggestions)és [Keresés](/rest/api/searchservice/lookup-document) | `$select` | Csak lekérhető **mezőkre** hivatkozhat |

## <a name="constants"></a>Állandók

A OData konstansai egy adott [entitás adatmodell](/dotnet/framework/data/adonet/entity-data-model) -(EDM-) típusának literális értékei. Az Azure Cognitive Search támogatott típusai listáját a [támogatott adattípusok](/rest/api/searchservice/supported-data-types) részben tekintheti meg. A gyűjteményi típusok állandói nem támogatottak.

Az alábbi táblázat az Azure Cognitive Search által támogatott adattípusok állandóit mutatja be:

| Adattípus | Példa állandók |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Speciális karakterek Escape-karakterlánc-konstansokban

A OData lévő karakterlánc-konstansokat szimpla idézőjelek határozzák meg. Ha olyan karakterlánc-állandó lekérdezést kell létrehoznia, amely magába foglalja az idézőjeleket, akkor a beágyazott idézőjelek megkettőzésével megduplázhatja őket.

Például egy formázatlan aposztróftal (például "Alice 's Car") jelölt kifejezés a OData karakterlánc-konstansként jelenik meg `'Alice''s car'` .

> [!IMPORTANT]
> Ha programozott módon alakítja ki a szűrőket, fontos megjegyezni, hogy a felhasználói bevitelből származó karakterlánc-konstansok megmaradnak. Ez csökkenti az [injekciós támadások](https://wikipedia.org/wiki/SQL_injection)lehetőségét, különösen akkor, ha szűrőket használ a [biztonsági vágás](search-security-trimming-for-azure-search.md)megvalósításához.

### <a name="constants-syntax"></a>Konstansok szintaxisa

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a fenti táblázatban látható állandók többségének nyelvtanát határozza meg. A Geo-térbeli típusok nyelvtana az [Azure Cognitive Search OData geo-térbeli függvények területén](search-query-odata-geo-spatial-functions.md)érhető el.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Kifejezések kiépítése a mezők elérési útjaiból és konstansokból

A mezők elérési útjai és állandói a OData egyik legalapvetőbb részét képezik, de már maguk a teljes kifejezések. Valójában a **$Select** paraméter az Azure Cognitive Searchban nem más, mint a mezők elérési útjai vesszővel tagolt listája, és a **$OrderBy** nem sokkal bonyolultabb, mint a **$Select**. Ha úgy látja, hogy van egy típusú mező az `Edm.Boolean` indexben, akkor is írhat egy olyan szűrőt, amely nem más, mint a mező elérési útja. Az állandók `true` és a `false` szűrők is érvényesek.

Azonban a legtöbb esetben összetettebb kifejezésekre lesz szüksége, amelyek egynél több mezőre és állandóra hivatkoznak. Ezek a kifejezések a paramétertől függően különböző módokon vannak felépítve.

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a **$filter**, a **$OrderBy** és a **$Select** paraméterek nyelvtanát határozza meg. Ezek az egyszerűbb kifejezésekből épülnek fel, amelyek a mező elérési útjaira és állandóra vonatkoznak:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A **$OrderBy** és a **$Select** paraméterek az egyszerűbb kifejezések vesszővel tagolt listája. A **$Filter** paraméter egy egyszerű alkifejezésből álló logikai kifejezés. Ezek az alkifejezések olyan logikai operátorokkal vannak kombinálva, mint a, a, [ `and` `or` `not` és ](search-query-odata-logical-operators.md)az összehasonlító operátorok, például,, stb., és a gyűjtemény operátorai, például a [ `any` és `all` ](search-query-odata-collection-operators.md) [ `eq` `lt` `gt` a](search-query-odata-comparison-operators.md).

A **$Filter**, **$OrderBy** és **$Select** paramétereket részletesebben ismertetjük a következő cikkekben:

- [OData $filter szintaxis az Azure-ban Cognitive Search](search-query-odata-filter.md)
- [OData $orderby szintaxis az Azure-ban Cognitive Search](search-query-odata-orderby.md)
- [OData $select szintaxis az Azure-ban Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Lásd még  

- [Sokoldalú navigálás az Azure-ban Cognitive Search](search-faceted-navigation.md)
- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search](query-simple-syntax.md)