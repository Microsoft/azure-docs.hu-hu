---
title: OData-összehasonlító operátor referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a OData összehasonlító operátorok (EQ, ne, gt, lt, GE és le) használatáról az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934871"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData összehasonlító operátorok az Azure-ban Cognitive Search- `eq` ,,,, `ne` `gt` `lt` `ge` és `le`

Az Azure Cognitive Search [OData-szűrési kifejezésének](query-odata-filter-orderby-syntax.md) legalapvetőbb művelete egy mező egy adott értékkel való összevetése. Az összehasonlítás két típusa lehetséges – egyenlőség-összehasonlítás és tartomány-összehasonlítás. A következő operátorok segítségével hasonlíthatja össze a mezőket egy állandó értékkel:

Esélyegyenlőségi operátorok:

- `eq`: Annak tesztelése, hogy egy mező **egyenlő-** e egy konstans értékkel
- `ne`: Annak tesztelése, hogy egy mező **nem egyenlő-** e állandó értékkel

Tartomány operátorai:

- `gt`: Annak tesztelése, hogy egy mező nagyobb-e, **mint** egy konstans érték
- `lt`: Annak tesztelése, hogy egy mező kisebb-e, **mint** a konstans érték
- `ge`: Annak tesztelése, hogy egy mező **nagyobb-e vagy egyenlő-** e egy konstans értékkel
- `le`: Annak tesztelése, hogy egy mező értéke **kisebb vagy egyenlő-** e egy konstans értékkel

A tartomány-operátorokat a [logikai operátorokkal](search-query-odata-logical-operators.md) együtt használva ellenőrizheti, hogy egy adott mező bizonyos tartományon belül van-e. Tekintse meg a jelen cikk későbbi részében található [példákat](#examples) .

> [!NOTE]
> Ha szeretné, az állandó értéket az operátor bal oldalán, a mező nevét pedig a jobb oldalon helyezheti el. A tartomány-operátorok esetében az összehasonlítás jelentése fordított. Ha például az állandó érték a bal oldalon van, `gt` akkor teszteli, hogy az állandó érték nagyobb-e, mint a mező. Az összehasonlító operátorok segítségével összehasonlíthatja a függvények eredményét, például `geo.distance` egy értéket is. Olyan logikai függvények esetében, mint például `search.ismatch` a, az eredmény vagy a választható érték összevetése `true` `false` .

## <a name="syntax"></a>Syntax

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiálja az összehasonlító operátorokat használó OData-kifejezések nyelvtanát.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

Az összehasonlító kifejezések két formája létezik. Az egyetlen különbség, hogy az állandó megjelenik-e az operátor bal vagy jobb oldali részén. Az operátor másik oldalán lévő kifejezésnek **változónak** vagy függvény hívásnak kell lennie. A változó lehet egy mezőnév vagy egy tartomány változó egy [lambda kifejezés](search-query-odata-collection-operators.md)esetén.

## <a name="data-types-for-comparisons"></a>Összehasonlítások adattípusai

Az összehasonlító operátorok mindkét oldalán lévő adattípusoknak kompatibilisnek kell lenniük. Ha például a bal oldali mező típusa `Edm.DateTimeOffset` , akkor a jobb oldalon dátum-idő állandónak kell lennie. A numerikus adattípusok rugalmasabbak. Bármilyen numerikus típus változóit és függvényeit összehasonlíthatja bármilyen más numerikus típussal, néhány korlátozással, az alábbi táblázatban leírtak szerint.

| Változó vagy függvény típusa | Konstans értéktípus | Korlátozások |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Az összehasonlításra a [következő speciális szabályok `NaN` vonatkoznak:](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | A konstans a értékre lett konvertálva `Edm.Double` , ami a nagy mennyiségű értékek pontosságának elvesztését eredményezi |
| `Edm.Double` | `Edm.Int32` | n.a. |
| `Edm.Int64` | `Edm.Double` | A `NaN` , `-INF` , vagy `INF` nem engedélyezett összehasonlítások |
| `Edm.Int64` | `Edm.Int64` | n.a. |
| `Edm.Int64` | `Edm.Int32` | A konstans konvertálása az `Edm.Int64` összehasonlítás előtt történik |
| `Edm.Int32` | `Edm.Double` | A `NaN` , `-INF` , vagy `INF` nem engedélyezett összehasonlítások |
| `Edm.Int32` | `Edm.Int64` | n.a. |
| `Edm.Int32` | `Edm.Int32` | n.a. |

A nem engedélyezett összehasonlításokhoz, például a típusú mezők összehasonlításához `Edm.Int64` `NaN` az Azure Cognitive Search REST API "http 400: hibás kérés" hibaüzenetet ad vissza.

> [!IMPORTANT]
> Bár a numerikus típusok összehasonlítása rugalmas, javasoljuk, hogy az összehasonlításokat a szűrőkben is érdemes megírni, hogy az állandó érték ugyanolyan adattípusú legyen, mint a változó vagy a függvény, amelyhez a rendszer hasonlít. Ez különösen akkor fontos, ha a lebegőpontos és az egész értékeket keverik, ahol a pontosságot elveszítő implicit konverziók lehetségesek.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>A és a speciális esetei `null``NaN`

Az összehasonlító operátorok használatakor fontos megjegyezni, hogy az Azure Cognitive Search összes nem gyűjteményes mezője potenciálisan lehet `null` . A következő táblázat az összehasonlítási kifejezés összes lehetséges eredményét tartalmazza, ahol mindkét oldal lehet `null` :

| Operátor | Eredmény, ha csak a mező vagy a változó szerepel `null` | Eredmény, ha csak az állandó érték van `null` | Eredmény, ha a mező vagy a változó és az állandó is `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `lt` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `ge` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `le` | `false` | HTTP 400: hibás kérelem hiba | HTTP 400: hibás kérelem hiba |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Összefoglalva, `null` csak önmagával egyenlő, és nem lehet kisebb vagy nagyobb, mint bármely más érték.

Ha az indexnek vannak típusú mezői `Edm.Double` , és értékeket tölt fel `NaN` ezekre a mezőkre, akkor a szűrők írásakor el kell végeznie a fiók használatát. Az Azure Cognitive Search az értékek kezelésére az IEEE 754 szabványt valósítja meg `NaN` , az ilyen értékekkel való összehasonlítás pedig nem nyilvánvaló eredményeket eredményez, ahogy az alábbi táblázatban is látható.

| Operátor | Eredmény, ha legalább egy operandus `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Összefoglalva, `NaN` nem egyenlő egyetlen értékkel sem, beleértve a saját magát is.

### <a name="comparing-geo-spatial-data"></a>Geo-térbeli adatainak összehasonlítása

Egy típusú mező nem hasonlítható össze `Edm.GeographyPoint` állandó értékkel, de használhatja a `geo.distance` függvényt is. Ez a függvény egy típusú értéket ad vissza `Edm.Double` , így összehasonlíthatja azt egy numerikus konstanssal, hogy az állandó geo-térbeli koordináták távolsága alapján szűrje azt. Tekintse meg az alábbi [példákat](#examples) .

### <a name="comparing-string-data"></a>Karakterlánc-adatértékek összehasonlítása

A karakterláncok összehasonlítható a szűrőkben a `eq` és a `ne` operátorok pontos egyezéséhez. Ezek az összehasonlítások megkülönböztetik a kis-és nagybetűket.

## <a name="examples"></a>Példák

Olyan dokumentumok egyeztetése, amelyekben a `Rating` mező 3 és 5 közötti, beleértve a következőket:

```text
Rating ge 3 and Rating le 5
```

Olyan dokumentumok egyeztetése, amelyekben a `Location` mező kevesebb, mint 2 kilométer a megadott szélességi és hosszúsági fok:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Olyan dokumentumok egyeztetése, amelyekben a `LastRenovationDate` mező értéke nagyobb vagy egyenlő, mint 2015, éjfélkor UTC:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Olyan dokumentumok egyeztetése `Details/Sku` , amelyek nem a mező `null` :

```text
Details/Sku ne null
```

A dokumentumok egyeztetése olyan hoteleknél, amelyekben legalább egy szoba "Deluxe szoba" típusú, ahol a `Rooms/Type` mező sztringje pontosan megfelel a szűrőnek:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)