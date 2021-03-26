---
title: Javaslattevő létrehozása
titleSuffix: Azure Cognitive Search
description: Adja meg az Azure Cognitive Search típus-előkészítő lekérdezési műveleteit a javaslatok létrehozásával, valamint az automatikus kiegészítés vagy az automatikus javaslattal rendelkező lekérdezési feltételeket meghívó kérelmek összeállításával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bf5e53d9f4a867c146cb01376fcd28d2797819c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606215"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Javaslat létrehozása az automatikus kiegészítés és a javasolt eredmények lekérdezésben való engedélyezéséhez

Az Azure Cognitive Search, a typeahead vagy a "keresési típus" lehetőséget egy *javaslaton* keresztül engedélyezheti. A javaslatok egy belső adatstruktúra, amely egy mező gyűjteményből áll. A mezők további jogkivonatok létrehozása vesznek részt, és előtagokat generálnak, amelyek támogatják a részleges feltételeken alapuló egyezéseket. Egy város mezőt tartalmazó javaslat például a "Sea", az "Seat", a "seatt" és a "seattl" előtag-kombinációkkal rendelkezik a "Seattle" kifejezéshez.

A részleges kifejezésekre való egyezés lehet egy autocompleted lekérdezés vagy egy javasolt egyezés. Ugyanez a javaslat mindkét élményt támogatja.

## <a name="typeahead-experiences-in-cognitive-search"></a>Typeahead-tapasztalatok Cognitive Search

A typeahead lehet az *automatikus kiegészítés*, amely egy teljes kifejezéssel végzett lekérdezés részleges bemenetét fejezi be *, vagy egy* adott egyezésre kattintva meghívja a kattintást. Az automatikus kiegészítés létrehoz egy lekérdezést. A javaslatok megfelelő dokumentumot hoznak létre.

A következő képernyőkép az [első alkalmazás létrehozása a C#-ban](tutorial-csharp-type-ahead-and-suggestions.md) című ábrán látható. Az automatikus kiegészítés egy lehetséges kifejezést is felhasznál, amely a "tw"-t "a" értékkel fejezi ki. A javaslatok a mini keresési eredmények, ahol a Hotel neve a megfelelő szállodai keresési dokumentumot jelöli az indexből. A javaslatok esetében bármilyen olyan mezőt felvehet, amely leíró információkat biztosít.

![Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Az automatikus kiegészítés és a javasolt lekérdezések vizuális összehasonlítása")

Ezeket a szolgáltatásokat külön vagy együtt is használhatja. A viselkedés Azure Cognitive Searchban való megvalósításához index és lekérdezési összetevő szükséges. 

+ Adjon hozzá egy javaslatot egy keresési index definícióhoz. A cikk további részében a javaslatok létrehozására összpontosítunk.

+ Az [alábbi API](#how-to-use-a-suggester)-k egyikével meghívhat egy javaslattal rendelkező lekérdezést, amely egy javaslat vagy egy automatikus kiegészítési kérelem formájában jelenik meg.

A keresési típust támogató támogatás engedélyezett a karakterlánc típusú mezők esetében a mező alapján. Mindkét typeahead-viselkedést ugyanazon keresési megoldáson belül implementálhatja, ha a képernyőképen láthatóhoz hasonló élményre van szüksége. Mindkét kérelem célja az adott index és válaszok *dokumentumainak* gyűjteménye, miután egy felhasználó legalább három karakterből álló bemeneti karakterláncot adott meg.

## <a name="how-to-create-a-suggester"></a>Javaslatok létrehozása

A javaslatok létrehozásához vegyen fel egyet egy [index-definícióba](/rest/api/searchservice/create-index). A javaslatok olyan nevet és mezőket vesznek fel, amelyeken a typeahead-élmény engedélyezve van. A javaslatok létrehozásához a legjobb idő az, amikor azt a mezőt is meghatározza, amelyik azt fogja használni.

+ Csak karakterlánc-mezőket használjon.

+ Ha a karakterlánc mező egy összetett típus része (például a címen belüli város mező), vegye fel a szülőt a mező elérési útjába: `"Address/City"` (REST, C# és Python), vagy `["Address"]["City"]` (JavaScript).

+ Használja az alapértelmezett standard Lucene-elemzőt ( `"analyzer": null` ) vagy egy [nyelvi elemzőt](index-add-language-analyzers.md) (például `"analyzer": "en.Microsoft"` ) a mezőn.

Ha már meglévő mezők használatával próbál létrehozni egy javaslatot, az API letiltja azt. Az előtagok az indexelés során jönnek létre, ha a két vagy több karakterből álló kombinációk részleges kifejezései a teljes feltételek mellett jogkivonatot kapnak. Mivel a meglévő mezők már jogkivonattal rendelkeznek, újra kell építenie az indexet, ha hozzá kívánja adni őket egy javaslathoz. További információ: [Azure Cognitive Search index újraépítése](search-howto-reindex.md).

### <a name="choose-fields"></a>Mezők kiválasztása

Bár a javaslatok több tulajdonsággal rendelkeznek, ez elsősorban olyan karakterlánc-mezők gyűjteménye, amelyekhez egy keresési típust engedélyez. Minden indexhez van egy javaslat, így a javaslatok listájának tartalmaznia kell az összes olyan mezőt, amely a javaslatokhoz és az automatikus kiegészítésekhez is hozzájárul a tartalomhoz.

Az automatikus kiegészítés kihasználja a mezők nagyobb készletének előnyeit, mivel a további tartalomhoz több távú befejezési lehetőség áll rendelkezésre.

A javaslatok azonban jobb eredményeket eredményeznek, ha a kiválasztott mező szelektív. Ne feledje, hogy a javaslat egy keresési dokumentum proxyja, ezért olyan mezőket szeretne használni, amelyek a legjobban egyetlen eredményt képviselnek. Azok a nevek, címek vagy más egyedi mezők, amelyek megkülönböztetik a többszörös egyezések közül a legmegfelelőbbet. Ha a mezők ismétlődő értékekből állnak, a javaslatok azonos eredményekből állnak, és a felhasználók nem tudják, melyikre kattintanak.

Ahhoz, hogy mindkét keresési típust kielégítse, adja hozzá az automatikus kiegészítéshez szükséges összes mezőt, majd használja a "$select", a "$top", a "$filter" és a "searchFields" kifejezést a javaslatok eredményeinek szabályozásához.

### <a name="choose-analyzers"></a>Elemzők kiválasztása

Az analizátor választása határozza meg, hogy a mezők hogyan legyenek jogkivonatban, és hogyan lesznek előre rögzítve. Például egy olyan elválasztott karakterlánc esetében, mint a "környezetfüggő", a nyelvi elemző használatával a következő jogkivonat-kombinációkat fogja eredményezni: "Context", "szenzitív", "Context-szenzitív". A standard Lucene Analyzert használta, az elválasztott karakterlánc nem létezik. 

Az elemzők kiértékelése során érdemes lehet a [szöveg elemzése API](/rest/api/searchservice/test-analyzer) -t használni a feltételek feldolgozásának a betekintéshez. Miután létrehozta az indexet, különböző elemzőket próbálhat ki egy sztringen a jogkivonat kimenetének megtekintéséhez.

Az [Egyéni elemzőket](index-add-custom-analyzers.md) vagy [beépített elemzőket](index-add-custom-analyzers.md#built-in-analyzers) használó mezők (a standard Lucene kivételével) kifejezetten nem teszik lehetővé a gyenge eredmények elkerülését.

> [!NOTE]
> Ha az analizátor korlátozását kell meghoznia, például ha egy kulcsszóra vagy ngram-elemzőre van szüksége bizonyos lekérdezési forgatókönyvekhez, két külön mezőt kell használnia ugyanahhoz a tartalomhoz. Ez lehetővé teszi, hogy az egyik mező legyen egy javaslat, míg a másik egyéni Analyzer-konfigurációval is beállítható.

## <a name="create-using-the-portal"></a>Létrehozás a portál használatával

Ha indexet szeretne létrehozni az **index hozzáadása** vagy az **adatimportálás** varázsló használatával, lehetősége van a javaslatok engedélyezésére:

1. Az index definíciójában adja meg a javaslat nevét.

1. Az új mezők minden mező-definíciójában jelölje ki a jelölőnégyzetet a javaslat oszlopban. A jelölőnégyzet csak karakterlánc-mezőkön érhető el. 

Ahogy azt korábban említettük, az analizátor Choice hatással van a jogkivonatok létrehozása és az előjavításra. A javaslatok engedélyezésekor vegye figyelembe a teljes mező definícióját. 

## <a name="create-using-rest"></a>Létrehozás a REST használatával

A REST API adjon hozzá javaslatokat a [create index](/rest/api/searchservice/create-index) vagy az [Update index](/rest/api/searchservice/update-index)használatával. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Létrehozás .NET használatával

A C# nyelvben Definiáljon egy [SearchSuggester objektumot](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` egy gyűjtemény egy SearchIndex objektumon, de csak egyetlen elemből áll. Adjon hozzá egy javaslatot az index definícióhoz.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Tulajdonság leírása

|Tulajdonság      |Leírás      |
|--------------|-----------------|
| name        | A javaslat definíciójában van megadva, de egy automatikus kiegészítési vagy javaslati kérelemre is meghívva. |
| sourceFields | A javaslat definíciójában van megadva. Az index egy vagy több mezőjének listája, amely a javaslatok tartalmának forrása. A mezőknek Type és típusúnak kell lenniük `Edm.String` `Collection(Edm.String)` . Ha a mezőben egy analizátor van megadva, akkor a [listán szereplő](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) lexikális analizátornak kell lennie (nem egyéni elemző). </br></br>Ajánlott eljárásként csak azokat a mezőket kell megadnia, amelyek a várt és a megfelelő választ adják meg, legyen szó egy keresési sávon vagy egy legördülő listában szereplő befejezett sztringről. </br></br>A Hotel neve jó jelölt, mert pontossággal rendelkezik. A részletes mezők, például a leírások és a megjegyzések túl sűrűk. Ehhez hasonlóan az ismétlődő mezők, például a kategóriák és a címkék kevésbé hatékonyak. A példákban bemutatjuk a "kategória" kifejezést is, amely azt mutatja be, hogy több mezőt is tartalmazhat. |
| searchMode  | Csak REST paraméter, de a portálon is látható. Ez a paraméter nem érhető el a .NET SDK-ban. Ez jelzi a jelölt kifejezésekre való kereséshez használt stratégiát. Az egyetlen jelenleg támogatott mód a (z `analyzingInfixMatching` ), amely jelenleg a kifejezés kezdetének felel meg.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Javaslat használata

A rendszer egy lekérdezést használ. A javaslat létrehozása után hívja meg a következő API-k egyikét a keresési típushoz:

+ [Javaslatok REST API](/rest/api/searchservice/suggestions)
+ [Automatikus kiegészítés REST API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync metódus](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync metódus](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

Egy keresési alkalmazásban az ügyfél kódjának egy olyan könyvtárat kell kihasználnia, mint a [jQuery felhasználói felületének automatikus kiegészítése](https://jqueryui.com/autocomplete/) a részleges lekérdezés összegyűjtéséhez és a egyezés biztosításához. További információ erről a feladatról: [automatikus kiegészítés hozzáadása vagy javasolt eredmények az ügyfél kódjához](search-add-autocomplete-suggestions.md).

Az API-használat a következő, az automatikus kiegészítési REST APIra irányuló hívásban látható. Ebből a példából két elvihetőség van. Először is, ahogy az összes lekérdezés esetében, a művelet egy index dokumentumainak gyűjteményén alapul, és a lekérdezés tartalmaz egy "Search" paramétert, amely ebben az esetben a részleges lekérdezést biztosítja. Másodszor, a kérelemhez hozzá kell adnia a "suggesterName" kifejezést. Ha egy javaslat nincs definiálva az indexben, az automatikus kiegészítés vagy a javaslatok sikertelenek lesznek.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Mintakód

+ [Hozza létre első alkalmazását C# nyelven (3. lecke – keresési típus hozzáadása)](tutorial-csharp-type-ahead-and-suggestions.md) minta bemutatja a javasolt lekérdezéseket, az automatikus kiegészítést és a sokoldalú navigációt. Ez a mintakód egy sandbox Azure Cognitive Search Service-ben fut, és egy előre betöltött Hotel-indexet használ egy már létrehozott javaslattal, így mindössze annyit kell tennie, hogy az alkalmazás futtatásához nyomja le az F5 billentyűt. Nincs szükség előfizetésre vagy bejelentkezésre.

## <a name="next-steps"></a>Következő lépések

A kérelmek létrehozásával kapcsolatban a következő cikkből tájékozódhat.

> [!div class="nextstepaction"]
> [Automatikus kiegészítés és javaslatok hozzáadása az ügyfél kódjához](search-add-autocomplete-suggestions.md)