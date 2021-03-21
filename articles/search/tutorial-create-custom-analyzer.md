---
title: 'Oktatóanyag: egyéni elemző létrehozása'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre egyéni elemzőt a keresési eredmények minőségének javításához az Azure Cognitive Searchban.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99097657"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Oktatóanyag: egyéni analizátor létrehozása telefonszámokhoz

Az [elemzők](search-analyzers.md) bármely keresési megoldás egyik kulcsfontosságú összetevője. A keresési eredmények minőségének javítása érdekében fontos megérteni, hogy az elemzők hogyan működnek és milyen hatással vannak az eredményekre.

Bizonyos esetekben, például egy ingyenes szövegmezővel, a megfelelő [nyelvi elemző](index-add-language-analyzers.md) kiválasztásával javíthatja a keresési eredményeket. Bizonyos esetekben azonban előfordulhat, hogy a telefonszámok, URL-címek vagy e-mailek pontos keresése az egyéni elemzők használatát igényli.

Ez az oktatóanyag a Poster és az Azure Cognitive Search [REST API](/rest/api/searchservice/) -jait használja a következőre:

> [!div class="checklist"]
> * Az elemzők működésének ismertetése
> * Egyéni elemző definiálása a telefonszámok kereséséhez
> * Az egyéni elemző tokenizes szövegének tesztelése
> * Külön elemzők létrehozása az indexeléshez és a kereséshez az eredmények további javítása érdekében

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következő szolgáltatások és eszközök szükségesek.

+ [Postman asztali alkalmazás](https://www.getpostman.com/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése

## <a name="download-files"></a>Fájlok letöltése

Az oktatóanyag forráskódja az [Azure-Samples/Azure-Search-Poster-Samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub-adattárban található [Custom-Analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) mappában található.

## <a name="1---create-azure-cognitive-search-service"></a>1 – Azure Cognitive Search szolgáltatás létrehozása

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure Cognitive Search szolgáltatásra, amelyet [a portálon hozhat létre](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet.

A következő lépésben ismernie kell a keresési szolgáltatás nevét és az API-kulcsát. Ha nem tudja, hogyan találja meg ezeket az elemeket, tekintse meg [ezt a](search-create-service-portal.md#get-a-key-and-url-endpoint)rövid útmutatót.


## <a name="2---set-up-postman"></a>2 – Poster beállítása

Ezután indítsa el a Poster-t, és importálja a letöltött gyűjteményt az [Azure-Samples/Azure-Search-Poster-Samples](https://github.com/Azure-Samples/azure-search-postman-samples)áruházból.

A gyűjtemény importálásához nyissa meg a **fájlok**  >  **importálása** elemet, majd válassza ki az importálni kívánt gyűjteményi fájlt.

Minden kérelem esetén a következőket kell tennie:

1. Cserélje le a `<YOUR-SEARCH-SERVICE>` elemet a keresési szolgáltatás nevére.

1. Cserélje le a `<YOUR-ADMIN-API-KEY>` kifejezést a keresési szolgáltatás elsődleges vagy másodlagos kulcsára.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Poster-kérelem URL-címe és fejléce" border="false":::

Ha nem ismeri a Poster-t, tekintse meg az [Azure Cognitive Search REST API-k megismerése](search-get-started-rest.md)című témakört.

## <a name="3---create-an-initial-index"></a>3 – kezdeti index létrehozása

Ebben a lépésben létrehozunk egy kezdeti indexet, betöltjük a dokumentumokat az indexbe, majd lekérdezjük a dokumentumokat, hogy meglássuk, hogyan végzik el a kezdeti kereséseket.

### <a name="create-index"></a>Index létrehozása

Először hozzon létre egy egyszerű indexet `tutorial-basic-index` , amelynek neve két mezőből áll: `id` és `phone_number` . Még nem definiáltak elemzőt, ezért a `standard.lucene` rendszer alapértelmezés szerint az analizátort fogja használni.

Az index létrehozásához a következő kérelmet küldjük:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Adatok betöltése

Ezután betöltünk egy adatsort az indexbe. Bizonyos esetekben előfordulhat, hogy nem szabályozhatja a betöltött telefonszámok formátumát, ezért a különböző típusú formátumoknál teszteljük a teszteket. Ideális esetben a keresési megoldás az összes egyező telefonszámot visszaküldi a formátumtól függetlenül.

A rendszer az alábbi kérelem használatával tölti be az adatindexbe az adatkészletet: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Az indexben lévő adattal készen áll a keresés megkezdésére.

### <a name="search"></a>Keresés

Ahhoz, hogy a keresés intuitív legyen, a legjobb, ha nem szeretné, hogy a felhasználók adott módon formázzuk a lekérdezéseket. A felhasználók a `(425) 555-0100` fentiekben megjelenő formátumok bármelyikében kereshetnek, és a rendszer a visszaadott eredményeket is elvárja. Ebben a lépésben egy pár minta lekérdezést fogunk kipróbálni, hogy lássuk, hogyan működnek.

Kezdjük a kereséssel `(425) 555-0100` :

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

A lekérdezés **négy várt eredményből három eredményt** ad vissza, de **két váratlan eredményt** is ad vissza:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Következő lépésként keressen egy számot formázás nélkül `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Ez a lekérdezés még rosszabb, csak **a négy helyes egyezés egyikét** adja vissza.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Ha ezeket az eredményeket zavarosnak találja, nem Ön egyedül. A következő szakaszban részletesen bemutatjuk, miért fogjuk lekérdezni ezeket az eredményeket.

## <a name="4---debug-search-results"></a>4 – keresési eredmények hibakeresése

A keresési eredmények megismeréséhez fontos, hogy megértse, hogyan működnek az elemzők. Innen az [elemzés szöveg API](/rest/api/searchservice/test-analyzer) használatával tesztelheti az alapértelmezett elemzőt, majd létrehozhatja az igényeinek megfelelő elemzőt.

### <a name="how-analyzers-work"></a>A elemzők működése

Az [elemző](search-analyzers.md) a [teljes szöveges keresőmotor](search-lucene-query-architecture.md) összetevője, amely a lekérdezési karakterláncokban és az indexelt dokumentumokban lévő szövegek feldolgozásához felelős. A különböző elemzők a forgatókönyvtől függően különböző módokon kezelhetik a szöveget. Ebben a forgatókönyvben a telefonszámokhoz igazított elemzőt kell létrehozni.

Az elemzők három összetevőből állnak:

+ A bemeneti szövegből eltávolított vagy lecserélt [**karakteres szűrők**](#CharFilters) .
+ Olyan [**Tokenizer**](#Tokenizers) , amely megszakítja a bemeneti szöveget a tokenekre, ami a keresési index kulcsai lesznek.
+ A tokenizer által létrehozott jogkivonatokat kezelő [**jogkivonat-szűrők**](#TokenFilters) .

Az alábbi ábrán láthatja, hogy a három összetevő hogyan működik együtt, hogy tokenize egy mondatot:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Elemzői folyamat diagramja tokenize":::

Ezeket a jogkivonatokat egy fordított index tárolja, amely lehetővé teszi a gyors, teljes szöveges kereséseket.  Egy fordított index lehetővé teszi a teljes szöveges keresést a lexikális analízis során kinyert összes egyedi kifejezés hozzárendelésével azokon a dokumentumokon, amelyekben azok bekövetkeznek. Az alábbi ábrán egy példa látható:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Példa invertált indexre":::

Az összes keresés megkeresi az invertált indexben tárolt feltételeket. Amikor egy felhasználó kiadja a lekérdezést:

1. A lekérdezés elemzésre kerül, és a lekérdezési feltételek elemzése megtörténik.
1. Ekkor a rendszer megkeresi a fordított indexet, és a megfelelő feltételekkel rendelkező dokumentumokat vizsgálja.
1. Végül a lekért dokumentumokat a [hasonlósági algoritmus](index-ranking-similarity.md)rangsorolja.

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Elemzői folyamat rangsorolási hasonlóságának ábrája":::

Ha a lekérdezési feltételek nem egyeznek meg a fordított index feltételeivel, az eredmények nem lesznek visszaadva. Ha többet szeretne megtudni a lekérdezések működéséről, tekintse meg a [teljes szöveges keresésről](search-lucene-query-architecture.md)szóló cikket.

> [!Note]
> A [részleges lejáratú lekérdezések](search-query-partial-matching.md) fontos kivételt képeznek a szabály alól. Ezek a lekérdezések (előtag-lekérdezés, helyettesítő karakteres lekérdezés, regex-lekérdezés) megkerülik a lexikális analízis folyamatát a reguláris kifejezéssel való lekérdezésekkel szemben. A részleges feltételek csak kisbetűvel vannak elfoglalva, mielőtt megegyeznek az indexben szereplő feltételekkel. Ha egy elemző nincs konfigurálva az ilyen típusú lekérdezések támogatására, gyakran váratlan eredményeket fog kapni, mert az indexben nem szerepelnek egyező feltételek.

### <a name="test-analyzer-using-the-analyze-text-api"></a>A test Analyzer használata az elemzés szövege API-val

Az Azure Cognitive Search egy [elemzés SZÖVEGES API](/rest/api/searchservice/test-analyzer) -t biztosít, amely lehetővé teszi az elemzők tesztelését a szöveg feldolgozásának megértéséhez.

Az elemzés szövege API a következő kérelem használatával hívható meg:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

Az API ezután visszaadja a szövegből kinyert jogkivonatok listáját. Láthatja, hogy a standard Lucene Analyzer három különálló tokenre osztja fel a telefonszámot:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Ezzel ellentétben a rendszer az `4255550100` írásjelek nélkül formázta a telefonszámot egyetlen jogkivonatba.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Ne feledje, hogy a lekérdezési kifejezések és az indexelt dokumentumok elemzése is megtörténik. Az előző lépésből származó keresési eredményekre való visszatéréshez megtudhatjuk, miért adtak vissza az eredményeket.

Az első lekérdezésben a helytelen telefonszámok lettek visszaadva, mert az egyik kifejezésük `555` megfelelt a keresett kifejezések egyikének. A második lekérdezésben csak az egy számot adta vissza a rendszer, mert az egyetlen olyan rekord, amelynek a kifejezése egyezést tartalmazott `4255550100` .

## <a name="5---build-a-custom-analyzer"></a>5 – egyéni elemző létrehozása

Most, hogy megértettük a megjelenő eredményeket, hozzunk létre egy egyéni elemzőt a jogkivonatok létrehozása logikájának fejlesztéséhez.

A cél az, hogy intuitív keresést biztosítson a telefonszámok között, függetlenül attól, hogy milyen formátumú a lekérdezés vagy az indexelt karakterlánc. Ennek az eredménynek a megadásához meg kell [adnia egy](#CharFilters) [tokenizer](#Tokenizers)és egy jogkivonat- [szűrőt](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Karakterek szűrése

A karakterláncok a szöveg feldolgozására szolgálnak, mielőtt a rendszer betáplálja azt a tokenizer. A karakteres szűrők gyakori felhasználási módjai közé tartozik a HTML-elemek kiszűrése vagy speciális karakterek cseréje.

Telefonszámok esetén el szeretné távolítani a szóközöket és a speciális karaktereket, mert nem minden telefonszám-formátum tartalmaz ugyanazokat a speciális karaktereket és szóközöket.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

A fenti szűrő eltávolítja `-` `(` `)` `+` `.` és szóközöket ad meg a bemenetből.

|Input (Bemenet)|Kimenet|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizers

A Tokenizers kioszthatja a jogkivonatokat, és elvetheti néhány karaktert, például az írásjeleket az út mentén. Sok esetben a jogkivonatok létrehozása célja, hogy egy mondatot egyedi szavakra osszanak fel.

Ebben a forgatókönyvben egy kulcsszó-tokenizer fogunk használni, `keyword_v2` mivel a telefonszámot egyetlen kifejezésként szeretnénk rögzíteni. Vegye figyelembe, hogy ez a probléma nem jelent megoldást. Lásd az alábbi [alternatív módszerek](#Alternate) szakaszt.

A kulcsszó tokenizers mindig ugyanazt a szöveget adja meg, amelyet egyetlen kifejezésként adott meg.

|Input (Bemenet)|Kimenet|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Jogkivonat-szűrők

A jogkivonat-szűrők kiszűrik vagy módosítják a tokenizer által generált jogkivonatokat. A jogkivonat-szűrők egyik gyakori használata az összes karakter kisbetűs jogkivonat-szűrővel való használata. Egy másik gyakori használat a indexelendő kiszűrése, például,, `the` `and` vagy `is` .

Habár ezen szűrők egyikét sem kell használnia ehhez a forgatókönyvhöz, egy nGram-jogkivonat-szűrőt fogunk használni, amely lehetővé teszi a telefonszámok részleges keresését.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

Az [nGram_v2 jogkivonat szűrő](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) a tokeneket a és paraméter alapján adott méretű n-grammra osztja szét `minGram` `maxGram` .

A Phone Analyzer esetében úgy van beállítva, hogy `minGram` `3` a legrövidebb alsztringet várjuk a felhasználóknak a kereséshez. `maxGram` úgy van beállítva, hogy `20` biztosítsa, hogy az összes telefonszám, még a bővítmények is csak egyetlen n-grammba illeszkedjenek.

 Az n-gramm szerencsétlen mellékhatása az, hogy a rendszer hamis pozitív értékeket ad vissza. Ezt a 7. lépésben javítjuk, ha kiépítünk egy különálló elemzőt az n-Gram-jogkivonat szűrőjét nem tartalmazó keresésekhez.

|Input (Bemenet)|Kimenet|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analyzer

A tokenizer, valamint a jogkivonat-szűrők a helyén már készen állnak az analizátor meghatározására.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Input (Bemenet)|Kimenet|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Figyelje meg, hogy a kimenetben szereplő bármelyik jogkivonat kereshető. Ha a lekérdezés tartalmazza a jogkivonatok bármelyikét, a rendszer visszaadja a telefonszámot.

Az egyéni analizátor definiálása után hozza létre újra az indexet, hogy az egyéni elemző elérhető legyen a következő lépésben való teszteléshez. Az egyszerűség kedvéért a Poster-gyűjtemény létrehoz egy nevű új indexet `tutorial-first-analyzer` az általunk definiált elemzővel.

## <a name="6---test-the-custom-analyzer"></a>6 – az egyéni elemző tesztelése

Az index létrehozása után mostantól tesztelheti a létrehozott elemzőt a következő kérelem használatával:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Ekkor megtekintheti a telefonszám alapján létrejövő jogkivonatok gyűjteményét:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 – egyéni elemző létrehozása lekérdezésekhez

Miután néhány minta lekérdezést végzett az indextel az egyéni elemzővel, megtudhatja, hogy a visszahívás javult, és az összes hozzá tartozó telefonszámot visszaadja. Az n-Gram-token szűrő azonban bizonyos hamis pozitív értékeket is eredményez. Ez egy n-gramos jogkivonat-szűrő gyakori mellékhatása.

A téves riasztások elkerülése érdekében hozzon létre egy külön elemzőt a lekérdezéshez. Ez az elemző ugyanaz lesz, mint a már létrehozott Analyzer, de **nem** `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

Az index definíciójában a és a is megadható `indexAnalyzer` `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Ezzel a módosítással minden be van állítva. Hozza létre újra az indexet, indexelje az adatforrást, majd tesztelje újra a lekérdezéseket, hogy a keresés a várt módon működjön. Ha a Poster-gyűjteményt használja, a rendszer létrehoz egy nevű harmadik indexet `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternatív megoldások

A fenti Analyzer úgy lett kialakítva, hogy maximalizálja a keresés rugalmasságát. Így azonban az indexben számos potenciálisan lényegtelen kifejezés tárolására kerül.

Az alábbi példa egy másik elemzőt mutat be, amely a feladathoz is használható. 

Az analizátor jól működik, kivéve az olyan bemeneti adatokat, `14255550100` amelyek megnehezítik a telefonszám logikai kitöltését. Például az analizátor nem tudja elkülöníteni az országkód, a `1` körzetszámtól a `425` . Ez az eltérés azt eredményezi, hogy a fenti számot a rendszer nem adja vissza, ha a felhasználó nem tartalmaz országkódot a keresésben.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Az alábbi példában láthatja, hogy a telefonszám felosztva van az olyan adattömbökre, amelyeket általában a felhasználónak kell megkeresnie.

|Input (Bemenet)|Kimenet|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

A követelményektől függően ez hatékonyabb megoldást jelenthet a problémára.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

Az egyszerűség kedvéért ez az oktatóanyag három új indexet hozott létre. Azonban gyakori a fejlesztés korai szakaszaiban az indexek törlése és újbóli létrehozása. Törölhet egy indexet a Azure Portalban, vagy a következő API-hívást használhatja:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag az egyéni elemzők létrehozásának és tesztelésének folyamatát mutatja be. Létrehozott egy indexet, indexelt információt, majd lekérdezte az indexet, hogy láthassa, milyen keresési eredményeket adott vissza. Innen a szöveg elemzése API-t használta a lexikális elemzési folyamat működés közbeni megtekintéséhez.

Míg az oktatóanyagban definiált elemző egyszerű megoldást kínál a telefonszámok keresésére, ezzel a folyamattal bármely lehetséges forgatókönyvhöz létrehozhat egy egyéni telefon-elemzőt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes eltávolítani azokat az erőforrásokat, amelyekre már nincs szüksége a projekt végén. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre egyéni elemzőt, vessünk egy pillantást az összes olyan szűrőre, tokenizers és elemzőre, amely elérhetővé teszi a gazdag keresési élményt.

> [!div class="nextstepaction"]
> [Egyéni elemzők az Azure-ban Cognitive Search](index-add-custom-analyzers.md)