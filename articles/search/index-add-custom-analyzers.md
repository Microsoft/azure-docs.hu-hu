---
title: Egyéni elemzők hozzáadása karakterlánc-mezőkhöz
titleSuffix: Azure Cognitive Search
description: A szöveges tokenizers és a karakteres szűrők konfigurálásával szöveges elemzéseket végezhet a karakterláncokon az indexelés és a lekérdezések során.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604440"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Egyéni elemzők hozzáadása karakterlánc-mezőkhöz Azure Cognitive Search indexben

Az *Egyéni elemző* a tokenizer, egy vagy több jogkivonat-szűrő, valamint egy vagy több, a keresési indexben definiált karakteres szűrő, majd az egyéni elemzést igénylő mező-definíciók hivatkozása. A tokenizer feladata a szövegek jogkivonatokban való tördelése, valamint a tokenizer által kibocsátott jogkivonatok módosítására szolgáló jogkivonat-szűrők használata. A tokenizer feldolgozása előtt a karakteres szűrők előkészítik a bemeneti szöveget. 

Az egyéni elemző lehetővé teszi a szövegek indexelhető és kereshető jogkivonatokra való konvertálásának folyamatát, mivel lehetővé teszi, hogy kiválassza a meghívni kívánt elemzési vagy szűrési típusokat, valamint azt, hogy a sorrend milyen sorrendben történjen. Ha egyéni beállításokkal rendelkező beépített elemzőt szeretne használni, például a standard szintű maxTokenLength módosítását, akkor a beállítások megadásához hozzon létre egy egyéni elemzőt a felhasználó által definiált névvel.

Olyan helyzetekben, ahol az egyéni elemzők hasznosak lehetnek:

- A karakterkészletek használatával a HTML-jelöléseket a szöveges bemenetek tokenbe helyezése előtt, vagy bizonyos karakterek vagy szimbólumok cseréjével távolíthatja el.

- Fonetikus keresés. Egy fonetikus szűrő hozzáadásával engedélyezheti a keresést, hogy a szavak hogyan szólalnak meg, és nem a helyesírást.  

- A lexikális analízis letiltása. A Kulcsszóválasztó Analyzer használatával olyan kereshető mezőket hozhat létre, amelyek nincsenek elemezve.  

- Gyors előtag-/utótag-keresés. Adja hozzá az Edge N-Gram token szűrőt a szavak előtagjainak indexeléséhez a gyors előtag-egyeztetés engedélyezéséhez. Kombinálja a fordított jogkivonat-szűrővel az utótagok egyeztetéséhez.  

- Egyéni jogkivonatok létrehozása. Például a szóköz tokenizer használatával a mondatokat kiválasztó karakterként kell megtörni a tokenekre  

- ASCII-összecsukható. Adja hozzá a standard ASCII összecsukható szűrőt a Mellékjelek, például ö vagy ê kifejezésekhez való normalizálása érdekében.  

Egyéni elemző létrehozásához adja meg a tervezéskor egy index "elemzők" szakaszában, majd hivatkozzon rá a kereshető, EDM. String mezőkre az "Analyzer" tulajdonság vagy a "indexAnalyzer" és a "searchAnalyzer" párok használatával.

> [!NOTE]  
> A létrehozott egyéni elemzők nincsenek kitéve a Azure Portal. Egyéni elemző hozzáadásának egyetlen módja a kód, amely meghatározza az indexet. 

## <a name="create-a-custom-analyzer"></a>Egyéni elemző létrehozása

Az analizátor definíciója tartalmaz egy nevet, egy típust, egy vagy több karaktert, legfeljebb egy tokenizer, valamint egy vagy több jogkivonat-szűrőt a jogkivonatok létrehozása utáni feldolgozáshoz. A jogkivonatok létrehozása előtt a rendszer alkalmazza a karakteres szűrőket. A jogkivonat-szűrők és a karakterstílus-szűrők balról jobbra vannak alkalmazva.

- Az egyéni analizátorokban lévő névnek egyedinek kell lennie, és nem egyezhet meg a beépített elemzők, a tokenizers, a jogkivonat-szűrők és a karakterek szűrőinek egyikével sem. Csak betűket, számokat, szóközöket, kötőjeleket vagy aláhúzásokat tartalmazhat, csak alfanumerikus karakterrel kezdődhet és végződhet, és legfeljebb 128 karakter hosszú lehet. 

- A típusnak #Microsoft. Azure. Search. CustomAnalyzer kell lennie.

- a "charFilters" lehet egy vagy több [szűrő a jogkivonatok létrehozása](#CharFilter)előtt feldolgozott szűrőkből a megadott sorrendben. Néhány karakteres szűrő rendelkezik beállításokkal, amelyek a "charFilter" szakaszban állíthatók be. A karakteres szűrők megadása nem kötelező.

- a "tokenizer" pontosan egy [tokenizer](#tokenizers). Meg kell adni egy értéket. Ha egynél több tokenizer van szüksége, több egyéni elemzőt is létrehozhat, és kioszthatja őket egy mező – mező alapján az index sémában.

- a "tokenFilters" lehet egy vagy több, a jogkivonatok létrehozása után feldolgozott [jogkivonat-szűrőből](#TokenFilters)származó szűrő a megadott sorrendben. A beállításokkal rendelkező jogkivonat-szűrők esetében adjon hozzá egy "tokenFilter" szakaszt a konfiguráció megadásához. A jogkivonat-szűrők megadása nem kötelező.

Az elemzők nem hozhatnak létre 300 karakternél hosszabb tokeneket, vagy az indexelés sikertelen lesz. A hosszú tokenek kivágásához vagy a kizáráshoz használja a **TruncateTokenFilter** és a **LengthTokenFilter** . Lásd: [**jogkivonat-szűrők**](#TokenFilters) .

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Az index definíciójában ezt a szakaszt bárhol elhelyezheti egy Create index-kérelem törzsében, de általában a végére kerül:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Az analizátor definíciója a nagyobb index részét képezi. A char szűrők, a tokenizers és a jogkivonat-szűrők definíciói csak akkor lesznek hozzáadva az indexhez, ha egyéni beállításokat állít be. Ha meglévő szűrőt vagy tokenizer szeretne használni, adja meg azt név szerint az elemző definíciójában. További információ: [create index (REST)](/rest/api/searchservice/create-index). További Példákért lásd: [elemzők hozzáadása az Azure Cognitive Searchban](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Egyéni elemzők tesztelése

A [test Analyzer (REST)](/rest/api/searchservice/test-analyzer) használatával megtekintheti, hogy az elemző hogyan szakítja meg az adott szöveget a jogkivonatokban.

**Kérés**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Válasz**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Egyéni elemzők frissítése

Az analizátor, a tokenizer, a jogkivonat-szűrő vagy a karakteres szűrő meghatározása után nem módosítható. Az új eszközök csak akkor adhatók hozzá meglévő indexekhez, ha a `allowIndexDowntime` jelző értéke TRUE (igaz) az index frissítési kérelmében:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

A művelet legalább néhány másodpercen belül offline állapotba helyezi az indexet, így az indexelés és a lekérdezési kérelmek sikertelenek lesznek. Az index teljesítményének és írásának rendelkezésre állása több percig is csökkenthető az index frissítése után, vagy hosszabb a nagyon nagy indexek esetében, de ezek a hatások ideiglenesek, és végül a saját maguk is megoldhatók.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Beépített elemzők

Ha egyéni beállításokkal rendelkező beépített elemzőt szeretne használni, hozzon létre egy egyéni elemzőt az a mechanizmus, amellyel ezeket a beállításokat megadja. Ezzel szemben, ha beépített elemzőt használ, a mező definíciójában egyszerűen [hivatkoznia kell rá](search-analyzers.md#how-to-specify-analyzers) .

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Leírás és beállítások**|  
|-----------------|-------------------------------|---------------------------|  
|[kulcsszó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |Egy mező teljes tartalmát egyetlen tokenként kezeli. Ez hasznos lehet például a zip-kódok, az azonosítók és a termékek neveihez.|  
|[minta](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|A rugalmasan elkülöníti a szöveget egy reguláris kifejezési minta használatával. </br></br>**Beállítások** </br></br>kisbetűs (típus: bool) – meghatározza, hogy a feltételek kisbetűsek-e. Az alapértelmezett érték a True (igaz). </br></br>[minta](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (típus: karakterlánc) – a jogkivonat-elválasztókkal egyeztetendő reguláris kifejezési minta. Az alapértelmezett érték a `\W+` , amely a nem a Word karaktereknek felel meg. </br></br>[Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: string) – reguláris kifejezés jelzői. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ, CASE_INSENSITIVE, Megjegyzés, DOTALL, LITERÁL, többsoros, UNICODE_CASE, UNIX_LINES </br></br>indexelendő (típus: String Array) – A indexelendő listája. Az alapértelmezett érték egy üres lista.|  
|[egyszerű](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |A szöveget nem betűkre osztja, és a szövegeket kisbetűvé alakítja. |  
|[Standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Más néven standard. Lucene)|StandardAnalyzer|Standard Lucene Analyzer, amely a szabványos tokenizer, a kisbetűk és a leállítási szűrőből áll. </br></br>**Beállítások** </br></br>maxTokenLength (típus: int) – a jogkivonat maximális hossza. Az alapértelmezett érték a 255. A maximális hossznál hosszabb tokenek vannak felosztva. A token maximális hossza 300 karakter lehet. </br></br>indexelendő (típus: String Array) – A indexelendő listája. Az alapértelmezett érték egy üres lista.|  
|standardasciifolding. Lucene|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |Standard analizátor ASCII-összecsukható szűrővel. |  
|[állj](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|A szöveget nem betűkre osztja, a kisbetűs és a nem indexelendő szót jogkivonat-szűrőket alkalmazza. </br></br>**Beállítások** </br></br>indexelendő (típus: String Array) – A indexelendő listája. Az alapértelmezett érték az angol nyelv előre definiált listája. |  
|[szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |A szóköz tokenizer használó elemző. A 255 karakternél hosszabb tokenek szét vannak osztva.|  

 <sup>1</sup> az analizátor típusa mindig előtaggal van ellátva a "#Microsoft. Azure. Search" kóddal, így a "PatternAnalyzer" kifejezés valójában "#Microsoft. Azure. Search. PatternAnalyzer" néven adható meg. Az előtagot eltávolítottuk a rövidség kedvéért, de az előtagot kötelező megadni a kódban.

A analyzer_type csak a testreszabható elemzők számára érhető el. Ha nincsenek lehetőségek, például a Kulcsszóválasztó Analyzer esetében, nincs társítva #Microsoft. Azure. Search típus.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Karakterek szűrése

Az alábbi táblázatban az Apache Lucene használatával megvalósított karakterkészletek a Lucene API dokumentációhoz kapcsolódnak.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Leírás és beállítások**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Egy char-szűrő, amely megkísérli kiszűrni a HTML-szerkezeteket.|  
|[leképezés](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Egy char-szűrő, amely a leképezések beállítással definiált leképezéseket alkalmazza. A megfeleltetés a kapzsi (a leghosszabb minta egyeztetése egy adott pont WINS esetében). A csere értéke lehet üres karakterlánc.  </br></br>**Beállítások**  </br></br> leképezések (típus: karakterlánc tömb) – a következő formátumú leképezések listája: "a =>b" (az "a" karakter összes előfordulását a "b" karakter váltja fel). Kötelező.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Egy char-szűrő, amely a bemeneti karakterláncban szereplő karaktereket váltja fel. Reguláris kifejezéssel azonosítja a megőrizni kívánt karakterkészleteket, valamint egy helyettesítő mintát a cserélni kívánt karakterek azonosítására. Például a következő bemeneti szöveg = "AA bb AA bb", Pattern = "(AA) \\ \s + (bb)" Replacement = "$ 1 # $2", result = "AA # bb AA # bb".  </br></br>**Beállítások**  </br></br>minta (típus: karakterlánc) – kötelező.  </br></br>csere (típus: karakterlánc) – kötelező.|  

 <sup>1</sup> a karakteres szűrőtípus mindig előtaggal van ellátva a "#Microsoft. Azure. Search" kóddal, így a "MappingCharFilter" kifejezés valójában "#Microsoft. Azure. Search. MappingCharFilter. Eltávolította az előtagot a táblázat szélességének csökkentése érdekében, de ne feledje, hogy belefoglalja a kódot. Figyelje meg, hogy char_filter_type csak a testreszabható szűrőkhöz adható meg. Ha nincsenek beállítások, mint a html_strip esetében, nincs társított #Microsoft. Azure. Search típus.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizers

A tokenizer a folytonos szöveget a tokenek sorrendjébe osztja, például egy mondat szövegbe való tördelését. Az alábbi táblázatban az Apache Lucene használatával megvalósított tokenizers az Lucene API dokumentációhoz kapcsolódnak.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Leírás és beállítások**|  
|------------------|-------------------------------|---------------------------|  
|[klasszikus](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Nyelvtanon alapuló tokenizer, amely a legtöbb európai nyelvi dokumentum feldolgozására alkalmas.  </br></br>**Beállítások**  </br></br>maxTokenLength (típus: int) – a jogkivonat maximális hossza. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|A Tokenizes egy szélétől az adott méret (ek) n-grammba való bevitelét adja meg.  </br></br> **Beállítások**  </br></br>minGram (típus: int) – alapértelmezett: 1, maximum: 300.  </br></br>maxGram (típus: int) – alapértelmezett: 2, maximum: 300. Nagyobbnak kell lennie, mint minGram.  </br></br>tokenChars (típus: String Array) – a tokenekben megőrizni kívánt karakteres osztályok. Megengedett értékek: </br>"Letter", "Digit", "szóköz", "központozás", "szimbólum". Az alapértelmezett érték egy üres tömb – megtartja az összes karaktert. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|A teljes bemenet egyetlen tokenként való kibocsátása.  </br></br>**Beállítások**  </br></br>maxTokenLength (típus: int) – a jogkivonat maximális hossza. Alapértelmezett: 256, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva.|  
|[levél](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Nem betűkre osztja a szöveget. A 255 karakternél hosszabb tokenek szét vannak osztva.|  
|[kisbetűket](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |A szöveget nem betűkre osztja, és a szövegeket kisbetűvé alakítja. A 255 karakternél hosszabb tokenek szét vannak osztva.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Szöveg felosztása nyelvspecifikus szabályok használatával.  </br></br>**Beállítások**  </br></br>maxTokenLength (típus: int) – a token maximális hossza, alapértelmezett értéke: 255, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva. A 300 karakternél hosszabb tokenek először a 300 hosszúságú jogkivonatokra vannak bontva, majd ezek a tokenek a maxTokenLength-készlet alapján vannak felosztva.  </br></br>isSearchTokenizer (típus: bool) – az értéke TRUE (igaz), ha a keresési tokenizer használja, akkor FALSE (hamis) értékűre van állítva, ha indexelési tokenizer használják. </br></br>nyelv (típus: karakterlánc) – használandó nyelv, alapértelmezett érték: "English". Az engedélyezett értékek a következők: </br>"Bangla", "bolgár", "katalán", "chineseSimplified", "chineseTraditional", "horvát", "Cseh", "dán", "holland", "English", "francia", "német", "görög", "ógörög", "hindi", "Izlandi", "indonéz", "olasz", "Japán", "kannada", "koreai", "maláj", "Malayalam", "marathi", "norwegianBokmaal", "lengyel", "portugál", "portugueseBrazilian", "punjabi", "román", "orosz", "serbianCyrillic", "serbianLatin", "szlovén", "spanyol", "svéd", "tamil", "telugu", "Thai", "ukrán", "urdu", "vietnami" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| A szöveget nyelvspecifikus szabályokkal osztja el, és csökkenti a szavakat az alapűrlapokra. </br></br>**Beállítások** </br></br>maxTokenLength (típus: int) – a token maximális hossza, alapértelmezett értéke: 255, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva. A 300 karakternél hosszabb tokenek először a 300 hosszúságú jogkivonatokra vannak bontva, majd ezek a tokenek a maxTokenLength-készlet alapján vannak felosztva. </br></br> isSearchTokenizer (típus: bool) – az értéke TRUE (igaz), ha a keresési tokenizer használja, akkor FALSE (hamis) értékűre van állítva, ha indexelési tokenizer használják. </br></br>nyelv (típus: karakterlánc) – használandó nyelv, alapértelmezett érték: "English". Az engedélyezett értékek a következők: </br>"Arab", "Bangla", "bolgár", "katalán", "horvát", "Cseh", "dán", "holland", "English", "észt", "finn", "francia", "német", "görög", "spanyol", "héber", "hindi", "magyar", "Izlandi", "indonéz", "olasz", "kannada", "lett", "litván", "maláj", "Malayalam", "marathi", "norwegianBokmaal", "lengyel", "portugál", "portugueseBrazilian", "punjabi", "román", "orosz", "serbianCyrillic", "serbianLatin", "szlovák", "szlovén", "spanyol", "svéd", "tamil", "telugu", "török", "ukrán", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|A megadott méret (ek) n-grammba Tokenizes a bemenetet. </br></br>**Beállítások** </br></br>minGram (típus: int) – alapértelmezett: 1, maximum: 300. </br></br>maxGram (típus: int) – alapértelmezett: 2, maximum: 300. Nagyobbnak kell lennie, mint minGram. </br></br>tokenChars (típus: String Array) – a tokenekben megőrizni kívánt karakteres osztályok. Megengedett értékek: "Letter", "Digit", "szóköz", "központozás", "szimbólum". Az alapértelmezett érték egy üres tömb – megtartja az összes karaktert. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer a Path-hez hasonló hierarchiák esetében. **Beállítások** </br></br>határolójel (típus: karakterlánc) – alapértelmezett: "/. </br></br>csere (típus: karakterlánc) – Ha be van állítva, a leválasztó karaktert váltja fel. Az alapértelmezett érték ugyanaz, mint a határolójel értéke. </br></br>maxTokenLength (típus: int) – a jogkivonat maximális hossza. Alapértelmezett: 300, maximum: 300. A maxTokenLength-nél hosszabb elérési utak figyelmen kívül lesznek hagyva. </br></br>fordított (típus: bool) – igaz értéke esetén a tokent fordított sorrendben hozza létre. Alapértelmezett érték: false (hamis). </br></br>kihagyás (típus: bool) – a kihagyni kívánt kezdeti tokenek. Az alapértelmezett érték a 0.|  
|[minta](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Ez a tokenizer regex-mintázatot használ a különböző tokenek összeállításához. </br></br>**Beállítások** </br></br> [minta](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (típus: karakterlánc) – reguláris kifejezési minta a jogkivonat-elválasztók egyeztetéséhez. Az alapértelmezett érték a `\W+` , amely a nem a Word karaktereknek felel meg. </br></br>[Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (Type: string) – reguláris kifejezés jelzői. Az alapértelmezett érték egy üres karakterlánc. Megengedett értékek: CANON_EQ, CASE_INSENSITIVE, Megjegyzés, DOTALL, LITERÁL, többsoros, UNICODE_CASE, UNIX_LINES </br></br>Csoport (típus: int) – a tokenekre kinyerni kívánt csoport. Az alapértelmezett érték:-1 (felosztás).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Megszakítja a szöveget a [Unicode-szöveg szegmentálási szabályai](https://unicode.org/reports/tr29/)után. </br></br>**Beállítások** </br></br>maxTokenLength (típus: int) – a jogkivonat maximális hossza. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes URL-címeket és e-maileket egyetlen tokenként. </br></br>**Beállítások** </br></br> maxTokenLength (típus: int) – a jogkivonat maximális hossza. Alapértelmezett: 255, maximum: 300. A maximális hossznál hosszabb tokenek vannak felosztva.|  
|[szóköz](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |Szóközre osztja a szöveget. A 255 karakternél hosszabb tokenek szét vannak osztva.|  

 <sup>1</sup> a Tokenizer-típusok mindig előtaggal vannak ellátva a "#Microsoft. Azure. Search" kóddal, így a "ClassicTokenizer" kifejezés valójában "#Microsoft. Azure. Search. ClassicTokenizer" lehet. Eltávolította az előtagot a táblázat szélességének csökkentése érdekében, de ne feledje, hogy belefoglalja a kódot. Figyelje meg, hogy tokenizer_type csak a testre szabható tokenizers esetében adható meg. Ha nincsenek beállítások, mint például a tokenizer betűjele, nincs társított #Microsoft. Azure. Search típus.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Jogkivonat-szűrők

A jogkivonat-szűrő a tokenizer által generált jogkivonatok kiszűrésére és módosítására szolgál. Megadhat például egy kisbetűs szűrőt, amely az összes karaktert kisbetűsre alakítja. Egy egyéni elemzőben több jogkivonat-szűrő is megadható. A jogkivonat-szűrők a felsorolt sorrendben futnak. 

Az alábbi táblázatban az Apache Lucene használatával megvalósított jogkivonat-szűrők a Lucene API dokumentációjában vannak társítva.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Leírás és beállítások**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Egy jogkivonat-szűrő, amely az Arab normalizáló alkalmazásával normalizálja az ábécét.|  
|[aposztróf](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Az összes karaktert egy aposztróf után (beleértve az aposztrófot). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Az alfanumerikus, numerikus és szimbolikus Unicode karaktereket alakítja át, amelyek nem az első 127 ASCII-karakterben vannak (az "alapszintű latin" Unicode blokk), ha van ilyen.<br /><br /> **Beállítások**<br /><br /> preserveOriginal (típus: bool) – Igaz érték esetén az eredeti tokent megőrzi a rendszer. Az alapértelmezett érték a false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Az űrlapok a StandardTokenizer-ból generált CJK-kifejezések bigrams.<br /><br /> **Beállítások**<br /><br /> ignoreScripts (típus: String Array) – figyelmen kívül hagyható parancsfájlok. Az engedélyezett értékek a következők: "Han", "hiragana", "katakana", "hangul". Az alapértelmezett érték egy üres lista.<br /><br /> outputUnigrams (típus: bool) – állítsa igaz értékre, ha mindig a unigrams és a bigrams kimenetet szeretné megadni. Az alapértelmezett érték a false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Normalizálja a CJK vastagsági különbségeit. A teljes szélességű ASCII-változatokat a megfelelő kanaba illeszti az egyenértékű alapszintű latin és félszélességű katakana-változatokba. |  
|[klasszikus](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Eltávolítja az angol birtokosokat és a pontokat a betűszók közül. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Az indexelés során a gyakran előforduló kifejezésekhez bigrams hozhat létre. Az egyes feltételek továbbra is indexelve vannak, és a bigrams átfedésben van.<br /><br /> **Beállítások**<br /><br /> commonWords (típus: karakterlánc tömb) – a gyakori szavak halmaza. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> ignoreCase (típus: bool) – Igaz érték esetén a megfeleltetés a kis-és nagybetűk megkülönböztetése. Az alapértelmezett érték a false.<br /><br /> queryMode (típus: bool) – a bigrams generál, majd eltávolítja a gyakori szavakat és az egyetlen kifejezést, majd egy közös szót. Az alapértelmezett érték a false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Számos germán nyelven talált összetett szavakat tartalmaz.<br /><br /> **Beállítások**<br /><br /> Szólista (típus: String Array) – a megfeleltetéshez használandó szavak listája. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> minWordSize (típus: int) – csak az ehhez a lekérésnél hosszabb szavakat kell feldolgozni. Az alapértelmezett érték az 5.<br /><br /> minSubwordSize (típus: int) – csak az ennél hosszabb alkifejezések jelennek meg. Az alapértelmezett érték a 2.<br /><br /> maxSubwordSize (típus: int) – csak az ennél rövidebb alkifejezések vannak megadva. Az alapértelmezett érték 15.<br /><br /> onlyLongestMatch (típus: bool) – csak a leghosszabb egyező alszót adja hozzá a kimenethez. Az alapértelmezett érték a false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|A megadott méret (ek) n-grammját hozza létre a bemeneti token elejétől vagy hátultól kezdve.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: 2, maximális 300. Nagyobbnak kell lennie, mint minGram.<br /><br /> oldal (típus: karakterlánc) – Megadja, hogy a bemenet melyik oldalán kell létrehozni az n-grammot. Megengedett értékek: "elöl", "vissza" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Eltávolítja a elisions. Például a "l ' Avion" (a síkja) a "avion" (repülőgép) értékre lesz konvertálva.<br /><br /> **Beállítások**<br /><br /> cikkek (típus: String Array) – az eltávolítandó cikkek halmaza. Az alapértelmezett érték egy üres lista. Ha nincs beállítva a cikkek listája, alapértelmezés szerint minden francia cikk törlődik.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |A német karakterek normalizálása a [German2 hógolyó algoritmus heurisztikus algoritmusa](https://snowballstem.org/algorithms/german2/stemmer.html) alapján.|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Normalizálja a szöveget a hindi nyelven, hogy eltávolítsa a különböző helyesírási eltéréseket. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|A szöveg Unicode-megjelenítésének normalizálása indiai nyelveken.
|[tartsa](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Jogkivonat-szűrő, amely csak a megadott szólistában található szöveggel rendelkező jogkivonatokat tartja.<br /><br /> **Beállítások**<br /><br /> keepWords (típus: String Array) – A megőrizni kívánt szavak listája. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> keepWordsCase (típus: bool) – Ha az értéke igaz, akkor először az összes szó szerepel. Az alapértelmezett érték a false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|A kifejezések kulcsszavaként való megjelölése.<br /><br /> **Beállítások**<br /><br /> Kulcsszavak (típus: karakterlánc-tömb) – A kulcsszavakat jelölő szavak listája. Az alapértelmezett érték egy üres lista. Kötelező.<br /><br /> ignoreCase (típus: bool) – Ha az értéke igaz, akkor először az összes szó szerepel. Az alapértelmezett érték a false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Minden bejövő tokent kétszer, egy kulcsszóként és egyszer, nem pedig kulcsszóként bocsát ki. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Nagy teljesítményű kstem-szűrő angol nyelven. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html) (hossz)|LengthTokenFilter|Eltávolítja a túl hosszú vagy túl rövid szavakat.<br /><br /> **Beállítások**<br /><br /> min (típus: int) – a minimális szám. Alapértelmezett: 0, maximum: 300.<br /><br /> Max (típus: int) – a maximális szám. Alapértelmezett: 300, maximum: 300.|  
|[korlát](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft. Azure. Search. LimitTokenFilter|Az indexelés során korlátozza a tokenek számát.<br /><br /> **Beállítások**<br /><br /> maxTokenCount (típus: int) – a létrehozni kívánt tokenek maximális száma. Az alapértelmezett érték 1.<br /><br /> consumeAllTokens (típus: bool) – azt határozza meg, hogy a bemenetből származó összes tokent akkor is fel kell-e használni, ha a maxTokenCount elérésekor. Az alapértelmezett érték a false.|  
|[kisbetűket](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Normalizálja a jogkivonat szövegét az kisbetű értékre. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Az adott méret (ek) n-grammját hozza létre.<br /><br /> **Beállítások**<br /><br /> minGram (típus: int) – alapértelmezett: 1, maximum: 300.<br /><br /> maxGram (típus: int) – alapértelmezett: 2, maximális 300. Nagyobbnak kell lennie, mint minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|A Java regexek használatával több tokent bocsát ki, egyet az egyes rögzítési csoportokhoz egy vagy több mintában.<br /><br /> **Beállítások**<br /><br /> minták (típus: String Array) – az egyes jogkivonatokkal egyeztetendő minták listája. Kötelező.<br /><br /> preserveOriginal (típus: bool) – igaz értékre állítva az eredeti token visszaadásához, akkor is, ha az egyik minta megegyezik, alapértelmezés: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Egy jogkivonat-szűrő, amely mintát alkalmaz az adatfolyamban található minden egyes tokenre, és lecseréli az egyeztetési karakterláncot a megadott helyettesítő sztringre.<br /><br /> **Beállítások**<br /><br /> minta (típus: karakterlánc) – kötelező.<br /><br /> csere (típus: karakterlánc) – kötelező.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.) |A perzsa nyelvű normalizálás alkalmazása. |  
|[fonetikus](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Hozzon létre jogkivonatokat a fonetikus egyezésekhez.<br /><br /> **Beállítások**<br /><br /> kódoló (típus: karakterlánc) – a használni kívánt fonetikus kódoló. Az engedélyezett értékek közé tartoznak a következők: "metaphone", "doubleMetaphone", "Soundex", "refinedSoundex", "caverphone1", "caverphone2", "Cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Alapértelmezett: "metaphone". Az alapértelmezett érték a metaphone.<br /><br /> További információért lásd a [kódolót](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) .<br /><br /> lecserélés (típus: bool) – igaz, ha a kódolt tokenek felülírják az eredeti jogkivonatokat, a hamis értéket, ha szinonimának kell szerepelniük. Az alapértelmezett érték a True (igaz).|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Átalakítja a jogkivonat-adatfolyamot a [Porting eredő algoritmusnak](https://tartarus.org/~martin/PorterStemmer/)megfelelően. |  
|[fordított](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Megfordítja a jogkivonat sztringjét. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Normalizálja a felcserélhető skandináv karakterek használatát. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |A åÅäæÄÆ->a és a öÖøØ->o skandináv karaktereket hajtja. Emellett nem tesz különbséget az AA, AE, Ao, OE és OO kettős magánhangzók használatában, és csak az elsőt hagyja. |  
|[zsindely](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Tokenek kombinációit hozza létre egyetlen jogkivonatként.<br /><br /> **Beállítások**<br /><br /> maxShingleSize (típus: int) – az alapértelmezett érték a 2.<br /><br /> minShingleSize (típus: int) – az alapértelmezett érték a 2.<br /><br /> outputUnigrams (típus: bool) – igaz értéke esetén a kimeneti adatfolyam tartalmazza a bemeneti jogkivonatokat (unigrams) és a zsindelyt. Az alapértelmezett érték a True (igaz).<br /><br /> outputUnigramsIfNoShingles (típus: bool) – igaz értéke esetén felülbírálja a outputUnigrams = = false viselkedését azokban az esetekben, amikor nem érhető el zsindely. Az alapértelmezett érték a false.<br /><br /> tokenSeparator (Type: string) – a szomszédos tokenekhez a zsindelyek létrehozásához használandó karakterlánc. Az alapértelmezett érték: "".<br /><br /> filterToken (Type: string) – a beszúrandó karakterlánc, amelyben az egyes pozíciókat nem lehet jogkivonat. Az alapértelmezett érték a "_".|  
|[hógolyó](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Hógolyó-jogkivonat szűrője.<br /><br /> **Beállítások**<br /><br /> nyelv (Type: string) – a megengedett értékek a következők: "örmény", "baszk", "katalán", "dán", "holland", "English", "finn", "francia", "német", "german2", "Hungarian", "olasz", "KP", "Lovins", "norvég", "Porter", "portugál", "román", "orosz", "spanyol", "svéd", "török"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizálja a sorani-szöveg Unicode-ábrázolását.<br /><br /> **Beállítások**<br /><br /> Nincsenek.|  
|szótőkereső|StemmerTokenFilter|Nyelvspecifikus szűrő.<br /><br /> **Beállítások**<br /><br /> nyelv (Type: string) – a megengedett értékek a következők: <br /> -   [Arab](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [örmény](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [baszk](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [Brazíliai](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"bolgár"<br />-   [katalán](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [Cseh](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [dán](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [holland](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [angol](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   [Lovins](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [finn](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [francia](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"galíciai"<br />- "minimalGalician"<br />-   [német](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [görög](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"hindi"<br />-   [Magyar](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [indonéz](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [ír](https://snowballstem.org/otherapps/oregan/)<br />-   [olasz](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [sorani](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [lett](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [Norvég](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [Portugál](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [román](https://snowballstem.org/otherapps/romanian/)<br />-   [Orosz](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [spanyol](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [svéd](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [Török](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Bármely szótár – a rendszer kulcsszavaként jelöli meg a kifejezéseket, ami megakadályozza a lánc leállítását. A kikerülő szűrők előtt kell elhelyezni.<br /><br /> **Beállítások**<br /><br /> szabályok (típus: karakterlánc tömb) – a szabályok a következő formátumban jelennek meg: "Word => Stem", például "Ran => Futtatás". Az alapértelmezett érték egy üres lista.  Kötelező.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Eltávolítja a leállítási szavakat a jogkivonat-adatfolyamból. Alapértelmezés szerint a szűrő az angol nyelvű előre definiált leállítási szólistát használja.<br /><br /> **Beállítások**<br /><br /> indexelendő (típus: String Array) – A indexelendő listája. Nem adható meg, ha meg van adva egy stopwordsList.<br /><br /> stopwordsList (Type: string) – A indexelendő előre definiált listája. Nem adható meg, ha a indexelendő meg van adva. Az engedélyezett értékek a következők: "Arab", "örmény", "baszk", "brazil", "Bulgár", "katalán", "Cseh", "dán", "holland", "English", "finn", "francia", "galíciai", "német", "görög", "hindi", "magyar", "indonéz", "Irish", "olasz", "lett", "norvég", "perzsa", "portugál", "román", "orosz", "sorani", "spanyol", "svéd", "Thai", "török", alapértelmezett: "English". Nem adható meg, ha a indexelendő meg van adva. <br /><br /> ignoreCase (típus: bool) – Ha az értéke igaz, a rendszer először az összes szót csökkenti. Az alapértelmezett érték a false.<br /><br /> removeTrailing (típus: bool) – Ha az értéke igaz, a rendszer figyelmen kívül hagyja a legutóbbi keresési kifejezést, ha az egy leállítási szó. Az alapértelmezett érték a True (igaz).
|[szinonima](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Egyetlen vagy több szó szinonimái a jogkivonat-adatfolyamban.<br /><br /> **Beállítások**<br /><br /> szinonimák (típus: String Array) – kötelező. Szinonimák listája a következő két formátum valamelyikében:<br /><br /> -hihetetlen, nem hihető, mesés => csodálatos – a bal> oldalán lévő összes kifejezés helyébe a jobb oldalán lévő összes kifejezés lép.<br /><br /> – hihetetlen, nem hihető, mesés, bámulatos – az egyenértékű szavak vesszővel tagolt listája. A Kibontás beállítás megadásával módosíthatja a lista értelmezésének módját.<br /><br /> ignoreCase (típus: bool) – a rendszer a megfelelő esetben bedobja a bemenetet. Az alapértelmezett érték a false.<br /><br /> Kibontás (típus: bool) – Igaz érték esetén a szinonimák listájában szereplő összes szó (IF => jelölése nincs használatban) leképezése egymásra. <br />A következő lista: hihetetlen, nem hihető, mesés, csodálatos: hihetetlen, nem hihető, mesés, Amazing => Incredible, hihetetlen, mesés, Amazing<br /><br />-Ha hamis, a következő lista: hihetetlen, nem hihető, mesés, elképesztő a következőkkel egyenértékű: Incredible, hihetetlen, mesés, Amazing => hihetetlen.|  
|[Trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |Levágja a tokenek kezdő és záró szóközöket. |  
|[truncate](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Levágja a feltételeket egy adott hosszra.<br /><br /> **Beállítások**<br /><br /> Hossz (típus: int) – alapértelmezett: 300, maximum: 300. Kötelező.|  
|[egyedi](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Kiszűri a tokeneket ugyanazzal a szöveggel, mint az előző jogkivonat.<br /><br /> **Beállítások**<br /><br /> onlyOnSamePosition (típus: bool) – Ha be van állítva, távolítsa el a duplikált elemeket csak ugyanazon a helyen. Az alapértelmezett érték a True (igaz).|  
|[nagybetűk](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(a típus csak akkor érvényes, ha elérhetők a lehetőségek.)  |A jogkivonat szövegének normalizálása nagybetűvé. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Feldarabolja a szavakat alszavakba, és nem kötelező átalakításokat hajt végre a alword-csoportokon.<br /><br /> **Beállítások**<br /><br /> generateWordParts (Type: bool) – a szavak egy részének generálását okozza (például "AzureSearch") az "Azure" "keresés" lesz. Az alapértelmezett érték a True (igaz).<br /><br /> generateNumberParts (típus: bool) – a rendszer a létrehozandó alszavak számát okozza. Az alapértelmezett érték a True (igaz).<br /><br /> catenateWords (típus: bool) – a Word-részek maximális futását okozhatja catenated, például az "Azure-Search" a "AzureSearch" lesz. Az alapértelmezett érték a false.<br /><br /> catenateNumbers (típus: bool) – a catenated maximális futását eredményezi, például "1-2" lesz "12". Az alapértelmezett érték a false.<br /><br /> catenateAll (típus: bool) – az alkifejezések összes részének catenated válik, például "Azure-Search-1" lesz "AzureSearch1". Az alapértelmezett érték a false.<br /><br /> splitOnCaseChange (típus: bool) – Ha az értéke igaz, a caseChange található szavakat feldarabolja, például "AzureSearch" lesz "Azure" "Search". Az alapértelmezett érték a True (igaz).<br /><br /> preserveOriginal – azt eredményezi, hogy az eredeti szavakat meg kell őrizni, és hozzá kell adni az alszavak listájához. Az alapértelmezett érték a false.<br /><br /> splitOnNumerics (típus: bool) – Ha az értéke igaz, a rendszer a számokat feldarabolja, például "Azure1Search" lesz "Azure" "1" "keresés". Az alapértelmezett érték a True (igaz).<br /><br /> stemEnglishPossessive (típus: bool) – az egyes alszavaknál az "a" törlését eredményezi. Az alapértelmezett érték a True (igaz).<br /><br /> protectedWords (típus: String Array) – a kiválasztható tokenek. Az alapértelmezett érték egy üres lista.|  

 <sup>1</sup> a jogkivonat-szűrési típusok mindig előtaggal vannak ellátva a "#Microsoft. Azure. Search" kóddal, így a "ArabicNormalizationTokenFilter" kifejezés valójában "#Microsoft. Azure. Search. ArabicNormalizationTokenFilter" néven adható meg.  Eltávolította az előtagot a táblázat szélességének csökkentése érdekében, de ne feledje, hogy belefoglalja a kódot.  

## <a name="see-also"></a>Lásd még

- [Azure Cognitive Search REST API-k](/rest/api/searchservice/)
- [Elemzők az Azure Cognitive Searchban (példák)](search-analyzers.md#examples)
- [Index létrehozása (REST)](/rest/api/searchservice/create-index)