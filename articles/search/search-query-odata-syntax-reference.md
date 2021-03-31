---
title: OData-kifejezés szintaxisának referenciája
titleSuffix: Azure Cognitive Search
description: Formális nyelvtani és szintaxis-specifikáció az Azure Cognitive Search lekérdezésekben található OData-kifejezésekhez.
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
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88928853"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája

Az Azure Cognitive Search [OData-kifejezéseket](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) használ paraméterekként az API-ban. A és a paraméterek leggyakrabban a OData kifejezéseket `$orderby` használják `$filter` . Ezek a kifejezések bonyolultak lehetnek, amelyek több záradékot, funkciót és operátort tartalmaznak. Az Azure Cognitive Search REST API számos részében azonban még az egyszerű OData kifejezések is használatosak, például a tulajdonságok elérési útjai. Az elérésiút-kifejezések például az összetett mezők almezőire hivatkoznak az API-ban mindenhol, például ha az almezőket egy [javaslatban](index-add-suggesters.md), egy [pontozási függvényben](index-add-scoring-profiles.md), a `$select` paraméterben, vagy akár a [Lucene-lekérdezésekben található mezőkben keresik](query-lucene-syntax.md).

Ez a cikk a OData kifejezések ezen formáit mutatja be formális nyelvtan használatával. Létezik egy [interaktív diagram](#syntax-diagram) is, amely a nyelvtan vizuális megismerését segíti.

## <a name="formal-grammar"></a>Formális nyelvtan

Leírjuk az Azure Cognitive Search által támogatott OData-nyelv részhalmazát egy EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) nyelvtan használatával. A szabályok "felülről lefelé" vannak felsorolva, kezdve a legbonyolultabb kifejezésekkel, és az összetettebb kifejezéseket lebontva. A tetején az Azure Cognitive Search REST API adott paramétereinek megfelelő nyelvtani szabályok szerepelnek:

- [`$filter`](search-query-odata-filter.md), amelyet a szabály határoz meg `filter_expression` .
- [`$orderby`](search-query-odata-orderby.md), amelyet a szabály határoz meg `order_by_expression` .
- [`$select`](search-query-odata-select.md), amelyet a szabály határoz meg `select_expression` .
- A szabály által definiált mezők elérési útjai `field_path` . A mező elérési útjai a teljes API-ban használatosak. Az indexek legfelső szintű mezőire, illetve egy vagy több [összetett mező](search-howto-complex-data-types.md) őseire hivatkozó almezőket is hivatkozhatnak.

Miután a EBNF egy böngészhető [szintaxisú diagram](https://en.wikipedia.org/wiki/Syntax_diagram) , amely lehetővé teszi, hogy interaktív módon vizsgálja meg a nyelvtant és a szabályok közötti kapcsolatokat.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Szintaxis diagram

Az Azure Cognitive Search által támogatott nyelvi OData vizuális megismeréséhez próbálja ki az interaktív szintaxis diagramot:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Lásd még  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
- [Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search](query-simple-syntax.md)