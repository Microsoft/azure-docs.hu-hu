---
title: 'Oktatóanyag: Egyoldalas webalkalmazás létrehozása – Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Ez az egyoldalas alkalmazás bemutatja, hogyan használható a Bing Web Search API a releváns keresési eredmények lekérésére, feldolgozására és megjelenítésére egy egyoldalas alkalmazásban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca542ac6c5184a0307a9293fada305a6c6356edd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434168"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Oktatóanyag: Egyoldalas alkalmazás létrehozása a Bing Web Search API használatával

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez az egyoldalas alkalmazás bemutatja, hogyan kérhetők le, dolgozhatók fel és jeleníthetők meg a Bing Web Search API-ból származó keresési eredmények. Az oktatóanyag HTML- és CSS-sablonokra épül, fő témája pedig a JavaScript-kód. A [Githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) az indulásban segítő útmutatókkal együtt érhetők el HTML-, CSS- és JS-fájlok.

Ez a mintaalkalmazás a következőkre képes:

> [!div class="checklist"]
> * A Bing Web Search API meghívása keresési beállításokkal
> * Webes, képi, hír és videó-eredmények megjelenítése
> * Az eredmények oldalakra osztása
> * Előfizetési kulcsok kezelése
> * Hibakezelés

Az alkalmazás használatához szükség van egy [Azure Cognitive Services-fiókra](../cognitive-services-apis-create-account.md) a Bing Search API-k mellé.

## <a name="prerequisites"></a>Előfeltételek

Néhány dolog, amire az alkalmazás futtatásához szüksége lesz:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" hozzon létre egy Bing Search erőforrást, "  target="_blank"> és hozzon létre egy Bing Search-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.

* A Node.js 8-as vagy újabb verziója

Az első lépés az adattár klónozása a mintaalkalmazás forráskódjával.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Ez után futtassa a `npm install` parancsot. Ebben az oktatóanyagban az Express.js az egyetlen függőség.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Alkalmazás-összetevők

A mintaalkalmazás, amelyet készítünk, négy részből áll:

* `bing-web-search.js` – Az Express.js-alkalmazásunk. Ez kezeli a lekérési/válaszadási logikát és az útválasztást.
* `public/index.html` – Alkalmazásunk váza; ez definiálja, hogy hogyan jelennek meg az adatok a felhasználók számára.
* `public/css/styles.css` – Az oldal stíluselemeit, például a betűtípusokat, színeket és betűméretet definiálja.
* `public/js/scripts.js` – Tartalmazza a Bing Web Search API-hoz kéréseket készítő logikát, kezeli az előfizetési kulcsokat, kezeli és feldolgozza a válaszokat, és megjeleníti az eredményeket.

Ennek az oktatóanyagnak a fő témája a `scripts.js` fájl, valamint a Bing Web Search API meghívásához és a válaszok kezeléséhez szükséges logika.

## <a name="html-form"></a>A HTML-űrlap

Az `index.html` tartalmaz egy űrlapot, amelyen a felhasználók keresést végezhetnek, és keresési beállításokat választhatnak. Az `onsubmit` attribútum az űrlap elküldésekor aktiválódik, és meghívja a `scripts.js` fájlban definiált `bingWebSearch()` metódust. Három argumentuma van:

* Keresési lekérdezés
* Kijelölt beállítások
* Előfizetői azonosító

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Lekérdezés beállításai

A HTML-űrlap lekérdezési beállításokat tartalmaz, amelyek a [Bing Web Search API 7-es verziójában](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) lekérdezési paraméterekre lesznek leképezve. Az alábbi táblázat részletesen bemutatja, hogyan szűrhetik a felhasználók a keresési eredményeket a mintaalkalmazással:

| Paraméter | Leírás |
|-----------|-------------|
| `query` | Szövegmező a lekérdezési sztring beviteléhez. |
| `where` | Legördülő menü a piac (hely és nyelv) kiválasztásához. |
| `what` | Jelölőnégyzetek adott típusú eredmények előléptetéséhez. A képek előléptetése esetén például magasabb lesz a képek rangja a keresési eredmények között. |
| `when` | Legördülő menü, amellyel a felhasználó az adott napra, hétre vagy hónapra korlátozhatja a keresési eredményeket. |
| `safe` | Jelölőnégyzet a felnőtt tartalmat kiszűrő Bing SafeSearch engedélyezéséhez. |
| `count` | Rejtett mező. A kérésenként visszaadandó keresési eredmények száma. Ezt az értéket módosítva oldalanként kevesebb vagy több keresési eredmény jeleníthető meg. |
| `offset` | Rejtett mező. Az első keresési eredmény eltolása a kérésben a lapozás elősegítéséhez. Minden új kérésnél `0` értékre van visszaállítva. |

> [!NOTE]
> A Bing Web Search API további lekérdezési paraméterekkel támogatja a keresési eredmények finomítását. Ez a minta csak néhányat használ. Az elérhető paraméterek teljes listáját a [Bing Web Search API 7-es verzió – referencia](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) című dokumentumban találja meg.

Ezeket a beállításokat a `bingSearchOptions()` függvény konvertálja át a Bing Search API által megkövetelt formátumra.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

A `SafeSearch` beállítása `strict`, `moderate` vagy `off` lehet. A Bing Web Search alapértelmezett beállítása `moderate`. Ez az űrlap egy jelölőnégyzetet használ, amely két állapottal rendelkezik: `strict` vagy `moderate` .

Ha az **Előléptetés** jelölőnégyzetek bármelyike be van jelölve, akkor a lekérdezéshez az `answerCount` paraméter is hozzá lesz fűzve. A `promote` paraméter használata mellett az `answerCount` is kötelező. Ebben a kódrészletben `9` értékre van beállítva, hogy minden elérhető eredménytípus vissza legyen adva.
> [!NOTE]
> Egy eredménytípus előléptetése nem *garantálja*, hogy az szerepelni fog a keresési eredmények között. Az előléptetés csak az ilyen típusú eredmények rangját növeli a szokásos rangjukhoz képest. A keresések egy adott eredménytípusra korlátozásához a `responseFilter` lekérdezési paraméter használható, vagy meghívható egy olyan meghatározott végpont, mint a Bing Image Search vagy a Bing News Search.

A `textDecoration` és a `textFormat` paraméter a szkript kötött része. Ezek emelik ki félkövéren a keresőkifejezést a keresési eredményekben. Ezek nem kötelező paraméterek.

## <a name="manage-subscription-keys"></a>Előfizetési kulcsok kezelése

Ez a mintaalkalmazás egy böngésző állandó tárolójában helyezi el az Bing Search API-előfizetési kulcsot, hogy azt ne kelljen beépíteni a kódba. Ha nincs tárolt előfizetési kulcs, akkor a rendszer a felhasználót kéri meg, hogy adjon meg egyet. Ha az API visszautasítja az előfizetési kulcsot, a rendszer megkéri a felhasználót, hogy adja meg újra.

A `getSubscriptionKey()` függvény a felhasználó előfizetési kulcsának tárolására és lekérésére használja a `storeValue` és `retrieveValue` függvényt. Ezek a függvények, ha az támogatott, a `localStorage` objektumot használják, egyébként cookie-kat.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Korábban már volt szó arról, hogy az űrlap beküldésekor aktiválódik a `bingWebSearch`-t meghívó `onsubmit`. Ez a függvény inicializálja és küldi el a kérést. A kérés hitelesítéséhez minden beküldésekor meg lesz hívva a `getSubscriptionKey`.

## <a name="call-bing-web-search"></a>A Bing Web Search hívása

A lekérdezés, a beállítás-sztring és az előfizetési kulcs ismeretében a `BingWebSearch` függvény létrehoz egy `XMLHttpRequest` objektumot a Bing Web Search-végpont hívásához.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Sikeres kérést követően aktiválódik a `load` eseménykezelő, és meghívja a `handleBingResponse` függvényt. A `handleBingResponse` feldolgozza az eredményobjektumot, megjeleníti az eredményeket, és hibakezelési logikát is tartalmaz a sikertelen kérésekhez.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Egy HTTP-kérés sikere *nem* jelenti azt, hogy maga a keresés is sikeres volt. Ha a keresési műveletben hiba merül fel, a Bing Web Search API visszaad egy nem 200-as értékű HTTP-állapotkódot, és belefoglalja a hibára vonatkozó információkat a JSON-válaszba. Ha a kérés sebessége korlátozva volt, az API üres választ ad vissza.

Az előző két függvény kódjainak nagy része a hibakezelésért felel. A következő fázisoknál léphetnek fel hibák:

| Fázis | Lehetséges hiba vagy hibák | Kezelő |
|-------|--------------------|------------|
| A kérésobjektum elkészítése | Érvénytelen URL-cím | `try` / `catch` blokk |
| Kérés végrehajtása | Hálózati hibák, megszakított kapcsolatok | `error` és `abort` eseménykezelők |
| Keresés végrehajtása | Érvénytelen kérés, érvénytelen JSON, sebességkorlátok | Tesztek a `load` eseménykezelőben |

A hibák a `renderErrorMessage()` meghívásával vannak kezelve. Ha a válasz mindegyik hibateszten megfelel, a `renderSearchResults()` függvény lesz meghívva a keresési eredmények megjelenítéséhez.

## <a name="display-search-results"></a>Keresési eredmények megjelenítése

A Bing Web Search API által visszaadott eredményekre [felhasználási és megjelenítési követelmények](./use-display-requirements.md) érvényesek. Mivel egy választ többféle eredménytípust tartalmazhat, nem elég a legfelső szintű `WebPages` gyűjteményen végiglépkedni. A mintaalkalmazás a `RankingResponse` függvényt használja az eredmények kívánt módon való rendezésére.

> [!NOTE]
> Ha egyetlen eredménytípust szeretne kapni, használhatja a `responseFilter` lekérdezési paramétert, vagy megfontolhatja egy másik Bing Search-végpont, például a Bing Image Search használatát.

Minden válaszhoz tartozik egy `RankingResponse` objektum, amely legfeljebb három gyűjteményt tartalmazhat: `pole`, `mainline` és `sidebar`. A `pole`, ha jelen van, a leginkább releváns eredmény, és kiemelten kell megjeleníteni. A `mainline` tartalmazza a legtöbb keresési eredményt, és közvetlenül a `pole` után jelenik meg. A `sidebar` kiegészítő keresési eredményeket tartalmaz. Ezek az eredmények lehetőség szerint az oldalsávon jelenítendők meg. Ha a képernyőterület korlátai miatt az oldalsáv nem jól használható, akkor ezeknek az eredményeknek a `mainline` eredmények után kell megjelenniük.

Minden `RankingResponse` tartalmaz egy `RankingItem` tömböt, amely az eredmények rendezési módját adja meg. Mintaalkalmazásunk az `answerType` és a `resultIndex` paraméter használatával azonosítja az eredményt.

> [!NOTE]
> Az eredmények más módokon is azonosíthatók és rangsorolhatók. További információ: [Eredmények megjelenítése rangsorolással](rank-results.md).

Vizsgáljuk meg a kódot:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

A `renderResultsItems()` függvény sorra veszi az egyes `RankingResponse` gyűjtemények elemeit, minden rangsorolási eredményt leképez egy keresési eredményre az `answerType` és a `resultIndex` értéke alapján, majd meghívja a megfelelő renderelő függvényt a HTML előállításához. Ha egy elemhez nincs megadva a `resultIndex`, a `renderResultsItems()` végiglépked minden adott típusú eredményen, és minden elemhez meghívja a renderelő függvényt. Az így előállított HTML az `index.html` megfelelő `<div>` elemébe lesz beszúrva.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>A renderelő függvények áttekintése

Mintaalkalmazásunkban a `searchItemRenderers` objektum magában foglalja a keresési eredmények típusaihoz HTML-t generáló függvényeket.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> A mintaalkalmazás weboldalakhoz, hírekhez, képekhez, videókhoz és kapcsolódó keresésekhez is rendelkezik renderelőkkel. Az alkalmazásnak minden olyan eredménytípushoz szüksége van egy renderelőre, ezek pedig számítások, helyesírási javaslatok, entitások, időzónák és definíciók is lehetnek.

Néhány renderelő függvény csak az `item` paramétert fogadja el. Mások további paramétereket is elfogadnak, amelyek az elemeknek a környezettől függő, másféle rendereléséhez használhatók. Az ilyen információt nem használó renderelőknek ezeket a paramétereket nem kell elfogadniuk.

A környezeti argumentumok a következők:

| Paraméter  | Leírás |
|------------|-------------|
| `section` | Az eredményeknek az a szakasza (`pole`, `mainline` vagy `sidebar`), amelyben az elem megjelenik. |
| `index`<br>`count` | Akkor érhető el, ha a `RankingResponse` elem megadja, hogy egy adott gyűjtemény összes eleme megjelenítendő; ellenkező esetben `undefined`. Az elem gyűjteményen belüli indexe és a gyűjteményben lévő elemek teljes száma. Ez az információ felhasználható az eredmények számozására, eltérő HTML generálására az első és az utolsó HTML-hez, és sok másra is. |

Ugyanebben az alkalmazásban az `images` és a `relatedSearches` renderelő is környezeti argumentumokat használ a generált HTML testre szabására. Vizsgáljuk meg alaposabban az `images` renderelőt:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

A képrenderelő:

* Kiszámítja a kép miniatűrjének méretét (a szélesség változó, a magasság mindig 60 képpont).
* Beszúrja a képtalálatot megelőző HTML-t a környezet alapján.
* Létrehozza a képet tartalmazó oldalra hivatkozó `<a>` HTML -címkét.
* Létrehozza az `<img>` HTML-címkét a képminiatűr megjelenítéséhez.

A képrenderelő a `section` és az `index` változó alapján, a helytől függő módon jeleníti meg az eredményeket. Az oldalsávon a képtalálatok közé sortörés (`<br>` címke) van beszúrva, hogy a képek az oldalsávon egy oszlopban jelenjenek meg. Más szakaszokban az első képtalálatot `(index === 0)` egy `<p>` címke előzi meg.

A miniatűr méretét az `<img>` címke, illetve a miniatűr URL-címének `h` és `w` mezője is használja. A `title` és az `alt` attribútum (a kép szöveges leírásai) a kép nevéből, és az URL-beli állomásnévbál van előállítva.

Az alábbi példa a képek mintaalkalmazásbeli megjelenését mutatja be:

![[Bing képtalálatok]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Az ügyfélazonosító megőrzése

A Bing Search API-k válaszai tartalmazhatnak egy `X-MSEdge-ClientID` fejlécet, amelyet az egymást követő kérések mindegyikével vissza kell küldeni az API-nak. Ha az alkalmazás több Bing Search API-t is használ, akkor fontos, hogy az összes szolgáltatás összes kérésével ugyanaz az ügyfélazonosító legyen elküldve.

Az `X-MSEdge-ClientID` fejléc megadása lehetővé teszi, hogy a Bing API-k egymáshoz társítsák a felhasználó összes keresését. Ez lehetővé teszi, hogy a Bing keresőmotorja korábbi kontextusokat is alkalmazzon a keresésekhez olyan találatok megjelenítése érdekében, amelyek jobban megfelelnek a kérésnek. Ha például a felhasználó korábban vitorlázáshoz kapcsolódó kifejezésekre keresett rá, egy későbbi keresés a „csomó” kifejezésre nagy valószínűséggel a vitorlázásban használt csomókkal kapcsolatos információkat fog eredményezni. Másrészt a Bing véletlenszerűen kiválaszthat felhasználókat, hogy új funkciókat próbálhassanak ki, mielőtt azok széles körben elérhetővé válnának. Ha minden kéréshez ugyanaz az ügyfél-azonosító van megadva, akkor azok a felhasználók, akik ki lettek választva egy funkció használatára, mindig látni fogják azt. Az ügyfél-azonosító nélkül a felhasználó azt tapasztalhatja, hogy egy funkció látszólag véletlenszerűen hol megjelenik, hol eltűnik a keresési eredményeknél.

Az olyan böngészőbiztonsági-szabályzatok, mint az Eltérő eredetű erőforrások megosztása (CORS) megakadályozhatják, hogy a mintaalkalmazás hozzáférjen az `X-MSEdge-ClientID` fejléchez. Ez a korlátozás akkor léphet életbe, ha a keresési válasz eredete különbözik az azt lekérő oldalétól. Éles környezetben egy olyan kiszolgálóoldali szkript futtatásával oldhatja fel a szabályzat okozta korlátozást, amely a weboldaléval megegyező tartományból hívja meg az API-t. Mivel a szkript eredete megegyezik a weboldaléval, az `X-MSEdge-ClientID` fejléc elérhető lesz a JavaScript számára.

> [!NOTE]
> Éles webalkalmazásban a kérést ettől függetlenül is kiszolgálói oldalról érdemes végrehajtani. Ellenkező esetben a weboldalnak tartalmaznia kell a Bing Search API előfizetési kulcsot, ahol a forrást megtekintők is hozzáférhetnek. Az API előfizetési kulcsával történő összes használatért Ön fizet, még az illetéktelen felek által létrehozott kérésekért is, ezért fontos, hogy a kulcsot ne tegye elérhetővé.

Fejlesztési célokból egy CORS-proxyn keresztül is küldhet kérést. Az ilyen típusú proxytól kapott válasz `Access-Control-Expose-Headers` fejléce a válaszok fejléceit szűri, és elérhetővé teszi őket a JavaScript számára.

CORS-proxyt könnyedén telepíthet annak érdekében, hogy mintaalkalmazásunk hozzáférhessen az ügyfél-azonosító fejlécéhez. Futtassa ezt a parancsot:

```console
npm install -g cors-proxy-server
```

Következő lépésként írja át a Bing Web Search-végpontot a `script.js` fájlban a következőre:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Indítsa el a CORS-proxyt az alábbi paranccsal:

```console
cors-proxy-server
```

Ne zárja be a parancsablakot, amíg használja az mintaalkalmazást; az ablak bezárása leállítja a proxyt. A keresési eredmények alatti, kibontható HTTP-fejlécek szakaszban meg kell jelennie az `X-MSEdge-ClientID` fejlécnek. Fontos, hogy ez minden kéréshez ugyanaz legyen.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Bing Web Search API 7-es verzió – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)