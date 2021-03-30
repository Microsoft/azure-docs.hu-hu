---
title: A OData-gyűjtési szűrők ismertetése
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan működnek a OData-gyűjtési szűrők az Azure Cognitive Search lekérdezésekben, beleértve a gyűjtemények egyedi korlátozásait és viselkedését.
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932916"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>A OData-gyűjtési szűrők ismertetése az Azure Cognitive Search

Az Azure Cognitive Search gyűjtemény mezőinek [szűréséhez](query-odata-filter-orderby-syntax.md) a [ `any` és a `all` operátort](search-query-odata-collection-operators.md) **lambda kifejezésekkel** együtt használhatja. A lambda kifejezés olyan logikai kifejezés, amely egy **tartomány változóra** hivatkozik. A `any` és a `all` kezelők a `for` legtöbb programozási nyelvben a hurokhoz hasonlóak, és a tartomány változó a Loop változó szerepét veszi át, a lambda kifejezést pedig a hurok törzsének. A Range változó a művelet "aktuális" értékét veszi át a hurok ismétlése során.

Ezek közül legalább a fogalmi módon működik. A valóságban az Azure Cognitive Search nagyon eltérő módon valósítja meg a szűrőket a `for` hurkok működéséhez. Ideális esetben ez a különbség láthatatlan lesz, de bizonyos helyzetekben nem. Ennek végeredménye az, hogy a lambda kifejezések írásakor követni kell a szükséges szabályokat.

Ez a cikk azt ismerteti, hogy az Azure Cognitive Search hogyan hajtja végre ezeket a szűrőket a gyűjtési szűrők szabályainak megismeréséhez. Ha összetett lambda kifejezésekkel rendelkező speciális szűrőket ír, akkor ez a cikk hasznos lehet a szűrőkben rejlő lehetőségek megismerésének kialakításában és a miért.

További információ a gyűjtési szűrők szabályairól, beleértve a példákat is: [OData-gyűjtési szűrők hibaelhárítása Az Azure Cognitive Searchban](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>A gyűjtési szűrők korlátozása

Három alapvető oka van annak, hogy az összes szűrési funkció nem támogatott az összes típusú gyűjtemény esetében:

1. Bizonyos adattípusok esetében csak bizonyos operátorok támogatottak. Így például nem érdemes összehasonlítani a logikai értékeket, és a, `true` `false` `lt` `gt` , és így tovább.
1. Az Azure Cognitive Search nem támogatja a (z) típusú mezők **korrelált keresését** `Collection(Edm.ComplexType)` .
1. Az Azure Cognitive Search invertált indexeket használ a szűrők végrehajtásához az összes típusú adathoz, beleértve a gyűjteményeket is.

Az első ok a OData nyelv és a EDM típusának meghatározása. Az utolsó kettőt részletesebben ismertetjük a cikk további részében.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelált és nem korrelált keresés

Ha összetett objektumok gyűjteményéhez több szűrési feltételt alkalmaz, a feltételek **korrelálnak** , mivel azok *a gyűjtemény egyes objektumaira* vonatkoznak. A következő szűrő például olyan szállodákat ad vissza, amelyek legalább egy deluxe szobával rendelkeznek, 100-nál kisebb sebességgel:

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

Ha a szűrés nem *korrelált*, a fenti szűrő olyan szállodákat ad vissza, amelyekben egy szoba Deluxe, egy másik helyiség pedig 100-nál kisebb alapértékkel rendelkezik. Ez nem lenne értelme, mivel a lambda kifejezés mindkét záradéka ugyanarra a tartományra vonatkozik, azaz `room` . Ez az oka, hogy az ilyen szűrők korrelálnak.

A teljes szöveges kereséshez azonban nincs mód arra, hogy egy adott tartomány változóra hivatkozzon. Ha a mezőhöz tartozó kereséssel [teljes Lucene-lekérdezést](query-lucene-syntax.md) szeretne kiadni:

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

Előfordulhat, hogy a rendszer visszaküldi a szállodát, ahol az egyik szoba Deluxe, a leírásban pedig egy másik szoba a "City View" kifejezésre hivatkozik. Az alábbi dokumentum például megfelel `Id` `1` a lekérdezésnek:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Ennek az az oka, hogy a `Rooms/Type` teljes dokumentumban a mező összes elemzett feltételére hivatkozik `Rooms/Type` , és hasonlóképpen `Rooms/Description` , az alábbi táblázatokban látható módon.

`Rooms/Type`A teljes szöveges keresés tárolása:

| Kifejezés a `Rooms/Type` | Dokumentumok azonosítói |
| --- | --- |
| Deluxe | 1, 2 |
| Standard | 1 |

`Rooms/Description`A teljes szöveges keresés tárolása:

| Kifejezés a `Rooms/Description` | Dokumentumok azonosítói |
| --- | --- |
| Courtyard | 2 |
| city | 1 |
| kertészkedni | 1 |
| nagy | 1 |
| Motel | 2 |
| szoba | 1, 2 |
| Standard | 1 |
| Suite | 1 |
| megtekintése | 1 |

Tehát a fenti szűrővel ellentétben, ami alapvetően azt mondja, hogy "a dokumentumok egyeztetése, ha egy szoba a `Type` " Deluxe szoba "értékkel rendelkezik, és **ugyanazon a helyen** `BaseRate` kevesebb mint 100", a keresési lekérdezés azt írja le, hogy "a" Deluxe "kifejezést tartalmazó dokumentumok megfelelnek, `Rooms/Type` és `Rooms/Description` a" City View "kifejezéssel rendelkezik. Az egyes helyiségek nem rendelkeznek olyan fogalmakkal, amelyek mezői összekapcsolhatók az utóbbi esetben.

> [!NOTE]
> Ha szeretné megtekinteni az Azure Cognitive Searchhoz hozzáadott korrelált keresés támogatását, kérjük, szavazzon [erre a felhasználói hangelemre](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Invertált indexek és gyűjtemények

Előfordulhat, hogy észrevette, hogy az összetett gyűjteményeknél jóval kevesebb korlátozás vonatkozik a lambda kifejezésekre, mint például a (z), és hasonló egyszerű gyűjtemények esetében `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)` . Ennek az az oka, hogy az Azure Cognitive Search az aldokumentumok tényleges gyűjteményei tárolja az összetett gyűjteményeket, míg az egyszerű gyűjtemények egyáltalán nem gyűjteményként vannak tárolva.

Tegyük fel például, hogy egy szűrhető karakterlánc-gyűjtési mező, például egy `seasons` online kiskereskedő indexe. Az indexbe feltöltött egyes dokumentumok így néznek ki:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

A `seasons` mező értékét egy **fordított index** nevű struktúrában tárolja a rendszer, amely a következőhöz hasonlóan néz ki:

| Időszak | Dokumentumok azonosítói |
| --- | --- |
| Spring | 1, 2 |
| nyári | 1 |
| esik | 1, 2 |
| téli | 2, 3 |

Ezt az adatszerkezetet úgy tervezték, hogy nagy sebességgel válaszoljon egy kérdésre: milyen dokumentumokon jelenik meg egy adott kifejezés? A kérdés megválaszolásához hasonlóan működik egy egyszerű egyenlőségi vizsgálat, mint egy gyűjteményben. Valójában ez azért van így, mert a karakterlánc-gyűjtemények esetében az Azure Cognitive Search csak a `eq` lambda kifejezésen belüli összehasonlítási operátorként engedélyezi `any` .

Az egyenlőségből való kiépítés után a következő lépésben megvizsgáljuk, hogyan lehet egyszerre több egyenlő ellenőrzést egyesíteni ugyanazon a tartományon belüli változóban `or` . Az algebra és [a mennyiségileg kiterjesztő tulajdonságának](https://en.wikipedia.org/wiki/Existential_quantification#Negation)köszönhetően működik. Ez a kifejezés:

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

egyenértékű a következővel:

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

a két `any` alkifejezést pedig hatékonyan lehet végrehajtani a fordított index használatával. Emellett a mennyiségi felmondás [törvényének köszönhetően a](https://en.wikipedia.org/wiki/Existential_quantification#Negation)következő kifejezés:

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

egyenértékű a következővel:

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

Ezért lehetséges a `all` és a használata `ne` `and` .

> [!NOTE]
> Bár a részletek ezen dokumentum hatókörén kívül esnek, ugyanazok az alapelvek a [földrajzi térbeli pontok gyűjteményeit is kiterjesztik a távolságra és a metszeti tesztekre](search-query-odata-geo-spatial-functions.md) . Ezért `any` :
>
> - `geo.intersects` nem lehet megfosztani
> - `geo.distance`össze kell hasonlítani a vagy a használatával `lt``le`
> - a kifejezéseket kombinálni kell `or` , nem `and`
>
> A Converse szabályok érvényesek `all` .

A (z),, `lt` `gt` `le` és `ge` operátorokat támogató adattípusok, például `Collection(Edm.Int32)` például a-gyűjtemények esetében a kifejezések szélesebb választéka engedélyezett. Pontosabban használhatja a-t és `and` `or` a-t is, feltéve, hogy `any` az alapul szolgáló összehasonlító kifejezések **tartományhoz viszonyítva vannak összehasonlítva** a használatával `and` , amelyek ezután tovább kombinálhatók a használatával `or` . A logikai kifejezések ezen struktúrájának neve [diszjunkt kötelezőségi Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), más néven "Örs of and". Ezzel szemben az `all` Ehhez az adattípusokhoz tartozó lambda kifejezéseknek a [Conjunctive normál formában (cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)kell lenniük, más néven a "Örs and". Az Azure Cognitive Search lehetővé teszi az ilyen címtartomány-összehasonlításokat, mivel azokat a lefordított indexek hatékony használatával hajthatja végre, ugyanúgy, mint a karakterláncok gyors kifejezésének kereséséhez.

Összefoglalva: Itt láthatók a lambda kifejezésben engedélyezett, a következőre vonatkozó szabályok:

- A `any` *pozitív ellenőrzések* mindig megengedettek, például az egyenlőség, a tartomány-összehasonlítás, `geo.intersects` vagy `geo.distance` a `lt` (z) vagy a `le` ("Bezárás", mint az egyenlőség ellenőrzése, ha a távolságot ellenőrzi).
- `any` `or` A szolgáltatáson belül mindig engedélyezett. `and`Csak olyan adattípusokhoz használható, amelyek expressz tartomány-ellenőrzéseket használhatnak, és csak akkor, ha a and (DNF)-t használja.
- A-ben `all` a szabályok fordítottak, csak a *negatív ellenőrzéseket* lehet használni, a `and` mindig használható, és `or` csak a and-ben kifejezett tartomány-ellenőrzéseknél lehet használni.

A gyakorlatban ezek azok a szűrők típusai, amelyeknek a legvalószínűbb, hogy amúgy is használni fogjuk. Továbbra is hasznos lehet, ha tisztában van azzal, hogy mi is lehetséges.

Adott példákat, amelyek esetében engedélyezett a szűrők típusa, és amelyek nem léteznek, tekintse meg az [érvényes gyűjteményi szűrők írását](search-query-troubleshoot-collection-filters.md#bkmk_examples)ismertető témakört.

## <a name="next-steps"></a>Következő lépések  

- [OData-gyűjtési szűrők hibaelhárítása Az Azure-ban Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)