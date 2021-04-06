---
title: Szemantikai lekérdezés létrehozása
titleSuffix: Azure Cognitive Search
description: Egy szemantikai lekérdezési típust úgy állíthat be, hogy a részletes tanulási modelleket a keresési rangsor és a relevancia részeként lekérdezheti a feldolgozás, a szándék és a kontextus alapján.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654662"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Hozzon létre egy lekérdezést a szemantikai feliratok számára Cognitive Search

> [!IMPORTANT]
> A szemantikai keresés nyilvános előzetes verzióban érhető el, és az előzetes verziójú REST API és Azure Portal is elérhető. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

Ebből a cikkből megtudhatja, hogyan hozhatja meg a szemantikai rangsorolást használó keresési kéréseket, és hogyan adja vissza a szemantikai feliratokat (és opcionálisan [szemantikai válaszokat](semantic-answers.md)), és kiemeli a legfontosabb feltételeket és kifejezéseket. Mindkét felirat és válasz a "szemantika" lekérdezési típussal megfogalmazott lekérdezésekben lesz visszaadva.

A feliratok és válaszok szövege szó szerint kinyerhető a keresési dokumentumban lévő szövegből. A szemantikai alrendszer meghatározza, hogy a tartalom milyen részét képezi a felirat vagy a válasz jellemzői, de nem állít össze új mondatokat vagy kifejezéseket. Emiatt a magyarázatokat vagy definíciókat tartalmazó tartalom a legmegfelelőbb a szemantikai kereséshez.

## <a name="prerequisites"></a>Előfeltételek

+ Egy standard szintű keresési szolgáltatás (S1, S2, S3), amely az alábbi régiók egyikében található: USA északi középső régiója, USA nyugati régiója, USA 2. nyugati régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa. Ha az egyik régióban már van S1 vagy nagyobb szolgáltatás, akkor új szolgáltatás létrehozása nélkül kérhet hozzáférést.

+ Hozzáférés a szemantikai keresés előzetes verziójához: [regisztráció](https://aka.ms/SemanticSearchPreviewSignup)

+ Egy meglévő, angol nyelvű tartalmat tartalmazó keresési index

+ Lekérdezések küldésére szolgáló keresési ügyfél

  A keresési ügyfélnek támogatnia kell az előzetes verziójú REST API-kat a lekérdezési kérelemben. Használhatja a [Poster](search-get-started-rest.md), a [Visual Studio Code](search-get-started-vs-code.md)vagy a Code, amely Rest-hívásokat tesz elérhetővé az előzetes verziójú API-khoz. Szemantikai lekérdezés elküldéséhez használhatja a Azure Portal [keresési tallózóját](search-explorer.md) is.

+ A [lekérdezési kérelemnek](/rest/api/searchservice/preview-api/search-documents) tartalmaznia kell a szemantikai beállítást és a jelen cikkben ismertetett egyéb paramétereket.

## <a name="whats-a-semantic-query"></a>Mi az a szemantikai lekérdezés?

Cognitive Search a lekérdezés egy paraméteres kérelem, amely meghatározza a lekérdezések feldolgozását és a válasz alakját. A *szemantikai lekérdezés* olyan paramétereket ad hozzá, amelyek felhívhatják a szemantikai visszahelyezési modellt, amely képes felmérni a megfelelő eredmények kontextusát és jelentését, a legfontosabb egyezéseket és a szemantikai válaszokat és feliratokat adja vissza.

A következő kérelem egy minimális szemantikai lekérdezésre jellemző (válasz nélkül).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Ahogy a Cognitive Search összes lekérdezése esetében, a kérelem egyetlen index dokumentum-gyűjteményét célozza meg. Emellett a szemantikai lekérdezés az elemzés, az elemzés, a vizsgálat és a pontozás egymást követő, nem szemantikai lekérdezési folyamatát is kifejti. 

A különbség a relevancia és a pontozás. Az előzetes kiadásban meghatározottak szerint a szemantikai lekérdezés olyan, amelynek *eredményei* egy szemantikai nyelvi modellel lettek rangsorolva, így az alapértelmezett hasonlósági algoritmus által hozzárendelt pontszámok helyett a szemantikai rangsornak leginkább megfelelőnek kell lennie.

A kezdeti eredmények közül csak a legfontosabb 50-as egyezés lehet szemantikailag rangsorolva, és az összes belefoglalási felirat szerepel a válaszban. Opcionálisan megadhat egy **`answer`** paramétert a kérelemben egy lehetséges válasz kinyeréséhez. További információ: [szemantikai válaszok](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Lekérdezés a keresési ablakban

A [Search Explorer](search-explorer.md) frissült, hogy a szemantikai lekérdezésekre vonatkozó beállításokat is tartalmazzon. Ezek a beállítások a következő lépések elvégzése után láthatóvá válnak a portálon:

1. A Search szolgáltatás [regisztrációja](https://aka.ms/SemanticSearchPreviewSignup) és befogadása az előzetes programba

1. Nyissa meg a portált a következő szintaxissal: `https://portal.azure.com/?feature.semanticSearch=true`

A lekérdezési beállítások közé tartoznak a szemantikai lekérdezések, a searchFields és a helyesírás-korrekciók engedélyezésére szolgáló kapcsolók. A lekérdezési karakterlánchoz is beillesztheti a szükséges lekérdezési paramétereket.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Lekérdezési beállítások a keresési Explorerben" border="true":::

## <a name="query-using-rest"></a>Lekérdezés REST használatával

A [keresési dokumentumok (REST Preview)](/rest/api/searchservice/preview-api/search-documents) használatával programozott módon alakíthatja ki a kérést.

A válasz tartalmazza a feliratok és a kiemelés automatikus kiemelését. Ha azt szeretné, hogy a válasz helyesírás-javítást vagy válaszokat tartalmazzon, adjon hozzá egy opcionális **`speller`** vagy egy **`answers`** paramétert a kéréshez.

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

A következő táblázat összefoglalja a szemantikai lekérdezésekben használt lekérdezési paramétereket, hogy azok holisztikusan lássák őket. Az összes paraméter listáját itt tekintheti meg: [dokumentumok keresése (REST előzetes verzió)](/rest/api/searchservice/preview-api/search-documents)

| Paraméter | Típus | Leírás |
|-----------|-------|-------------|
| queryType | Sztring | Az érvényes értékek közé tartozik az egyszerű, a teljes és a szemantikai érték. Szemantikai lekérdezésekhez a "szemantika" érték szükséges. |
| queryLanguage | Sztring | Szemantikai lekérdezésekhez szükséges. Jelenleg csak az "en-us" van implementálva. |
| searchFields | Sztring | A kereshető mezők vesszővel tagolt listája. Meghatározza azokat a mezőket, amelyeken a szemantikai rangsorolás bekövetkezik, amelyből a rendszer kinyeri a feliratokat és a válaszokat. </br></br>Az egyszerű és a teljes lekérdezési típusokkal szemben a listában szereplő mezők sorrendje határozza meg a sorrendet. További használati utasításokért lásd a [2. lépés: SearchFields beállítása](#searchfields)című témakört. |
| helyesírás | Sztring | Nem kötelező megadni a szemantikai lekérdezéseket, amelyek a keresőmotor elérésének megkezdése előtt kijavítsák a hibásan írt kifejezéseket. További információ: [helyesírás-javítás hozzáadása lekérdezésekhez](speller-how-to-add.md). |
| válaszok |Sztring | Választható paraméterek, amelyek meghatározzák, hogy az eredmény tartalmazza-e a szemantikai válaszokat. Jelenleg csak a "kinyerő" van implementálva. A válaszokat beállíthatja úgy, hogy legfeljebb öt értéket lehessen visszaadni. Az alapértelmezett érték egy. Ez a példa három válasz számát mutatja: "kinyerő \| count3". További információ: [szemantikai válaszok visszaküldése](semantic-answers.md).|

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

A searchFields paraméterrel azonosíthatók azok a részek, amelyeket a rendszer "szemantikai hasonlóság" esetén kiértékel a lekérdezéshez. Az előzetes verzió esetében nem javasoljuk, hogy a searchFields üresen hagyja, mivel a modellhez olyan célzásra van szükség, amely a legfontosabb, hogy milyen mezőket kell feldolgoznia.

A searchFields sorrendje kritikus. Ha már meglévő searchFields használja az egyszerű vagy a teljes Lucene-lekérdezéseket, akkor a paraméter megadásával keresse meg a mezők sorrendjét a szemantikai lekérdezés típusára való váltáskor.

Két vagy több searchFields esetén:

+ Csak karakterlánc típusú mezőket és legfelső szintű karakterlánc mezőket tartalmazzon a gyűjteményekben. Ha nem karakterlánc típusú mezőket vagy alacsonyabb szintű mezőket tartalmaz egy gyűjteményben, nincs hiba, de ezek a mezők nem lesznek felhasználva szemantikai rangsorban.

+ Az első mezőnek mindig tömörnek kell lennie (például cím vagy név), ideális esetben 25 szó alatt.

+ Ha az indexnek van olyan URL-mezője, amely szöveges (emberi olvasásra alkalmas `www.domain.com/name-of-the-document-and-other-details` , például, nem pedig gépi `www.domain.com/?id=23463&param=eis` ), helyezze a másodikat a listára (vagy először, ha nincs tömör Cím mező).

+ Ezeket a mezőket olyan leíró mezők követik, amelyekben a szemantikai lekérdezésekre adott válasz, például a dokumentum fő tartalma található.

Ha csak egy mező van megadva, használjon olyan leíró mezőt, amelyben a szemantikai lekérdezésekre adott válasz található, például a dokumentum fő tartalma. 

#### <a name="step-3-remove-orderby-clauses"></a>3. lépés: az orderBy záradékok eltávolítása

Távolítson el minden orderBy záradékot, ha már létezik egy meglévő kérelemben. A szemantikai pontszám az eredmények rendezésére szolgál, és ha explicit rendezési logikát tartalmaz, a rendszer HTTP 400-hibát ad vissza.

#### <a name="step-4-add-answers"></a>4. lépés: válaszok hozzáadása

Ha további feldolgozást szeretne felvenni a válaszra, adja hozzá a "válaszok" lehetőséget. A válaszok (és a feliratok) ki vannak kinyerve a searchFields-ben felsorolt mezőkben található szakaszokból. Ügyeljen arra, hogy a searchFields a tartalomban gazdag mezőket tartalmazza, hogy a válaszban a legjobb válaszokat kapja meg. További információ: [szemantikai válaszok visszaküldése](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>5. lépés: egyéb paraméterek hozzáadása

Adja meg a kérésben használni kívánt egyéb paramétereket. Az olyan paraméterek, mint a [speller](speller-how-to-add.md), a [Select](search-query-odata-select.md)és a Count, javítják a kérés minőségét és a válasz olvashatóságát.

Igény szerint testre szabhatja a feliratokra alkalmazott kiemelés stílusát. A feliratok a válasz összegzése a dokumentumban lévő legfontosabb részeknél. A mező alapértelmezett értéke: `<em>`. Ha meg szeretné adni a formázás típusát (például sárga háttér), megadhatja a highlightPreTag és a highlightPostTag.

## <a name="evaluate-the-response"></a>A válasz kiértékelése

Ahogy az összes lekérdezés esetében, a válasz a beolvasható, vagy csak a Select paraméterben felsorolt mezőkből áll. Magában foglalja az eredeti relevanciás pontszámot is, és tartalmazhat darabszámot vagy kötegelt eredményeket, attól függően, hogy hogyan alakította ki a kérést.

Szemantikai lekérdezésekben a válasz további elemeket tartalmaz: egy új, szemantikailag rangsorolt relevanciás pontszám, egyszerű szöveg feliratai, és kiemeli a válaszokat, és opcionálisan választ is.

Egy ügyfélalkalmazás esetében feldolgozhatja a keresési oldalt, hogy egy adott mező teljes tartalma helyett egy feliratot is tartalmazzon a egyezés leírásához. Ez akkor hasznos, ha az egyes mezők túl sűrűk a keresési eredmények lapon.

A fenti példában szereplő lekérdezésre adott válasz a következő egyezést adja vissza, mint a legfelső szintű kivételezés. A feliratok automatikusan, egyszerű szöveggel és Kiemelt verziókkal lesznek visszaadva. A válaszok kimaradnak a példából, mert az adott lekérdezés és a corpus nem határozható meg.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Következő lépések

Ne felejtse el, hogy a szemantikai rangsorolás és válaszok a kezdeti eredményhalmaz fölé épülnek. A kezdeti eredmények minőségét javító minden logika továbbítja a szemantikai keresést. A következő lépésként tekintse át azokat a funkciókat, amelyek a kezdeti eredményekhez járulnak hozzá, beleértve azokat a elemzőket is, amelyek befolyásolják a sztringek jogkivonatának módját, az eredményeket beállító pontozási profilokat és az alapértelmezett relevanciás algoritmust.

+ [A szöveg feldolgozásának elemzői](search-analyzers.md)
+ [Hasonlósági rangsor algoritmusa](index-similarity-and-scoring.md)
+ [Pontozási profilok](index-add-scoring-profiles.md)
+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)
+ [Szemantikai rangsorolási algoritmus](semantic-ranking.md)
