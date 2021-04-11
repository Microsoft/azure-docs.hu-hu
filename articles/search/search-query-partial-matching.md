---
title: Részleges feltételek, mintázatok és speciális karakterek
titleSuffix: Azure Cognitive Search
description: Használjon helyettesítő karaktereket, regexeket és előtag-lekérdezéseket az Azure Cognitive Search lekérdezési kérelemben szereplő teljes vagy részleges feltételek egyeztetéséhez. A speciális karaktereket tartalmazó, nehezen egyeztethető minták teljes lekérdezési szintaxissal és egyéni elemzővel oldhatók fel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584176"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Részleges kifejezéses keresés és minták speciális karakterekkel (kötőjelek, helyettesítő karakterek, regex, mintázatok)

A *részleges kifejezéses keresés* olyan kifejezésekre hivatkozik, amelyek a kifejezés töredékeit foglalják magukban, ahol a teljes kifejezés helyett csak a kezdő, a középső vagy a záró kifejezés (más néven előtag, Infix vagy utótag típusú lekérdezések) lehet. A részleges kifejezéses keresés tartalmazhat töredékek kombinációját is, amelyek gyakran speciális karakterekkel, például kötőjelekkel, kötőjelekkel vagy perjelekkel rendelkeznek, amelyek a lekérdezési karakterlánc részét képezik. Gyakori használati esetek közé tartoznak a telefonszámok, URL-címek, kódok vagy elválasztott összetett szavak részei.

A különleges karaktereket tartalmazó részleges kifejezéses keresési és lekérdezési karakterláncok lehetnek problematikusak, ha az index nem rendelkezik a várt formátumú jogkivonatokkal. Az indexelés [lexikális elemzési fázisában](search-lucene-query-architecture.md#stage-2-lexical-analysis) (feltéve, hogy az alapértelmezett szabványos elemző) a speciális karaktereket elveti, az összetett szavakat feldarabolják, és törlik a szóközt. az összes művelet sikertelen lehet, ha nem található egyezés. Például egy telefonszám (például:,, `+1 (425) 703-6214` `"1"` `"425"` `"703"` , `"6214"` ) nem fog megjelenni a `"3-62"` lekérdezésben, mert a tartalom valójában nem létezik az indexben. 

A megoldás egy elemző meghívása az indexelés során, amely megőrzi a teljes karakterláncot, beleértve a szóközöket és a speciális karaktereket, ha szükséges, így a szóközök és a karakterek is megadhatók a lekérdezési karakterláncban. Hasonlóképpen, ha egy teljes sztring, amely nem a kisebb részekre van társítva, lehetővé teszi a "Start with" vagy a "végződik" lekérdezésekkel való egyezést, ahol az Ön által megadott minta olyan kifejezésre értékelhető ki, amelyet nem a lexikális analízis végez. Egy érintetlen sztring további mezőjének létrehozása, valamint a teljes értékű jogkivonatokat kibocsátó Content-reserving Analyzer használata a megoldás mindkét mintához, valamint a speciális karaktereket tartalmazó lekérdezési karakterláncok egyeztetéséhez.

> [!TIP]
> Ha már ismeri a Poster és a REST API-kat, [töltse le a lekérdezési példák gyűjteményét](https://github.com/Azure-Samples/azure-search-postman-samples/) a jelen cikkben ismertetett részleges feltételek és speciális karakterek lekérdezéséhez.

## <a name="about-partial-term-search"></a>A részleges kifejezés keresésének ismertetése

Az Azure Cognitive Search megkeresi a teljes jogkivonat-előírásokat az indexben, és nem talál egyezést részleges kifejezéssel, kivéve, ha helyettesítő helyőrző operátorokat ( `*` és) is tartalmaz `?` , vagy a lekérdezést reguláris kifejezésként formázza. A részleges feltételek a következő módszerekkel adhatók meg:

+ A [reguláris kifejezések lekérdezése](query-lucene-syntax.md#bkmk_regex) lehet bármilyen reguláris kifejezés, amely az Apache Lucene alatt érvényes. 

+ Az [előtaggal egyező helyettesítő karakteres operátorok](query-simple-syntax.md#prefix-search) olyan általánosan felismert mintázatra utalnak, amely egy kifejezés kezdetét, majd a (z `*` ) vagy az `?` utótag operátorait követi, például `search=cap*` a "kapitány Jack Waterfront Inn" vagy "Gacc Capital" karaktert. Az előrögzítés egyeztetése az egyszerű és a teljes Lucene lekérdezési szintaxisban is támogatott.

+ A [Infix és utótag-megfeleltetésű helyettesítő karakter](query-lucene-syntax.md#bkmk_wildcard) a `*` `?` kifejezés elején vagy végén elhelyezi a és operátorokat, és reguláris kifejezési szintaxist igényel (ahol a kifejezés továbbítási perjeltel van ellátva). A lekérdezési karakterlánc () például `search=/.*numeric*./` "alfanumerikus" és "alfanumerikus" értékkel tér vissza az eredményeket utótagként és Infix.

A részleges vagy a minta kereséshez, valamint néhány más lekérdezési űrlaphoz, például a zavaros kereséshez, a rendszer nem használja az elemzőket a lekérdezés időpontjában. Ezekhez a lekérdezési űrlapokhoz, amelyeket az elemző észlel a kezelők és elválasztók jelenlétében, a rendszer a lekérdezési karakterláncot lexikális elemzés nélkül továbbítja a motornak. A lekérdezési űrlapok esetében a rendszer figyelmen kívül hagyja a mezőben megadott elemzőt.

> [!NOTE]
> Ha egy részleges lekérdezési karakterlánc olyan karaktereket tartalmaz, mint például a perjel egy URL-töredékben, akkor lehet, hogy hozzá kell adnia a Escape-karaktereket. A JSON-ban a továbbítási perjelek egy `/` visszafelé perjelet eredményeznek `\` . Ennek megfelelően a `search=/.*microsoft.com\/azure\/.*/` "Microsoft.com/Azure/" URL-töredék szintaxisa.

## <a name="solving-partialpattern-search-problems"></a>Részleges/minta keresési problémák megoldása

Ha töredékekre vagy mintázatokra vagy speciális karakterekre kell keresnie, felülbírálhatja az alapértelmezett elemzőt egy olyan egyéni elemzővel, amely egyszerűbb jogkivonatok létrehozása-szabályok alatt működik, és megőrzi a teljes karakterláncot az indexben. A lépés a következőképpen néz ki:

1. Definiáljon egy mezőt, amely a sztring érintetlen verzióját tárolja (feltéve, hogy a lekérdezési időpontban elemezni és nem elemzett szöveget kíván használni)
1. Értékelje ki és válassza ki azokat a különböző elemzőket, amelyek a megfelelő részletességű tokeneket bocsátanak ki.
1. Az analizátor kiosztása a mezőhöz
1. Az index létrehozása és tesztelése

> [!TIP]
> Az elemzők kiértékelése olyan iterációs folyamat, amely gyakori index-újraépítést igényel. Ezt a lépést a Poster, a REST API-k [létrehozása](/rest/api/searchservice/create-index), az indexek [törlése](/rest/api/searchservice/delete-index), a[dokumentumok betöltése](/rest/api/searchservice/addupdate-or-delete-documents)és a dokumentumok [keresése](/rest/api/searchservice/search-documents)során egyszerűbbé teheti. A betöltési dokumentumok esetében a kérelem törzsének tartalmaznia kell egy kis reprezentatív adatkészletet, amelyet szeretne tesztelni (például egy telefonszámot vagy Termékkód számot tartalmazó mező). Ha ezekkel az API-kkal ugyanabban a Poster-gyűjteményben vannak, gyorsan elvégezheti ezeket a lépéseket.

## <a name="1---create-a-dedicated-field"></a>1 – dedikált mező létrehozása

Az elemzők határozzák meg, hogy a feltételek hogyan legyenek jogkivonatban egy indexben. Mivel az elemzők egy mező alapján vannak kiosztva, az indexben mezőket hozhat létre a különböző forgatókönyvek optimalizálásához. Megadhatja például a "featureCode" és a "featureCodeRegex" karakterláncot, hogy támogassa a normál teljes szöveges keresést az első, és a speciális mintázatot a másodikban. Az egyes mezőkhöz rendelt elemzők határozzák meg, hogy az egyes mezők tartalma hogyan legyen jogkivonatban az indexben.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2 – analizátor beállítása

Ha olyan elemzőt választ ki, amely teljes körű jogkivonatokat állít elő, a következő elemzők gyakoriak:

| Analyzer | Viselkedés |
|----------|-----------|
| [nyelvi elemzők](index-add-language-analyzers.md) | A megőrzi a kötőjeleket összetett szavakban vagy sztringekben, magánhangzó-mutációkban és művelet-űrlapokban. Ha a lekérdezési minták kötőjeleket tartalmaznak, a nyelvi elemző használata elegendő lehet. |
| [kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | A teljes mező tartalma egyetlen kifejezésként van jogkivonat. |
| [szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Csak szóközöket választ ki. A kötőjeleket vagy más karaktereket tartalmazó kifejezéseket egyetlen jogkivonatként kezeli a rendszer. |
| [Egyéni analizátor](index-add-custom-analyzers.md) | ajánlott Az egyéni elemző létrehozása lehetővé teszi a tokenizer és a jogkivonat-szűrő megadását is. A korábbi elemzőket a következőképpen kell használni:. Az egyéni elemző lehetővé teszi, hogy kiválassza a használni kívánt tokenizers és jogkivonat-szűrőket. <br><br>Az ajánlott kombináció a [tokenizer kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) , amely egy [kisbetűs jogkivonat-szűrővel](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)rendelkezik. Önmagában a beépített [kulcsszó-elemző](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nem csökkenti a nagybetűket, így a lekérdezések sikertelenek lehetnek. Az egyéni elemző egy mechanizmust biztosít az alsó szintű jogkivonat-szűrő hozzáadásához. |

Ha webes API-teszt eszközt (például Poster) használ, hozzáadhatja a [test Analyzer-Rest-hívást](/rest/api/searchservice/test-analyzer) a jogkivonat-kimenet vizsgálatához.

A használatához fel kell töltenie egy feltöltött indexet. A meglévő indexek és a kötőjeleket vagy a részleges kifejezéseket tartalmazó mezők esetében különböző elemzőket lehet kipróbálni a megadott feltételek alapján, hogy megtudja, milyen jogkivonatok vannak kibocsátva.  

1. Először tekintse meg a standard Analyzert, és tekintse meg, hogy az alapértelmezett érték a jogkivonatok tokenje.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Értékelje ki a választ, és tekintse meg, hogy a szöveg hogyan legyen jogkivonatban az indexen belül. Figyelje meg, hogy az egyes kifejezések kisebb méretűek, kötőjelek eltávolítva és alsztringek az egyes jogkivonatokban. Az eredményekben csak azok a lekérdezések lesznek visszaadva, amelyek megfelelnek ezen jogkivonatoknak. A "10 – NOR" kifejezést tartalmazó lekérdezések sikertelenek lesznek.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Most módosítsa a (z) vagy az Analyzer használatára vonatkozó kérelmet `whitespace` `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. A válasz mostantól egyetlen tokenből áll, amely a karakterlánc részeként megőrzött kötőjelekkel van ellátva. Ha a minta vagy a részleges kifejezés, például a "10 – nem" kifejezést kell keresnie, a lekérdezési motor mostantól a találatok keresésének alapját képezi.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Ügyeljen arra, hogy a lekérdezés-elemzők a lekérdezési fa létrehozásakor gyakran alacsonyabb eseti kifejezéseket adjanak a keresési kifejezésekben. Ha olyan elemzőt használ, amely nem a kis-és nagybetűket jeleníti meg az indexelés során, és nem várt eredményeket kap, ez az oka lehet. A megoldás egy kisméretű jogkivonat-szűrő hozzáadása az alábbi, "egyéni elemzők használata" című szakaszban leírtak szerint.

## <a name="3---configure-an-analyzer"></a>3 – analizátor konfigurálása
 
Függetlenül attól, hogy az elemzőket kiértékeli, vagy egy adott konfigurációval halad előre, meg kell adnia az analizátort a mező definíciójában, és saját maga is konfigurálnia kell, ha nem beépített elemzőt használ. Az elemzők cseréjekor általában újra kell építeni az indexet (eldobás, újbóli létrehozás és újratöltés). 

### <a name="use-built-in-analyzers"></a>Beépített elemzők használata

A beépített elemzők név alapján adhatók meg `analyzer` egy mező definíciójának tulajdonságában, és az indexben nem szükséges további konfiguráció. Az alábbi példa bemutatja, hogyan állíthatja be az `whitespace` elemzőt egy mezőre. 

További forgatókönyvek és további információ a beépített elemzők használatáról: [beépített elemzők](./index-add-custom-analyzers.md#built-in-analyzers). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Egyéni elemzők használata

Ha [Egyéni elemzőt](index-add-custom-analyzers.md)használ, definiálja az indexben a tokenizer felhasználó által definiált kombinációjában, amely a lehetséges konfigurációs beállításokkal rendelkezik. Ezután hivatkozzon egy mező-definícióra, ugyanúgy, mint a beépített elemző.

Ha a cél a teljes távú jogkivonatok létrehozása, a **kulcsszó tokenizer** és **kisbetűs jogkivonat-szűrőből** álló egyéni analizátor ajánlott.

+ A tokenizer kulcsszó egyetlen jogkivonatot hoz létre egy mező teljes tartalmához.
+ A kisbetűs jogkivonat szűrője a nagybetűket kis méretű szöveggé alakítja át. A lekérdezés-elemzők általában kisbetűsek a nagybetűs szöveg bemenetei. Az alsó szintű ház homogenizálja a bemeneteket a jogkivonatos feltételekkel.

Az alábbi példa egy egyéni elemzőt mutat be, amely a kulcsszó tokenizer és egy kisbetűs jogkivonat-szűrőt biztosít.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> A `keyword_v2` rendszer a tokenizer és a `lowercase` jogkivonat-szűrőt ismeri a rendszeren, és az alapértelmezett konfigurációját használja, ezért a név alapján hivatkozhat rájuk, anélkül, hogy először kellene megadnia őket.

## <a name="4---build-and-test"></a>4 – létrehozás és tesztelés

Miután meghatározta a forgatókönyvét támogató elemzőket és mezőértékeket tartalmazó indexet, betöltheti a reprezentatív karakterláncokat tartalmazó dokumentumokat, hogy tesztelje a részleges karakterlánc-lekérdezéseket. 

Az előző szakaszban a logikát ismertetjük. Ez a szakasz végigvezeti az egyes API-k lépésein, amelyeket a megoldás tesztelésekor kell meghívnia. Ahogy korábban már említettük, ha interaktív webes tesztelési eszközt (például Poster) használ, gyorsan elvégezheti ezeket a feladatokat.

+ Az [index törlése](/rest/api/searchservice/delete-index) eltávolítja az azonos nevű meglévő indexet, hogy újra létre lehessen hozni.

+ A [create index](/rest/api/searchservice/create-index) létrehozza az index struktúrát a keresési szolgáltatásban, beleértve az analizátor-definíciókat és a Analyzer-specifikációval rendelkező mezőket.

+ A [betöltési dokumentumok](/rest/api/searchservice/addupdate-or-delete-documents) a dokumentumokat a tárgymutatóval megegyező struktúrával, valamint kereshető tartalommal importálják. A lépés után az index készen áll a lekérdezésre vagy a tesztelésre.

+ A [test Analyzer](/rest/api/searchservice/test-analyzer) az [analizátor beállítása](#set-an-analyzer)során lett bevezetve. Tesztelje az index egyes karakterláncait különböző elemzők használatával, hogy megtudja, hogyan történik a feltételek tokenje.

+ A [dokumentumok keresése](/rest/api/searchservice/search-documents) során megtudhatja, hogyan hozhat létre egy lekérdezési kérést [egyszerű szintaxissal](query-simple-syntax.md) vagy [teljes Lucene szintaxissal](query-lucene-syntax.md) helyettesítő karakterekkel és reguláris kifejezésekkel.

  A részleges lejáratú lekérdezéseknél, például a "3-6214" lekérdezésben a "+ 1 (425) 703-6214" kifejezésre való kereséshez használhatja az egyszerű szintaxist: `search=3-6214&queryType=simple` .

  A Infix és az utótag lekérdezéséhez, például a "NUM" vagy a "numerikus" lekérdezéshez az "alfanumerikus" kifejezéshez használja a teljes Lucene szintaxist és egy reguláris kifejezést: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Lekérdezési teljesítmény hangolása

Ha a keyword_v2 tokenizer és a kisbetűs jogkivonat-szűrőt tartalmazó ajánlott konfigurációt alkalmazza, a lekérdezés teljesítményének csökkenése miatt előfordulhat, hogy az indexben már meglévő jogkivonatok esetében a további jogkivonat-szűrő feldolgozása történik. 

Az alábbi példa egy [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) hozzáadásával teszi lehetővé, hogy az előtag gyorsabban illeszkedjen. További tokenek jönnek létre a 2-25 karakteres kombinációkban, amelyek karaktereket tartalmaznak: (nem csak MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

Az is elképzelhető, hogy a további jogkivonatok létrehozása nagyobb indexet eredményeznek. Ha elegendő kapacitása van a nagyobb index elfogadásához, akkor ez a megközelítés a gyorsabb válaszidő jobb megoldás lehet.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan járulnak hozzá az elemzők a problémák lekérdezéséhez és a lekérdezési problémák megoldásához. A következő lépésként tekintse meg az analizátorra gyakorolt hatást az indexeléssel és a lekérdezések feldolgozásával kapcsolatban. Különösen érdemes lehet a Retext API-t használni a jogkivonat-kimenet visszaküldéséhez, hogy pontosan lássuk, mit hoz létre az elemző az indexhez.

+ [Nyelvi elemzők](search-language-support.md)
+ [Az Azure Cognitive Searchban való szövegszerkesztés elemzői](search-analyzers.md)
+ [Szöveges API elemzése (REST)](/rest/api/searchservice/test-analyzer)
+ [A teljes szöveges keresés működése (lekérdezési architektúra)](search-lucene-query-architecture.md)