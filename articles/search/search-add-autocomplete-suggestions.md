---
title: Automatikus kiegészítés hozzáadása egy keresési mezőhöz
titleSuffix: Azure Cognitive Search
description: Az Azure-Cognitive Search keresési típusú lekérdezési műveleteinek engedélyezése a javaslatok létrehozásával és a befejezett kifejezésekkel vagy kifejezésekkel rendelkező keresőmező automatikus kiegészítésére szolgáló kérelmek összeállításával. A javasolt egyezéseket is visszaadhatja.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6bc45bb6eec9dbf46e039dd1e2c32197820bb09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626707"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Automatikus kiegészítés és javaslatok hozzáadása az ügyfélalkalmazások számára az Azure Cognitive Search használatával

A keresési típus a gyakori módszer a lekérdezési hatékonyság javítására. Az Azure Cognitive Searchban ez a megoldás az *automatikus kiegészítésen* keresztül támogatott, amely egy kifejezést vagy kifejezést végez a részleges bevitel ("Micro" és "Microsoft") alapján. A második felhasználói élmény *javaslatok*, vagy a megfelelő dokumentumok rövid listája (a könyv címének visszaadása egy azonosítóval, amely a könyv részletes oldalára hivatkozik). Az automatikus kiegészítés és a javaslatok is az indexben egyeznek meg. A szolgáltatás nem kínál olyan lekérdezéseket, amelyek nulla eredményt adnak vissza.

A tapasztalatok Azure Cognitive Search-ban való megvalósításához a következőkre lesz szüksége:

+ Az index sémába beágyazott *javaslati* definíció.
+ Egy *lekérdezés* , amely az [automatikus kiegészítést](/rest/api/searchservice/autocomplete) vagy a [javaslatok](/rest/api/searchservice/suggestions) API-t határozza meg a kérelemre vonatkozóan.
+ Egy *felhasználói felületi vezérlő* , amely a keresési típusok közötti interakciókat kezeli az ügyfélalkalmazás számára. Azt javasoljuk, hogy egy meglévő JavaScript-függvénytárat használjon erre a célra.

Az Azure Cognitive Searchban az automatikusan befejezett lekérdezéseket és a javasolt eredményeket a rendszer a keresési indexből kérdezi le, a kijelölt mezőkből, amelyeket egy javaslatban regisztráltak. A javaslat az index részét képezi, és meghatározza, hogy mely mezők biztosítanak a lekérdezés befejezését, vagy a két művelet eredményét. Az index létrehozásakor és betöltésekor a rendszer belsőleg létrehoz egy szuggesztív adatstruktúrát a részleges lekérdezésekhez való megfeleltetéshez használt előtagok tárolásához. A javaslatok, amelyek egyedi, vagy legalábbis ismétlődő, megfelelő mezők kiválasztásával elengedhetetlenek a felhasználói élményhez. További információ: [javaslat létrehozása](index-add-suggesters.md).

A cikk további része a lekérdezésekre és az ügyfél kódjára összpontosít. A JavaScript és a C# használatával mutatja be a legfontosabb pontokat. REST API példákat használunk az egyes műveletek tömör bemutatása érdekében. A végpontok közötti kód mintákra mutató hivatkozásokat a [következő lépések](#next-steps)című szakaszban tekintheti meg.

## <a name="set-up-a-request"></a>Kérelem beállítása

A kérelem elemei közé tartozik az egyik keresési típusú API, egy részleges lekérdezés és egy javaslat. A következő parancsfájl egy kérelem összetevőit mutatja be, az automatikus kiegészítési REST API használatával példaként.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

A "suggesterName" Megadja a feltételek vagy javaslatok végrehajtásához használni kívánt, javasolt mezőket. A konkrét javaslatok esetében a mezőlista olyan elemekből áll, amelyek a megfelelő eredmények között egyértelmű döntéseket nyújtanak. A számítógépes játékokat értékesítő webhelyeken a játék címe lehet.

A "keresés" paraméter a részleges lekérdezést adja meg, ahol a karakterek a lekérdezési kérelemhez a jQuery automatikus kiegészítés-vezérlőn keresztül vannak betáplálva. A fenti példában a "minecraf" egy statikus ábrája annak, amit a vezérlő átadott.

Az API-k nem írnak elő minimális hosszúságú követelményeket a részleges lekérdezéshez; akár egy karakter is lehet. A jQuery automatikus kiegészítés azonban minimális hosszt biztosít. A minimum kettő vagy három karakter jellemző.

A egyezések egy kifejezés elején vannak, bárhol a bemeneti karakterláncban. A "The Quick Brown Fox", az automatikus kiegészítés és a javaslatok a "The", a "Quick", a "Brown" vagy a "Fox" részleges verzióihoz tartoznak, de nem a részleges Infix, például a "sorvégi" vagy az "Ox" kifejezéssel. Emellett az egyes egyezések az alsóbb rétegbeli bővítések hatókörét határozzák meg. A "Quick br" részleges lekérdezése megfelel a "Quick Brown" vagy a "Quick kenyér" kifejezésnek, de a "barna" vagy a "kenyér" nem egyezik meg egymással, hacsak a "gyors" megelőzi őket.

### <a name="apis-for-search-as-you-type"></a>A kereséshez használt API-k

Kövesse az alábbi hivatkozásokat a REST és a .NET SDK-referenciák oldalaihoz:

+ [Javaslatok REST API](/rest/api/searchservice/suggestions) 
+ [Automatikus kiegészítés REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestAsync metódus](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync metódus](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Válasz strukturálása

Az automatikus kiegészítésre és a javaslatokra adott válaszok a következő mintában számíthatnak: az [automatikus kiegészítés](/rest/api/searchservice/autocomplete#response) a feltételek listáját adja vissza, a [javaslatok](/rest/api/searchservice/suggestions#response) pedig egy dokumentum azonosítóját, így a dokumentum beolvasása érdekében (a [keresési dokumentum](/rest/api/searchservice/lookup-document) API-val lekérheti az adott dokumentumot egy részletes oldalhoz).

A válaszok a kérelem paramétereinek alapján vannak formázva:

+ Az automatikus kiegészítés beállításnál állítsa be a [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) annak megállapítására, hogy a szöveg befejezése egy vagy két kifejezésen történik-e. 

+ A javaslatok beállításnál állítsa be a [$Select](/rest/api/searchservice/suggestionse#query-parameters) értéket, amely egyedi vagy megkülönböztető értékeket (például neveket és leírást) tartalmazó mezőket ad vissza. Kerülje az ismétlődő értékeket (például kategóriát vagy várost) tartalmazó mezőket.

A következő további paraméterek is érvényesek az automatikus kiegészítésre és a javaslatokra, de talán a javaslatoknál nagyobb szükség van rá, különösen akkor, ha egy javaslat több mezőt is tartalmaz.

| Paraméter | Használat |
|-----------|-------|
| searchFields | A lekérdezés korlátozása adott mezőkre. |
| $filter | Egyezési feltételek alkalmazása az eredményhalmaz () értékre `$filter=Category eq 'ActionAdventure'` . |
| $top | Korlátozza az eredményeket egy adott számra ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Felhasználói interakciós kód hozzáadása

Egy lekérdezési kifejezés automatikus kitöltésével vagy a megfelelő hivatkozások listájának lebontásával a felhasználói interakciós kód (jellemzően JavaScript) szükséges a külső forrásokból érkező kérések, például az automatikus kiegészítés vagy a javaslatok lekérdezése Azure Search kognitív indexen keresztül.

Bár ezt a kódot natív módon is megírhatja, sokkal könnyebb használni a függvényeket a meglévő JavaScript-könyvtárból, például a következők egyikét. 

+ Az [automatikus kiegészítés widget (JQUERY UI)](https://jqueryui.com/autocomplete/) megjelenik a javaslati kódrészletben. Létrehozhat egy keresőmezőt, majd hivatkozhat arra egy JavaScript-függvényben, amely az automatikus kiegészítés widgetet használja. A widget tulajdonságai a forrást (automatikus kiegészítés vagy javaslatok függvény), a művelet végrehajtása előtt a bemeneti karakterek minimális hosszát és a pozicionálást is beállítják.

+ A [XDSoft automatikus kiegészítés beépülő modulja](https://xdsoft.net/jqplugins/autocomplete/) megjelenik az automatikus kiegészítési kódrészletben.

+ a [javaslatok](https://www.npmjs.com/package/suggestions) a [JavaScript-oktatóanyagban](tutorial-javascript-overview.md) és a kód mintában jelennek meg.

Ezeket a kódtárakat az ügyfélen a javaslatok és az automatikus kiegészítést támogató keresőmező létrehozásához használhatja. A keresőmezőbe gyűjtött bemenetek a keresési szolgáltatás javaslataival és automatikus kiegészítési műveleteivel párosítva lesznek.

## <a name="suggestions"></a>Javaslatok

Ez a szakasz végigvezeti a javasolt eredmények megvalósításán, kezdve a keresőmező-definícióval. Azt is bemutatja, hogyan és szkripttel hívja meg a cikkben hivatkozott első JavaScript automatikus kiegészítési függvénytárat.

### <a name="create-a-search-box"></a>Keresőmező létrehozása

Feltételezve, hogy a [jQuery felhasználói felület automatikus kiegészítésének könyvtára](https://jqueryui.com/autocomplete/) és egy MVC-projekt a C#-ban, az **index. Cshtml** fájlban a JavaScript használatával definiálhatja a keresőmezőt. A könyvtár az MVC-vezérlőhöz a javaslatok beolvasásához aszinkron módon hívja fel a keresési típus típusú interakciót a keresőmezőbe.

Az **index. cshtml** mappa \Views\Home területén a keresőmező létrehozásához a következő sor lehet:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Ez a példa egy egyszerű beviteli szövegmező, amelynek a stílusa, a JavaScript által hivatkozott azonosító, valamint a helyőrző szövege.  

Ugyanazon a fájlon belül ágyazza be a JavaScriptet, amely a keresőmezőbe hivatkozik. A következő függvény meghívja az ajánlott API-t, amely a javasolt egyezési dokumentumokat a részleges feltételek bemenetei alapján kéri le:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Az `source` azt jelzi, hogy a jQuery felhasználói felületének automatikus kiegészítési funkciója a keresőmező alatt megjelenítendő elemek listájának beolvasása. Mivel ez a projekt egy MVC-projekt, a **HomeController. cs** metódusban hívja meg a **suggestion függvényt** , amely tartalmazza a lekérdezési javaslatok visszaadására vonatkozó logikát. Ez a függvény néhány paramétert is továbbít a csúcsfények, a zavaros egyezés és a kifejezés szabályozásához. Az automatikus kiegészítés JavaScript API hozzáadja a kifejezés paramétert.

Az `minLength: 3` biztosítja, hogy a javaslatok csak akkor jelenjenek meg, ha legalább három karakter szerepel a keresőmezőbe.

### <a name="enable-fuzzy-matching"></a>A zavaros egyezés engedélyezése

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. A szerkesztési távolság 1, ami azt jelenti, hogy a felhasználói bevitel és a találatok között egy karakterből álló maximális eltérés lehet. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Kiemelés engedélyezése

A kiemelés a betűméretet az eredményben szereplő karakterekre alkalmazza, amelyek a bemenetnek felelnek meg. Ha például a részleges bemenet "Micro", az eredmény a **Micro** Soft, a **Micro** scope, és így tovább jelenik meg. A kiemelés a HighlightPreTag és a HighlightPostTag paramétereken alapul, amelyek a javaslat függvénnyel vannak meghatározva.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Javasolt függvény

Ha C#-ot és MVC-alkalmazást használ, a **HomeController. cs** fájl a vezérlők könyvtárban található, ahol létrehozhat egy osztályt a javasolt eredményekhez. A .NET-ben a javaslatok a [SuggestAsync módszer](/dotnet/api/azure.search.documents.searchclient.suggestasync)alapján működnek. A .NET SDK-val kapcsolatos további információkért lásd: az [Azure Cognitive Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A `InitSearch` metódus létrehoz egy hitelesített http-index ügyfelet az Azure Cognitive Search szolgáltatáshoz. A [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) osztály tulajdonságai határozzák meg, hogy mely mezők kereshetők és visszaadásra kerülnek az eredmények között, a egyezések száma, valamint az, hogy a rendszer felhasználja-e a zavaros egyezést 

Az automatikus kiegészítéshez a zavaros egyezés csak egy kihagyott vagy rossz helyen lévő módosítási távolságra korlátozódik. Vegye figyelembe, hogy az automatikus kiegészítési lekérdezésekben a zavaros egyezés esetenként váratlan eredményeket eredményezhet az index méretétől és a szilánkok mennyiségétől függően. További információ: [particionálási és](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)skálázási fogalmak.

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

A SuggestAsync függvény két paramétert fogad el, amelyek meghatározzák, hogy a találatok visszaadása vagy a homályos megfeleltetés a keresési kifejezés bemenetén kívül történik-e. Akár nyolc egyezés is szerepelhet a javasolt eredmények között. A metódus létrehoz egy [SuggestOptions objektumot](/dotnet/api/azure.search.documents.suggestoptions), amelyet a rendszer továbbít az ajánlott API-nak. A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

## <a name="autocomplete"></a>Automatikus kiegészítés

Eddig a Search UX-kód a javaslatokra van központosítva. A következő kódrészlet az automatikus kiegészítést jeleníti meg, amely a XDSoft jQuery felhasználói felületének automatikus kiegészítési funkciója, amely az Azure Cognitive Search automatikus kiegészítésére vonatkozó kérést küld. Ahogy a javaslatok esetében is, egy C#-alkalmazásban a felhasználói interakciót támogató kód az **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Automatikus kiegészítési függvény

Az automatikus kiegészítés a [AutocompleteAsync metóduson](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)alapul. Ahogy a javaslatok esetében, ez a kódrészlet a **HomeController. cs** fájlban is elérhető.

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Az automatikus kiegészítési függvény a keresési kifejezés bemenetét veszi igénybe. A metódus létrehoz egy [AutoCompleteParameters objektumot](/rest/api/searchservice/autocomplete). A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

## <a name="next-steps"></a>Következő lépések

Ezeket a hivatkozásokat követve megtekintheti a keresési lehetőségekkel kapcsolatos teljes körű útmutatást és kódot. A minta a javaslatok és az automatikus kiegészítések hibrid megvalósítását mutatja be együtt.

+ A [Keresés hozzáadása egy webhelyhez (JavaScript)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) egy nyílt forráskódú javaslatokat tartalmazó csomagot használ a részleges kifejezés befejezéséhez az ügyfélalkalmazás számára.
+ [Oktatóanyag: az első alkalmazás létrehozása a c#-ban (3. lecke)](tutorial-csharp-type-ahead-and-suggestions.md) és a hozzá tartozó  [c#-kód mintája: Azure-Search-DotNet-Samples/Create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) a typeahead natív támogatásának bemutatása.