---
title: Szemantikai lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Egy szemantikai lekérdezési típust úgy állíthat be, hogy a részletes tanulási modelleket a keresési rangsor és a relevancia részeként lekérdezheti a feldolgozás, a szándék és a kontextus alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 7f7a09b9e20b461a8a1e448bf4a7b0747a35fbb1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487144"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Szemantikai lekérdezés létrehozása Cognitive Search

> [!IMPORTANT]
> A szemantikai lekérdezés típusa nyilvános előzetes verzióban érhető el, és az előzetes verziójú REST API és Azure Portal is elérhető. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők. A kezdeti előzetes indítás során a szemantikai keresés díjmentes. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

Ebből a cikkből megtudhatja, hogyan hozhat létre szemantikai rangsorolást használó keresési kéréseket, és hogyan hozhat létre szemantikai feliratokat és válaszokat.

A szemantikai lekérdezések általában olyan keresési indexeken működnek, amelyek nagy mennyiségű szöveges tartalomból, például PDF-fájlokból vagy nagyméretű szöveggel ellátott dokumentumokból állnak.

## <a name="prerequisites"></a>Előfeltételek

+ Egy standard szintű keresési szolgáltatás (S1, S2, S3), amely az alábbi régiók egyikében található: USA északi középső régiója, USA nyugati régiója, USA 2. nyugati régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa. Ha az egyik régióban már van S1 vagy nagyobb szolgáltatás, akkor új szolgáltatás létrehozása nélkül kérhet hozzáférést.

+ Hozzáférés a szemantikai keresés előzetes verziójához: [regisztráció](https://aka.ms/SemanticSearchPreviewSignup)

+ Egy meglévő keresési index, amely angol nyelvű tartalmat tartalmaz

+ Lekérdezések küldésére szolgáló keresési ügyfél

  A keresési ügyfélnek támogatnia kell az előzetes verziójú REST API-kat a lekérdezési kérelemben. Használhatja a [Poster](search-get-started-rest.md), a [Visual Studio Code](search-get-started-vs-code.md)vagy az Ön által módosított kódot, hogy Rest-hívásokat hajtson végre az előnézeti API-khoz. Szemantikai lekérdezés elküldéséhez használhatja a Azure Portal [keresési tallózóját](search-explorer.md) is.

+ A [keresési dokumentumok](/rest/api/searchservice/preview-api/search-documents) a szemantikai lehetőséggel és a jelen cikkben ismertetett egyéb paraméterekkel rendelkeznek.

## <a name="whats-a-semantic-query"></a>Mi az a szemantikai lekérdezés?

Cognitive Search a lekérdezés egy paraméteres kérelem, amely meghatározza a lekérdezések feldolgozását és a válasz alakját. A *szemantikai lekérdezés* olyan paramétereket ad hozzá, amelyek felhívhatják a szemantikai visszahelyezési modellt, amely képes felmérni a megfelelő eredmények kontextusát és jelentését, a legfontosabb egyezéseket és a szemantikai válaszokat és feliratokat adja vissza.

A következő kérelem egy alapszintű szemantikai lekérdezésre (válasz nélkül) jellemző.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Ahogy a Cognitive Search összes lekérdezése esetében, a kérelem egyetlen index dokumentum-gyűjteményét célozza meg. Emellett a szemantikai lekérdezés az elemzés, az elemzés és a vizsgálat egymást követő, nem szemantikai lekérdezési folyamatát is kifejti. A különbség abban rejlik, hogy a relevancia kiszámítása milyen módon történik. Az előzetes kiadásban meghatározottak szerint a szemantikai lekérdezés olyan, amelynek *eredményeit* speciális algoritmusok használatával dolgozzák fel újra, és így a szemantikai rangsorban leginkább relevánsnak ítélt egyezések feldolgozhatók az alapértelmezett hasonlósági algoritmus által hozzárendelt pontszámok helyett. 

A kezdeti eredmények közül csak a legfontosabb 50-as egyezés lehet szemantikailag rangsorolva, és az összes belefoglalási felirat szerepel a válaszban. Opcionálisan megadhat egy **`answer`** paramétert a kérelemben egy lehetséges válasz kinyeréséhez. Ez a modell legfeljebb öt lehetséges választ tud készíteni a lekérdezésre, amelyet a keresési oldal tetején választhat.

## <a name="query-using-rest-apis"></a>Lekérdezés REST API-k használatával

A REST API teljes leírása megtalálható a következő helyen: [Search Documents (REST Preview)](/rest/api/searchservice/preview-api/search-documents).

A szemantikai lekérdezések lehetővé teszik a feliratok és a kiemelés automatikus kiemelését. Ha azt szeretné, hogy a válasz tartalmazzon választ, hozzáadhat egy opcionális **`answer`** paramétert is a kérelemhez. Ez a paraméter, valamint maga a lekérdezési karakterlánc felépítése is választ ad a válaszban.

Az alábbi példa a Hotels-Sample-index használatával hoz létre szemantikai lekérdezési kérést szemantikai válaszokkal és feliratokkal:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>A kérelem összeállítása

Ez a szakasz a szemantikai kereséshez szükséges lekérdezési paramétereket ismerteti.

#### <a name="step-1-set-querytype-and-querylanguage"></a>1. lépés: a queryType és a queryLanguage beállítása

Adja hozzá a következő paramétereket a REST-hez. Mindkét paraméter megadása kötelező.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

A queryLanguage konzisztensnek kell lennie az index séma mezőihez rendelt [nyelvi elemzők](index-add-language-analyzers.md) esetében. Ha a queryLanguage "en-us", akkor minden nyelvi elemzőnek angol változatnak ("en. Microsoft" vagy "en. Lucene") is szerepelnie kell. A nyelvtől független elemzők, például a kulcsszavak vagy az egyszerűek nem ütköznek a queryLanguage értékekkel.

Lekérdezési kérelem esetén, ha a [helyesírás-javítást](speller-how-to-add.md)is használja, a beállított queryLanguage a helyesírást, a válaszokat és a feliratokat egyaránt alkalmazza. Az egyes részek esetében nincs felülbírálás. 

Míg a keresési index tartalma több nyelvből is állhat, a lekérdezés bemenete valószínűleg egy. A keresőmotor nem ellenőrzi a queryLanguage, a Language Analyzer és a tartalom összetételének nyelvét, ezért ügyeljen arra, hogy a helytelen eredmények kiépítésének elkerülése érdekében a hatókör-lekérdezések megfelelően legyenek kitéve.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>2. lépés: a searchFields beállítása

Ez a paraméter nem kötelező abban az esetben, ha nincs hiba, ha elhagyja, de a mezők rendezett listájának megadása kifejezetten ajánlott mindkét felirathoz és válaszhoz.

A searchFields paraméterrel azonosíthatók azok a részek, amelyeket a rendszer "szemantikai hasonlóság" esetén kiértékel a lekérdezéshez. Az előzetes verzió esetében nem javasoljuk, hogy a searchFields üresen hagyja, mivel a modellhez olyan célzásra van szükség, amely a legfontosabb, hogy milyen mezőket kell feldolgoznia.

A searchFields sorrendje kritikus. Ha már meglévő egyszerű vagy teljes Lucene-lekérdezésekben használja a searchFields-t, akkor a szemantikai lekérdezés típusára váltáskor mindenképpen újra meg kell keresnie ezt a paramétert.

Kövesse az alábbi irányelveket az optimális eredmények biztosításához, ha két vagy több searchFields van megadva:

+ Csak karakterlánc típusú mezőket és legfelső szintű karakterlánc mezőket tartalmazzon a gyűjteményekben. Ha nem karakterlánc típusú mezőket vagy alacsonyabb szintű mezőket tartalmaz egy gyűjteményben, nincs hiba, de ezek a mezők nem lesznek felhasználva szemantikai rangsorban.

+ Az első mezőnek mindig tömörnek kell lennie (például cím vagy név), ideális esetben 25 szó alatt.

+ Ha az indexnek van olyan URL-mezője, amely szöveges (emberi olvasásra alkalmas, például `www.domain.com/name-of-the-document-and-other-details` nem gépi `www.domain.com/?id=23463&param=eis` ), akkor helyezze a másodikat a listára (vagy először, ha nincs tömör Cím mező).

+ Ezeket a mezőket olyan leíró mezők követik, amelyekben a szemantikai lekérdezésekre adott válasz, például a dokumentum fő tartalma található.

Ha csak egy mező van megadva, használjon olyan leíró mezőket, ahol a szemantikai lekérdezésekre adott válasz található, például a dokumentum fő tartalma. Olyan mezőt válasszon, amely elegendő tartalmat biztosít.

#### <a name="step-3-remove-orderby-clauses"></a>3. lépés: az orderBy záradékok eltávolítása

Távolítson el minden orderBy záradékot, ha már létezik egy meglévő kérelemben. A szemantikai pontszám az eredmények rendezésére szolgál, és ha explicit rendezési logikát tartalmaz, a rendszer HTTP 400-hibát ad vissza.

#### <a name="step-4-add-answers"></a>4. lépés: válaszok hozzáadása

Ha további feldolgozást szeretne felvenni a válaszra, adja hozzá a "válaszok" lehetőséget. A válaszokat (és a feliratokat) a searchFields-ben felsorolt mezőkben található szakaszokból alakítottuk ki. Ügyeljen arra, hogy a searchFields a tartalomban gazdag mezőket tartalmazza, hogy a válaszban a legjobb válaszokat és feliratokat kapja meg.

Vannak explicit és implicit feltételek, amelyek válaszokat hoznak létre. 

+ A explicit feltételek közé tartozik a "Answers = Extracting" hozzáadása. Emellett a teljes válaszban visszaadott válaszok számának megadásához adja hozzá a "Count" értéket, amelyet a következő szám követ: `"answers=extractive|count=3"` .  Az alapértelmezett érték egy. Legfeljebb öt.

+ Az implicit feltételek közé tartozik egy lekérdezési karakterlánc-konstrukció, amely választ ad magának. A "What Hotel 's The Green Room" kifejezésből álló lekérdezés nagyobb valószínűséggel "válaszol", mint egy "Hotel with Fancy Interior" utasításból álló lekérdezés. Ahogy várható, a lekérdezés nem lehet meghatározatlan vagy NULL értékű.

A fontos szempont, hogy ha a lekérdezés nem egy kérdéshez hasonlít, a rendszer kihagyja a válasz feldolgozását, még akkor is, ha a "válaszok" paraméter be van állítva.

#### <a name="step-5-add-other-parameters"></a>5. lépés: egyéb paraméterek hozzáadása

Adja meg a kérésben használni kívánt egyéb paramétereket. Az olyan paraméterek, mint a [speller](speller-how-to-add.md), a [Select](search-query-odata-select.md)és a Count, javítják a kérés minőségét és a válasz olvashatóságát.

Igény szerint testre szabhatja a feliratokra alkalmazott kiemelés stílusát. A feliratok a válasz összegzése a dokumentumban lévő legfontosabb részeknél. A mező alapértelmezett értéke: `<em>`. Ha meg szeretné adni a formázás típusát (például sárga háttér), megadhatja a highlightPreTag és a highlightPostTag.

### <a name="review-the-response"></a>A válasz áttekintése

A fenti lekérdezésre adott válasz a következő egyezést adja vissza, mint a legfelső szintű kivételezés. A feliratok automatikusan, egyszerű szöveggel és Kiemelt verziókkal lesznek visszaadva. A szemantikai válaszokkal kapcsolatos további információkért lásd: [szemantikai rangsorolás és válaszok](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Szemantikai lekérdezésben használt paraméterek

A következő táblázat összefoglalja a szemantikai lekérdezésekben használt lekérdezési paramétereket, hogy azok holisztikusan lássák őket. Az összes paraméter listáját itt tekintheti meg: [dokumentumok keresése (REST előzetes verzió)](/rest/api/searchservice/preview-api/search-documents)

| Paraméter | Típus | Leírás |
|-----------|-------|-------------|
| queryType | Sztring | Az érvényes értékek közé tartozik az egyszerű, a teljes és a szemantikai érték. Szemantikai lekérdezésekhez a "szemantika" érték szükséges. |
| queryLanguage | Sztring | Szemantikai lekérdezésekhez szükséges. Jelenleg csak az "en-us" van implementálva. |
| searchFields | Sztring | A kereshető mezők vesszővel tagolt listája. Nem kötelező, de ajánlott. Meghatározza azokat a mezőket, amelyeken szemantikai rangsorolás történik. </br></br>Az egyszerű és a teljes lekérdezési típusokkal szemben a listában szereplő mezők sorrendje határozza meg a sorrendet.|
| válaszok |Sztring | Választható mező annak megadásához, hogy a szemantikai válaszok szerepeljenek-e az eredményben. Jelenleg csak a "kinyerő" van implementálva. A válaszokat beállíthatja úgy, hogy legfeljebb öt értéket lehessen visszaadni. Az alapértelmezett érték egy. Ez a példa három válasz számát mutatja: "kinyerő \| count3". |

## <a name="query-with-search-explorer"></a>Lekérdezés a keresési ablakban

A következő lekérdezés az API 2020-06-30-es verziójának használatával, valamint a keresési Explorerben futtatott, a beépített szállodákat ábrázoló minta indexet célozza meg. A `$select` záradék csak néhány mezőre korlátozza az eredményeket, így könnyebben megvizsgálható a Search Explorerben a részletes JSON-ban.

### <a name="with-querytypesemantic"></a>A queryType = szemantika

```json
search=nice hotel on water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Az első néhány találat a következő:

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>A queryType (alapértelmezett)

Az összehasonlításhoz futtassa a fenti lekérdezést, és távolítsa el a következőt: `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Figyelje meg, hogy az eredmények nem jelennek `"@search.rerankerScore"` meg, és hogy a különböző szállodák az első három pozícióban jelennek meg.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Következő lépések

Ne felejtse el, hogy a szemantikai rangsorolás és válaszok a kezdeti eredményhalmaz fölé épülnek. A kezdeti eredmények minőségét javító minden logika továbbítja a szemantikai keresést. A következő lépésként tekintse át azokat a funkciókat, amelyek a kezdeti eredményekhez járulnak hozzá, beleértve azokat a elemzőket is, amelyek befolyásolják a sztringek jogkivonatának módját, az eredményeket beállító pontozási profilokat és az alapértelmezett relevanciás algoritmust.

+ [A szöveg feldolgozásának elemzői](search-analyzers.md)
+ [Hasonlóság és pontozás a Cognitive Search](index-similarity-and-scoring.md)
+ [Pontozási profilok hozzáadása](index-add-scoring-profiles.md)
+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)
+ [Helyesírás-ellenőrzés hozzáadása a lekérdezési feltételekhez](speller-how-to-add.md)
