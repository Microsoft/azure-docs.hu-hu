---
title: OData search.in függvény referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a search.in függvény Azure Cognitive Search lekérdezésekben való használatáról.
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922820"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData `search.in` függvény az Azure Cognitive Search

A [OData szűrési kifejezések](query-odata-filter-orderby-syntax.md) egyik gyakori forgatókönyve, hogy az egyes dokumentumok egyetlen mezője egyenlő-e a lehetséges értékek valamelyikével. Például, hogy egyes alkalmazások hogyan valósítják meg a [biztonsági körülvágást](search-security-trimming-for-azure-search.md) – egy vagy több egyszerű azonosítót tartalmazó mező ellenőrzésével a lekérdezést kiállító felhasználót jelölő egyszerű azonosítók listájára. A (z) és az operátorok használata az alábbi módon írható le [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) :

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

Ezt azonban a függvény használatával rövidebb idő alatt írhatja `search.in` :

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> Amellett, hogy rövidebb és könnyebben olvasható is, a használata `search.in` [teljesítménybeli előnyöket](#bkmk_performance) biztosít, és elkerüli a [szűrők bizonyos méretre vonatkozó korlátozásait](search-query-odata-filter.md#bkmk_limits) , ha több száz vagy akár több ezer érték szerepel a szűrőben. Ezért javasoljuk, hogy `search.in` az egyenlőségi kifejezések összetettebb leválasztása helyett használja a használatát.

> [!NOTE]
> A OData standard 4,01-es verziója nemrég bemutatta a [ `in` kezelőt](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), amely hasonló viselkedést tartalmaz az `search.in` Azure Cognitive Search függvényében. Az Azure Cognitive Search azonban nem támogatja ezt az operátort, ezért Ehelyett a függvényt kell használnia `search.in` .

## <a name="syntax"></a>Syntax

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a függvény nyelvtanát határozza meg `search.in` :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A `search.in` függvény ellenőrzi, hogy egy adott sztring mező vagy tartomány változó egyenlő-e az értékek egy adott listájával. A változó és a listában szereplő értékek közötti egyenlőség a kis-és nagybetűket megkülönböztető módon határozza meg, ugyanúgy, mint a `eq` kezelő esetében. Ezért a hasonló kifejezés `search.in(myfield, 'a, b, c')` `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'` , amely `search.in` sokkal jobb teljesítményt eredményez.

A függvénynek két túlterhelése van `search.in` :

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

A paraméterek a következő táblázatban vannak meghatározva:

| Paraméter neve | Típus | Description |
| --- | --- | --- |
| `variable` | `Edm.String` | Egy karakterlánc-mezőre mutató hivatkozás (vagy egy tartománybeli változó egy karakterlánc-gyűjtési mezőn keresztül abban az esetben, ha az `search.in` egy `any` vagy kifejezésen belül használatos `all` ). |
| `valueList` | `Edm.String` | A paraméternek megfelelő értékek tagolt listáját tartalmazó karakterlánc `variable` . Ha a `delimiters` paraméter nincs megadva, az alapértelmezett határolójelek a szóköz és a vessző. |
| `delimiters` | `Edm.String` | Karakterlánc, amelyben minden karakter elválasztó van kezelve a paraméter elemzésekor `valueList` . Ennek a paraméternek az alapértelmezett értéke, `' ,'` ami azt jelenti, hogy a szóközökkel és/vagy vesszővel elválasztott értékek külön lesznek elkülönítve. Ha a szóközöktől és a vesszőtől eltérő elválasztókat kell használnia, mert az értékek magukban foglalják a karaktereket, alternatív határolójeleket is megadhat, például ebben `'|'` a paraméterben. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Teljesítmény `search.in`

Ha használja `search.in` , akkor várható, hogy a második paraméterben a másodpercben megadott idő több száz vagy ezer értékből álló listát tartalmaz. Az átadható elemek száma nincs kifejezett korláttal `search.in` , de a kérések maximális mérete továbbra is korlátozott. Azonban a késés növekedni fog, ahogy az értékek száma növekszik.

## <a name="examples"></a>Példák

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel. A kifejezések szóközöket tartalmaznak, amelyek alapértelmezett határolójelek. Az idézőjelek között egy alternatív határolójelet is megadhat a harmadik sztring paraméterként:  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel, a következővel elválasztva: "|".

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

A "WiFi" vagy a "kád" címkével rendelkező összes Hotel megkeresése:

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

Megtalálhatja a gyűjteményen belüli kifejezéseket, például a "fűtött törölköző állványok" vagy a "hajszárító" címkét.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

A "Motel" vagy a "cabin'" címke nélküli összes Hotel megkeresése:

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)