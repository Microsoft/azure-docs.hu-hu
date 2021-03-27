---
title: Helyesírás-ellenőrzés hozzáadása
titleSuffix: Azure Cognitive Search
description: Csatolja a lekérdezési folyamat helyesírás-javítását, hogy a lekérdezés végrehajtása előtt kijavítsa a lekérdezési feltételekkel kapcsolatos hibákat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625908"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Helyesírás-ellenőrzés hozzáadása a lekérdezésekhez Cognitive Search

> [!IMPORTANT]
> A helyesírás-javítás nyilvános előzetes verzióban érhető el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők. A kezdeti előzetes indítás során a speller díjmentesen használható. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A keresőmotor megkezdése előtt az egyes keresési lekérdezési kifejezések helyesírásának javításával javíthatja a visszahívást. A **helyesírás** -ellenőrző paraméter az összes lekérdezési típus esetében támogatott: [egyszerű](query-simple-syntax.md), [teljes](query-lucene-syntax.md), és az új [szemantikai](semantic-how-to-query-request.md) lehetőség jelenleg nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

+ Egy meglévő keresési index, amely angol nyelvű tartalmat tartalmaz. A helyesírás-javítás jelenleg nem működik a [szinonimákkal](search-synonyms.md). Ne használja olyan indexeken, amelyekben szinonimákat kell megadnia bármely mező definíciójában.

+ Lekérdezések küldésére szolgáló keresési ügyfél

  A keresési ügyfélnek támogatnia kell az előzetes verziójú REST API-kat a lekérdezési kérelemben. Használhatja a [Poster](search-get-started-rest.md), a [Visual Studio Code](search-get-started-vs-code.md)vagy az Ön által módosított kódot, hogy Rest-hívásokat hajtson végre az előnézeti API-khoz.

+ A helyesírás-korrekciót használó [lekérdezési kérelem](/rest/api/searchservice/preview-api/search-documents) "API-Version = 2020-06 -30-Preview", "speller = lexikon" és "queryLanguage = en-us".

  A queryLanguage szükséges a helyesíráshoz, és jelenleg az "en-us" az egyetlen érvényes érték.

> [!Note]
> A helyesírás-ellenőrző paraméter minden szinten elérhető, ugyanabban a régióban, ahol szemantikai keresést is biztosít. Nem kell regisztrálnia az előzetes verziójú szolgáltatáshoz való hozzáférésre. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Helyesírás-javítás egyszerű kereséssel

Az alábbi példa a beépített Hotels-Sample index használatával mutatja be a helyesírás-korrekciót egy egyszerű, ingyenes szöveges lekérdezésben. A helyesírás-javítás nélkül a lekérdezés nulla eredményt ad vissza. A kijavítással a lekérdezés egy eredményt ad vissza a Johnson család-orientált üdülőhelyének.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Helyesírás-javítás teljes Lucene

A helyesírási javítás az egyes lekérdezési kifejezéseken történik, amelyek elemzést végeznek, ezért a speller paramétert néhány Lucene-lekérdezéssel használhatja, másokat azonban nem.

+ Nem kompatibilis lekérdezési űrlapok, amelyek megkerülik a szöveges elemzést: helyettesítő karakter, regex, fuzzy
+ A kompatibilis lekérdezési űrlapok a következők: mezős keresés, közelség, kifejezés fellendítése

Ebben a példában a category (kategória) mezőn, a teljes Lucene szintaxissal, valamint egy hibásan írt lekérdezési kifejezéssel végzett, mezőben lévő keresést használunk. A helyesírás-ellenőrzővel együtt a "Suiite" elírása kijavítva, a lekérdezés pedig sikeres lesz.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Helyesírás-javítás szemantikai kereséssel

Ez a lekérdezés minden kifejezésben, kivéve az egyiket, a helyesírási hibák kijavításával visszaküldi a vonatkozó eredményeket. További információt a [szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md)című témakörben talál.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Nyelvi megfontolások

A helyesíráshoz szükséges queryLanguage paraméternek konzisztensnek kell lennie az index séma mezőihez rendelt [nyelvi elemzők](index-add-language-analyzers.md) esetében. 

+ a queryLanguage meghatározza, hogy mely lexikonok használhatók a helyesírás-ellenőrzéshez, és a [szemantikai rangsorolási algoritmus](semantic-answers.md) bemenetként is használatos, ha "queryType = szemantika" eszközt használ.

+ A nyelvi elemzők az indexelés és a lekérdezés végrehajtása során használatosak a keresési indexben található egyező dokumentumok kereséséhez. Példa egy Language Analyzert használó mező-definícióra `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Ha a queryLanguage az "en-us", a helyesírást a legjobb eredmény érdekében kell megadni, akkor a nyelvi elemzőknek angol változatnak ("en. Microsoft" vagy "en. Lucene") is szerepelniük kell.

> [!NOTE]
> A nyelvtől független elemzők (például a kulcsszó, a Simple, a standard, a stop, a szóköz vagy `standardasciifolding.lucene` a) nem ütköznek a queryLanguage beállításaival.

A lekérdezési kérelemben a beállított queryLanguage a speller, a Answers és a Captions elemre egyformán vonatkozik. Az egyes részek esetében nincs felülbírálás.

Míg a keresési index tartalma több nyelvből is állhat, a lekérdezés bemenete valószínűleg egy. A keresőmotor nem ellenőrzi a queryLanguage, a Language Analyzer és a tartalom összetételének nyelvét, ezért ügyeljen arra, hogy a helytelen eredmények kiépítésének elkerülése érdekében a hatókör-lekérdezések megfelelően legyenek kitéve.

## <a name="next-steps"></a>Következő lépések

+ [Szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md)
+ [Alapszintű lekérdezés létrehozása](search-query-create.md)
+ [Teljes Lucene lekérdezési szintaxis használata](query-Lucene-syntax.md)
+ [Egyszerű lekérdezési szintaxis használata](query-simple-syntax.md)
+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)