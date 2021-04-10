---
title: Egyszerű Lucene lekérdezési szintaxis használata
titleSuffix: Azure Cognitive Search
description: Példák a teljes szöveges keresés egyszerű szintaxisára, a keresés szűrésére és a Geo-keresésre egy Azure Cognitive Search indexben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694036"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Az "egyszerű" keresési szintaxis használata az Azure-ban Cognitive Search

Az Azure Cognitive Searchban az [egyszerű lekérdezési szintaxis](query-simple-syntax.md) meghívja a teljes szöveges keresés alapértelmezett lekérdezés-elemzőjét. Az elemző gyorsan és gyakran kezeli a gyakori forgatókönyveket, például a teljes szöveges keresést, a szűrt és a sokoldalú keresést, valamint az előtag-keresést. Ez a cikk példákat mutat be a [keresési dokumentumok (REST API)](/rest/api/searchservice/search-documents) kérelem egyszerű szintaxisának megjelenítésére.

> [!NOTE]
> Egy alternatív lekérdezési szintaxis [teljes Lucene](query-lucene-syntax.md), amely összetettebb lekérdezési struktúrákat támogat, például a fuzzy és a helyettesítő karakterek keresését. További információt és példákat [a teljes Lucene szintaxis használata](search-query-lucene-examples.md)című témakörben talál.

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ a "Search" tulajdonság értéke `*` nem megadott lekérdezés, amely null vagy üres kereséssel egyenértékű. Ez nem különösen hasznos, de ez a legegyszerűbb keresési lehetőség, amely az index összes lekérdezhető mezőjét megjeleníti az összes értékkel együtt.

+ az alapértelmezett érték "Simple" ("queryType") értékre van állítva, de a rendszer azt is megerősíti, hogy a cikkben szereplő példák egyszerű szintaxissal vannak kifejezve.

+ a "Select" beállítást a rendszer a mezők vesszővel tagolt listájára használja a keresési eredmények összetételéhez, beleértve azokat a mezőket is, amelyek a keresési eredmények kontextusában hasznosak.

+ a "Count" érték a keresési feltételeknek megfelelő dokumentumok számát adja vissza. Üres keresési sztring esetén a szám az indexben lévő összes dokumentumot (a 50-as verzióban a Hotels-Sample-index esetében) fogja tartalmazni.

## <a name="example-1-full-text-search"></a>1. példa: teljes szöveges keresés

A teljes szöveges keresés tetszőleges számú önálló kifejezést vagy idézőjelbe foglalt kifejezést tartalmazhat, logikai operátorokkal vagy anélkül. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

A fontos kifejezésekből vagy kifejezésből álló kulcsszavas keresések általában a legjobban működnek. A karakterlánc-mezők szöveges elemzést végeznek az indexelés és a lekérdezés során, a nem alapvető szavakat, például a "The", a "és a" kifejezést. Ha szeretné megtekinteni, hogy a lekérdezési karakterlánc hogyan legyen jogkivonatban az indexben, adja át a karakterláncot egy [elemzett szöveges](/rest/api/searchservice/test-analyzer) hívásban az indexnek.

A "searchMode" paraméter vezérli a pontosságot és a visszahívást. Ha további visszahívásra van szüksége, használja az alapértelmezett "any" értéket, amely visszaadja az eredményt, ha a lekérdezési karakterlánc bármely része megfelel. Ha a pontosságot részesíti előnyben, a sztring összes részének egyeznie kell a searchMode "all" értékre való módosításával. Próbálja ki a fenti lekérdezést mindkét módon, hogy megtekintse, hogyan változtatja meg a searchMode az eredményt.

A "Pool Spa + Airport" lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie, a rövidség kedvéért.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Figyelje meg a válasz keresési pontszámát. Ez a egyezés relevanciás pontszáma. Alapértelmezés szerint a keresési szolgáltatás a pontszám alapján a legfontosabb 50-es egyezést fogja visszaadni.

A "1,0" egységes pontszáma akkor fordul elő, ha nincs rangsor, vagy mert a keresés nem volt teljes szöveges keresés, vagy mert nem adtak meg feltételeket. Egy üres keresés (Search =) esetében például a `*` sorok tetszőleges sorrendben jönnek vissza. A tényleges feltételek belefoglalásakor a keresési pontszámok jelentős értékekre lesznek kialakítva.

## <a name="example-2-look-up-by-id"></a>2. példa: Keresés azonosító alapján

Ha a keresési eredményeket egy lekérdezésben adja vissza, a logikai következő lépés egy olyan részleteket tartalmazó oldal megadása, amely több mezőt tartalmaz a dokumentumból. Ebből a példából megtudhatja, hogyan adhat vissza egyetlen dokumentumot a [keresési dokumentum](/rest/api/searchservice/lookup-document) használatával a dokumentum azonosítójának átadásával.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Minden dokumentum egyedi azonosítóval rendelkezik. Ha a portált használja, válassza az indexek az **indexek** lapról lehetőséget, majd tekintse meg a mezők definícióit, és állapítsa meg, hogy melyik mező a kulcs. A REST használatával az [index lekérése](/rest/api/searchservice/get-index) hívás a válasz törzsében lévő index definícióját adja vissza.

A fenti lekérdezésre adott válasz a 41-es kulccsal rendelkező dokumentumból áll. Az index definíciójában a "lekérhető" értékkel jelölt mezők a keresési eredményekben és a megjelenített alkalmazásban adhatók vissza.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>3. példa: szűrés szövegre

A [szűrési szintaxis](search-query-odata-filter.md) egy OData kifejezés, amelyet önmagával vagy a "kereséssel" is használhat. Együtt használva a "filter" először a teljes indexre lesz alkalmazva, majd a keresés a szűrő eredményein történik. A szűrők éppen ezért hasznosak a lekérdezés teljesítményének javítására, mivel általuk lecsökkenthető a keresési lekérdezés által feldolgozandó dokumentumok köre.

A szűrők az index definíciójában "szűrhető" jelöléssel jelölt bármely mezőnél meghatározhatók. A Hotels-Sample-index, a szűrhető mezők kategória-, címke-, ParkingIncluded-, minősítési és a legtöbb címtartományt tartalmaz.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A fenti lekérdezésre adott válasz csak azokra a szállodákra vonatkozik, amelyek a "jelentés és a fürdő" kifejezéssel vannak kategorizálva, amelyek tartalmazzák a "művészet" vagy a "túrák" kifejezést. Ebben az esetben csak egyetlen egyezés van.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>4. példa: szűrési függvények

A szűrési kifejezések tartalmazhatják a ["Search. ismatch" és a "Search. ismatchscoring" függvényeket](search-query-odata-full-text-search-functions.md), amelyek lehetővé teszik, hogy keresési lekérdezést hozzon létre a szűrőn belül. A szűrő kifejezése *ingyenes* helyettesítő karaktert használ a szolgáltatások kiválasztásához, beleértve az ingyenes Wi-Fi-t, az ingyenes parkolást és így tovább.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

A fenti lekérdezésre adott válasz az ingyenes kényelmi szolgáltatásokat kínáló 19 szálláshelyre illeszkedik. Figyelje meg, hogy a keresési pontszám egy egységes "1,0" a találatok között. Ennek az az oka, hogy a keresési kifejezés null értékű vagy üres, ami a Verbatim-szűrőnek felel meg, de nem tartalmaz teljes szöveges keresést. A relevancia pontszámok csak a teljes szöveges kereséshez lesznek visszaadva. Ha "keresés" nélkül használja a szűrőket, győződjön meg arról, hogy elegendő rendezhető mezőket tartalmaz, hogy a keresési rangsorban is beállítható legyen.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>5. példa: tartomány szűrőinek

A tartomány szűrése bármely adattípushoz tartozó szűrők kifejezéssel támogatott. Az alábbi példák a numerikus és a karakterlánc-tartományokat szemléltetik. Az adattípusok fontosak a tartományhoz tartozó szűrőkben, és akkor működnek a legjobban, ha numerikus mezőkben numerikus adat szerepel, és a sztring mezőkben sztring szerepel. A karakterlánc mezőiben szereplő numerikus adat nem alkalmas tartományokhoz, mert a numerikus karakterláncok nem hasonlíthatók össze.

A következő lekérdezés egy numerikus tartomány. A Hotels-Sample-index esetében az egyetlen szűrhető numerikus mező a minősítés.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

A lekérdezésre adott válasznak a következő példához hasonlóan kell kinéznie, rövidítve.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

A következő lekérdezés egy karakterlánc-mező (címe/StateProvince) feletti tartomány szűrője:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

A lekérdezésre adott válasznak az alábbi példához hasonlóan kell kinéznie, amely a rövidség kedvéért van kimetszve. Ebben a példában nem rendezheti a StateProvince, mert a mező nem "rendezhető" az index definíciójában.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>6. példa: Geo-keresés

A Hotels-Sample index egy geo_location mezőt tartalmaz szélességi és hosszúsági koordinátákkal. Ez a példa a [Geo. Distance függvényt](search-query-odata-geo-spatial-functions.md#examples) használja, amely egy kiindulási pont kerületén belüli dokumentumok szűrésére szolgál, az Ön által megadott távolságra (kilométerben). A lekérdezésben szereplő utolsó értéket (10) módosíthatja a lekérdezés felületi területének csökkentése vagy nagyítása érdekében.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

A lekérdezésre adott válasz az összes olyan szállodát adja vissza, amely a megadott koordináták 10 kilométeres távolságára esik:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>7. példa: logikai értékek searchMode

Az egyszerű szintaxis karakterek () formájában támogatja a logikai operátorokat a `+, -, |` és a, a és a nem lekérdezési logikájának támogatásához. A logikai keresés a várt módon viselkedik, és néhány figyelemreméltó kivételt is tartalmaz. 

Az előző példákban a "searchMode" paraméter a pontosság és visszahívás befolyásolására szolgáló mechanizmusként jelent meg, a "searchMode = any" szívességet (a feltételek bármelyikének megfelelő dokumentum egyezésnek számít), és a "searchMode = minden" előnyben részesített pontosságot (az összes feltételt egy dokumentumban kell megfeleltetni). 

Egy logikai keresés kontextusában a "searchMode = any" alapértelmezett érték zavaró lehet, ha több operátorral rendelkező lekérdezést halmoz fel, és a szűkebb eredmények helyett szélesebb körűen fog megjelenni. Ez különösen igaz, ha a találatok között az összes dokumentum nem tartalmazza az adott kifejezést vagy kifejezést.

Az alábbi példa egy illusztrációt tartalmaz. A következő lekérdezés futtatása a searchMode (any), a 42-es dokumentumokat adja vissza: az "étterem" kifejezést tartalmazza, valamint az összes olyan dokumentumot, amely nem rendelkezik a "klíma" kifejezéssel. 

Figyelje meg, hogy nincs szóköz a logikai operátor ( `-` ) és a "klíma" kifejezés között.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Ha a "searchMode = all" értékre változik, a feltételek kumulatív hatással vannak a feltételekre, és egy kisebb eredményhalmazt (7 egyezést) ad vissza, amely az "étterem" kifejezést tartalmazza, mínusz a "klíma" kifejezést tartalmazó dokumentumok.

A lekérdezésre adott válasz mostantól az alábbi példához hasonlóan fog kinézni, rövidítve.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>8. példa: lapozás eredményei

Az előző példákban megtanulta azokat a paramétereket, amelyek befolyásolják a keresési eredmények összetételét, beleértve a "Select" attribútumot is, amely meghatározza, hogy mely mezők vannak az eredményben, a rendezési sorrendek, valamint az összes egyezés számának szerepeltetése. Ez a példa a keresési eredmények összetételének folytatása a lapozási paraméterek formájában, amely lehetővé teszi, hogy az adott oldalon megjelenő találatok számát batch-ként adja meg. 

Alapértelmezés szerint a Search szolgáltatás a legfelső 50 egyezést adja vissza. Az egyes lapokban található egyezések számának szabályozásához használja a "Top" értéket a köteg méretének meghatározásához, majd a "kihagyás" lehetőséggel válassza ki a további kötegeket.

Az alábbi példa egy szűrőt és rendezési sorrendet használ a minősítési mezőben (a minősítés egyszerre szűrhető és rendezhető), mert könnyebben megtekinthető a rendezett eredmények lapozásának hatása. A normál teljes keresési lekérdezésekben a legfontosabb egyezéseket a "" értékkel rangsorolják és Lapozzák @search.score .

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

A lekérdezés 21 egyező dokumentumot talál, de mivel a "Top" értéket adta meg, a válasz csak az első öt egyezést adja vissza, a minősítést pedig a 4,9-kor kezdődően, és a 4,7-as "Lady of the Lake B & B" karakterrel végződik. 

A következő 5 beszerzéséhez hagyja ki az első köteget:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

A második kötegre adott válasz kihagyja az első öt találatot, a következő öt, a "Pull'r Inn Motel" kifejezéssel kezdődően. A további kötegek folytatásához a "Top" érték marad 5, majd minden új kérelemnél a "Skip" (kihagyás = 5, kihagyás = 10, kihagyás = 15 és így tovább) értékre kell emelkedni.

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már rendelkezik gyakorlattal az alapszintű lekérdezési szintaxissal, próbálkozzon a lekérdezéseknek a kódban való megadásával. Az alábbi hivatkozások azt ismertetik, hogyan állíthat be keresési lekérdezéseket az Azure SDK-k használatával.

+ [Az index lekérdezése a .NET SDK használatával](search-get-started-dotnet.md)
+ [Az index lekérdezése a Python SDK használatával](search-get-started-python.md)
+ [Az index lekérdezése a JavaScript SDK használatával](search-get-started-javascript.md)

A szintaxissal, a lekérdezési architektúrával és a példákkal kapcsolatban a következő hivatkozásokban találhat további tudnivalókat:

+ [Példák a speciális lekérdezések kiépítési Lucene](search-query-lucene-examples.md)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Egyszerű lekérdezési szintaxis](query-simple-syntax.md)
+ [Teljes Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [Szűrési szintaxis](search-query-odata-filter.md)