---
title: Teljes Lucene lekérdezési szintaxis használata
titleSuffix: Azure Cognitive Search
description: Példák a Lucene lekérdezési szintaxisára a fuzzy kereséshez, a közelségi kereséshez, a kifejezés fokozásához, a reguláris kifejezések kereséséhez és a helyettesítő karakteres keresésekhez egy Azure Cognitive Search indexben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693560"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>A "teljes" Lucene keresési szintaxis használata (speciális lekérdezések az Azure Cognitive Searchban)

Az Azure Cognitive Search-lekérdezések létrehozásakor lecserélheti az alapértelmezett [egyszerű lekérdezés-elemzőt](query-simple-syntax.md) a hatékonyabb [Lucene lekérdezés-elemzővel](query-lucene-syntax.md) , amely speciális és speciális lekérdezési kifejezéseket hoz létre.

A Lucene-elemző összetett lekérdezési formátumokat támogat, például a mező hatókörű lekérdezéseket, a zavaros keresést, a Infix és az utótag helyettesítő karakteres keresését, a közelségi keresést, a kifejezés növelését és a reguláris kifejezések keresését. További feldolgozási követelmények is elérhetők, ezért érdemes lehet valamivel több végrehajtási időt várnia. Ebben a cikkben áttekintheti a lekérdezési műveleteket bemutató példákat a teljes szintaxis alapján.

> [!Note]
> A teljes Lucene lekérdezési szintaxison keresztül engedélyezett speciális lekérdezési szerkezetek közül sok nem kerül [szöveg-elemzésre](search-lucene-query-architecture.md#stage-2-lexical-analysis), ami meglepő lehet, ha az eredmény vagy a morfológiai elemzéshez. A lexikális analízis csak teljes feltételekkel (lekérdezés vagy kifejezéses lekérdezés) hajtható végre. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. Az egyetlen, a részleges lekérdezési feltételeken végrehajtott átalakítás lowercasing. 
>

## <a name="hotels-sample-index"></a>A Hotels-minta indexe

A következő lekérdezések a Hotels-Sample-index alapján jelennek meg, amelyet [az ebben a](search-get-started-portal.md)rövid útmutatóban található utasítások követésével hozhat létre.

A lekérdezéseket a REST API és a POST kérések alapján tagoljuk. A Cognitive Search bővítménnyel beillesztheti és futtathatja a [Poster](search-get-started-rest.md) vagy a [Visual Studio Code](search-get-started-vs-code.md)-ban.

A kérelem fejlécének a következő értékekkel kell rendelkeznie:

| Kulcs | Érték |
|-----|-------|
| Content-Type | application/json|
| API-kulcs  | `<your-search-service-api-key>`, vagy lekérdezési vagy rendszergazdai kulcs |

Az URI-paramétereknek tartalmaznia kell a keresési szolgáltatás végpontját a következő példához hasonlóan: index neve, docs-gyűjtemények, keresési parancs és API-verzió.

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

A kérelem törzsét érvényes JSON-ként kell létrehozni:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ a "Search" tulajdonság értéke `*` nem megadott lekérdezés, amely null vagy üres kereséssel egyenértékű. Ez nem különösen hasznos, de ez a legegyszerűbb keresési lehetőség, amely az index összes lekérdezhető mezőjét megjeleníti az összes értékkel együtt.

+ a "queryType" értéke "Full" értékre állítja a teljes Lucene lekérdezés-elemzőt, és ez szükséges ehhez a szintaxishoz.

+ a "Select" beállítást a rendszer a mezők vesszővel tagolt listájára használja a keresési eredmények összetételéhez, beleértve azokat a mezőket is, amelyek a keresési eredmények kontextusában hasznosak.

+ a "Count" érték a keresési feltételeknek megfelelő dokumentumok számát adja vissza. Üres keresési sztring esetén a szám az indexben lévő összes dokumentumot (a 50-as verzióban a Hotels-Sample-index esetében) fogja tartalmazni.

## <a name="example-1-fielded-search"></a>1. példa: mező szerinti keresés

A mezőben megadott keresési hatókör egyedi, beágyazott keresési kifejezése egy adott mezőre vonatkozik. Ez a példa a "Hotel" kifejezéssel megkeresi a szállodai neveket, de a "Motel" kifejezést nem. Több mezőt is megadhat a és a használatával. 

Ha ezt a lekérdezési szintaxist használja, akkor kihagyhatja a "searchFields" paramétert, ha a lekérdezni kívánt mezők magukban a keresési kifejezésben vannak. Ha a "searchFields" mezővel szerepel, a `fieldName:searchExpression` mindig elsőbbséget élvez a "searchFields" kifejezéssel szemben.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

A lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie, amely a "Resort and Spa" szűrve, a "Hotel" vagy a "Motel" kifejezést tartalmazó szállodákat adja vissza a névben.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

A keresési kifejezés lehet egy kifejezés vagy egy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, az idézőjelek közé kell helyeznie egy kifejezést, ahogy ebben az esetben a StateProvince mezőben két különálló helyet keres. Az ügyféltől függően előfordulhat, hogy el kell menekülnie ( `\` ) az idézőjeleket.

A mezőben megadott mezőnek `fieldName:searchExpression` kereshető mezőnek kell lennie. További részletekért lásd: [create index (REST API)](/rest/api/searchservice/create-index) .

## <a name="example-2-fuzzy-search"></a>2. példa: fuzzy keresés

A zavaros keresés a hasonló kifejezésekre hasonlít, beleértve a hibásan írt szavakat is. A zavaros kereséshez fűzze hozzá a tilde `~` szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti értéket, amely megadja a szerkesztési távolságot. Például a `blue~` `blue~1` kék, a blues és a ragasztó is visszatérhet.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

A lekérdezésre adott válasz a megfelelő dokumentumokban lévő "concierge"-re van feloldva, a rövidség kedvéért:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

A kifejezések nem támogatottak közvetlenül, de a több részből álló kifejezések minden egyes időszakában megadható egy fuzzy egyezés, például: `search=Tags:landy~ AND sevic~` .  Ez a lekérdezési kifejezés a "mosodai szolgáltatás" 15 találatát keresi.

> [!Note]
> A zavaros lekérdezések [elemzése](search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. Hiányos kifejezésekkel rendelkező lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex lekérdezés, fuzzy lekérdezés) közvetlenül a lekérdezési fában lesznek hozzáadva, az elemzési szakasz megkerülésével. A részleges lekérdezési feltételeken végrehajtott átalakítások alacsonyabbak.
>

## <a name="example-3-proximity-search"></a>3. példa: a közelség keresése

A közelségi keresés megkeresi a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát.

Ez a lekérdezés a "Hotel" és a "repülőtér" kifejezést keresi a dokumentumban lévő 5 szó szerint. Az idézőjelek megmaradnak ( `\"` ) a kifejezés megőrzése érdekében:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

A lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>4. példa: a kifejezés fokozása

A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Egy kifejezés növeléséhez használja a kalapot, a `^` szimbólumot a keresett kifejezés végén lévő kiemelés faktor (a szám) karakterrel. A Boost faktor alapértelmezett értéke 1, és bár pozitívnak kell lennie, akkor 1-nél kisebb lehet (például 0,2). A kifejezés fokozása különbözik az adott pontozási profilokban lévő pontozási profiloktól, és nem adott feltételekkel javítja bizonyos mezőket.

Ebben az "előtt" lekérdezésben keressen rá a "strand-hozzáférés" kifejezésre, és figyelje meg, hogy hét dokumentum felel meg egy vagy mindkét feltételnek.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Valójában csak egy olyan dokumentum létezik, amely megfelel a "hozzáférés" kifejezésnek, és mivel ez az egyetlen egyezés, az elhelyezés magas (második pozíció), bár a dokumentumból hiányzik a "Beach" kifejezés.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

Az "After" lekérdezésben ismételje meg a keresést, ez az idő fokozza az eredményeket a "strand" kifejezéssel a "hozzáférés" kifejezés alatt. A lekérdezés egy olvasható verziója `search=Description:beach^2 access` . Az ügyféltől függően előfordulhat, hogy a kifejezésre kell mutatnia `^2` `%5E2` .

A "Beach" kifejezés kiemelése után a régi Carrabelle-beli egyezés a hatodik helyre lép.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>5. példa: regex

A reguláris kifejezéses keresés a "/" perjelek közötti, a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált tartalom alapján keres egyezést.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> A regex-lekérdezések [elemzése](./search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. A részleges lekérdezési feltételeken végrehajtott átalakítások alacsonyabbak.
>

## <a name="example-6-wildcard-search"></a>6. példa: helyettesítő karakteres keresés

Az általánosan felismert szintaxis több ( `*` ) vagy szimpla ( `?` ) karakteres helyettesítő karakteres kereséshez használható. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.

A lekérdezésben keresse meg az "sc" előtagot tartalmazó szállodai neveket. A `*` Keresés első karaktere nem használható a vagy a `?` szimbólumként.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> A helyettesítő karakteres lekérdezések [elemzése](./search-lucene-query-architecture.md#stage-2-lexical-analysis)nem történik meg. A részleges lekérdezési feltételeken végrehajtott átalakítások alacsonyabbak.
>

## <a name="next-steps"></a>Következő lépések

Próbálja meg megadni a lekérdezéseket a kódban. Az alábbi hivatkozások azt ismertetik, hogyan állíthat be keresési lekérdezéseket az Azure SDK-k használatával.

+ [Az index lekérdezése a .NET SDK használatával](search-get-started-dotnet.md)
+ [Az index lekérdezése a Python SDK használatával](search-get-started-python.md)
+ [Az index lekérdezése a JavaScript SDK használatával](search-get-started-javascript.md)

A szintaxissal, a lekérdezési architektúrával és a példákkal kapcsolatban a következő hivatkozásokban találhat további tudnivalókat:

+ [Példák a speciális lekérdezések kiépítési Lucene](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](query-simple-syntax.md)
+ [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [Szűrési szintaxis](search-query-odata-filter.md)