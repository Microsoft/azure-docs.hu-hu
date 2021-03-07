---
title: 'Oktatóanyag: egyoldalas Webalkalmazás létrehozása a Bing News Search API használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel az Oktatóanyaggal létrehozhat egy egyoldalas webalkalmazást, amely képes keresési lekérdezéseket küldeni a Bing News API-nak, és megjeleníti az eredményeket a weboldalon belül.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: cb9e68efd27deb3bf66d3c286c0cd7a128d8bf59
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430581"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Oktatóanyag: egylapos webes alkalmazás létrehozása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing News Search API lehetővé teszi az interneten való keresést és a keresési lekérdezésnek megfelelő hírtípus szerinti eredmények lekérését. Ebben az oktatóanyagban létrehozunk egy egyoldalas webalkalmazást, amely a Bing News Search API-t használja a keresési eredmények megjelenítéséhez az oldalon. Az alkalmazás HTML-, CSS- és JavaScript-összetevőkből áll. A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> A lap alján található JSON- és HTTP-fejlécek a JSON-válasz és a HTTP-kérés adatait mutatják, ha rájuk kattint. Ezek a részletek hasznosak lehetnek, ha meg szeretne ismerkedni a szolgáltatással.

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:
> [!div class="checklist"]
> * Bing News Search API-hívás indítása a JavaScriptben
> * Keresési beállítások továbbítása a Bing News Search API-nak
> * A keresési eredményül kapott híreket négy kategóriából (bármilyen, üzleti, egészséggel kapcsolatos vagy politikai), az elmúlt 24 órára, az elmúlt hétre vagy elmúlt hónapra korlátozódó, vagy minden elérhető időtartományt magában foglaló időkeretből jelenítheti meg.
> * Keresési eredmények közötti lapozás
> * A Bing-ügyfélazonosító és az API előfizetői azonosító kezelése
> * Az esetlegesen előforduló problémák kezelése

Az oktatóanyag oldala teljesen önálló; nem használ semmilyen külső keretrendszert, stíluslapot vagy képfájlt. Egyedül széles körben támogatott JavaScript nyelvi funkciókat használ, és az összes nagyobb webböngésző aktuális verziójával kompatibilis.


## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy követni tudja az oktatóanyagot, előfizetési kulcsokra van szüksége a Bing Search API-hoz. Ha nem rendelkezik ezekkel, létre kell hoznia őket:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" hozzon létre egy Bing Search erőforrást, "  target="_blank"> és hozzon létre egy Bing Search-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.

## <a name="app-components"></a>Alkalmazás-összetevők
Mint minden egyoldalas webalkalmazás, ez az oktatóalkalmazás is három részből áll:

> [!div class="checklist"]
> * HTML – Meghatározza az oldal szerkezetét és tartalmát
> * CSS – Meghatározza az oldal megjelenését
> * JavaScript – Meghatározza az oldal viselkedését

A HTML és a CSS nagy része hagyományos, ezért az oktatóanyag részeként nem vesszük át őket. A HTML tartalmazza a keresési űrlapot, amelyen a felhasználó megad egy lekérdezést, és kiválasztja a keresési beállításokat. Az űrlap a JavaScripthez van csatlakoztatva, amely ténylegesen végrehajtja a keresést a `<form>` címke `onsubmit` attribútumával:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
Az `onsubmit` kezelő `false` értéket ad vissza, ami megakadályozza az űrlap elküldését a kiszolgálóra. A JavaScript-kód begyűjti a szükséges adatokat az űrlapból, és végrehajtja a keresést.

A HTML azokat a részlegeket (HTML `<div>` címkéket) is tartalmazza, amelyekben a keresési eredmények megjelennek.

## <a name="managing-subscription-key"></a>Előfizetői azonosító kezelése

Annak érdekében, hogy a Bing Search API előfizetői azonosítóját ki lehessen hagyni a kódból, a böngésző állandó tárolójában tároljuk az azonosítót. Mielőtt az azonosítót eltárolnánk, elkérjük a felhasználó azonosítóját. Ha az azonosítót később elutasítja az API, érvénytelenítjük a tárolt azonosítót, ezért a felhasználótól újra el kell kérnünk az övét.

Meghatározzuk a `storeValue` és `retrieveValue` függvényeket, amelyek vagy a `localStorage` objektumot használják (amelyet nem minden böngésző támogat), vagy egy cookie-t. A `getSubscriptionKey()` függvény a felhasználó azonosítójának tárolására és lekérésére használja ezeket a függvényeket. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
Egy HTML `<form>` címke, az `onsubmit` meghívja a `bingWebSearch` függvényt, hogy visszaadja a keresési eredményeket. A `bingWebSearch` minden lekérdezés hitelesítéséhez a `getSubscriptionKey()` függvényt használja. Ahogy az előző definíció is mutatja, a `getSubscriptionKey` elkéri a felhasználótól az azonosítót, ha az még nem lett megadva. A rendszer ezután eltárolja az azonosítót az alkalmazás által további használatra.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása
A következő ábrán a lekérdezési szövegmező és egy iskolafinanszírozási hírekkel kapcsolatos keresést meghatározó beállítások láthatók.

![A Bing News Search beállításai](media/news-search-categories.png)

A HTML-űrlap a következő nevű elemeket tartalmazza:

|Elem|Leírás|
|-|-|
| `where` | Legördülő menü a kereséshez használt piac (hely és nyelv) kiválasztásához. |
| `query` | Szövegmező a keresőkifejezések megadásához. |
| `category` | Jelölőnégyzetek az eredmények bizonyos típusainak előléptetéséhez. Az Egészség előléptetése esetén például magasabb lesz az egészségügyi hírek besorolása. |
| `when` | Legördülő menü a keresés opcionális, az elmúlt napra, hétre vagy hónapra történő korlátozásához. |
| `safe` | Jelölőnégyzet, amellyel megadható, hogy a Bing SafeSearch funkció kiszűrje-e a „felnőtteknek szánt” tartalmakat. |
| `count` | Rejtett mező. A kérésenként visszaadandó keresési eredmények száma. A módosításával oldalanként kevesebb vagy több keresési eredmény jeleníthető meg. |
| `offset`|  Rejtett mező. Az első keresési eredmény eltolása a kérésben; a lapozást segíti elő. Új kérésnél `0` értékre van visszaállítva. |

> [!NOTE]
> A Bing Web Search más lekérdezési paramétereket is kínál, de mi ezek közül csak néhányat használunk.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

A `SafeSearch` paraméter értéke például egy tényleges API-hívásban `strict`, `moderate` vagy `off` lehet. Ezek közül a `moderate` az alapértelmezett. A mi űrlapunk viszont egy jelölőnégyzetet használ, amelynek csak két állapota van. A JavaScript-kód ezt a beállítást `strict` vagy `off` értékké alakítja (a `moderate` nem használatos).

## <a name="performing-the-request"></a>A kérés végrehajtása
A lekérdezés, a beállítási sztring és az API-kulcs ismeretében a `BingNewsSearch` függvény egy `XMLHttpRequest` objektumot használ arra, hogy kérést intézzen a Bing News Search-végponthoz.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
A HTTP-kérés sikeres befejezése esetén a JavaScript meghívja a `load` eseménykezelőjét, a `handleBingResponse()` függvényt, hogy kezeljen egy, az API-hoz intézett sikeres HTTP GET kérést. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "News") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Egy sikeres HTTP-kérés *nem* feltétlenül jelenti azt, hogy maga a keresés is sikeres volt. Ha a keresési műveletben hiba merül fel, a Bing News Search API visszaad egy nem 200-as értékű HTTP-állapotkódot, és belefoglalja a hibára vonatkozó információkat a JSON-válaszba. Továbbá, ha a kérés sebessége korlátozva volt, az API egy üres választ ad vissza.

Az előző két függvény kódjainak nagy része a hibakezelésért felel. A következő fázisoknál léphetnek fel hibák:

|Fázis|Lehetséges hiba vagy hibák|Kezelő|
|-|-|-|
|A JavaScript-kérésobjektum létrehozása|Érvénytelen URL-cím|`try`/`catch` blokk|
|Kérés végrehajtása|Hálózati hibák, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|Keresés végrehajtása|Érvénytelen kérés, érvénytelen JSON, sebességkorlátok|tesztek a `load` eseménykezelőben|

A hibák kezelése a `renderErrorMessage()` meghívásával történik a hibával kapcsolatos ismert részletek megadásával. Ha a válasz hibatesztek teljes skáláját továbbítja, meghívjuk a `renderSearchResults()` függvényt, hogy megjelenítse a keresési eredményeket az oldalon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése
A keresési eredmények megjelenítésére használatos fő függvény a `renderSearchResults()`. Ez a függvény a Bing News Search szolgáltatás által visszaadott JSON segítségével rendereli a hírtalálatokat és a kapcsolódó kereséseket, ha vannak.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
A JSON-válasz a fő keresési eredményeket a legfelső szintű `value` objektumként adja vissza. Mi elküldjük őket a `renderResults()` függvénynek, amely végighalad rajtuk, és meghív egy külön függvényt az egyes elemek HTML-be történő rendereléséhez. A kapott HTML vissza lesz adva a `renderSearchResults()` függvénynek, amelyben a lap `results` részlegébe lesz beszúrva.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
A Bing News Search API legfeljebb négy különböző típusú kapcsolódó eredményt ad vissza, mindegyiket a saját legfelső szintű objektumában. Ezek a következők:

|Kapcsolat típusa|Description|
|-|-|
|`pivotSuggestions`|Lekérdezések, amelyek az eredeti keresés egyik lecserélhető szavát egy másikra cserélik. Ha például a „piros virágok” kifejezésre keres, a „piros” egy lecserélhető szó, a „sárga virágok” pedig egy alternatív javaslat.|
|`queryExpansions`|Lekérdezések, amelyek további kifejezések hozzáadásával szűkítik az eredeti keresést. Ha például a „Microsoft Surface” kifejezésre keres, a lekérdezés egyik lehetséges kibővítése a „Microsoft Surface Pro”.|
|`relatedSearches`|Az eredeti keresőkifejezéssel is próbálkozó felhasználók további lekérdezései. Ha például a „Mount Rainier” kifejezésre keres, egy lehetséges kapcsolódó keresés lehet a „Mt. Saint Helens”.|
|`similarTerms`|Lekérdezések, amelyeknek az eredeti kereséshez hasonló jelentésük van. Ha például az „iskolák” kifejezésre keres, az „oktatás” az egyik ilyen hasonló értelmű keresőszó.|

Ahogy a `renderSearchResults()` függvényben láthatta, csak a `relatedItems` javaslatokat rendereljük, és a kapott hivatkozásokat a lap oldalsávjában helyezzük el.

## <a name="rendering-result-items"></a>Eredményelemek renderelése

A JavaScript-kódban a `searchItemRenderers` objektum *leképezőket* is tartalmaz, vagyis olyan függvényeket, amelyek minden típusú keresési eredményhez létrehoznak egy HTML-t.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Egy leképező függvény a következő paramétereket fogadja el:

|Paraméter|Leírás|
|-|-|
|`item`| A JavaScript-objektum, amely az elem tulajdonságait tartalmazza, például az URL-címét és a leírását.|
|`index`| Az eredményelem indexe a saját gyűjteményén belül.|
|`count`| Az eredményelem gyűjteményében található elemek száma.|

Az `index` és `count` paraméterek használhatók a találatok megszámozására, egy gyűjtemény elején vagy végén egy speciális HTML létrehozására, egy bizonyos számú elem utáni sortörés beszúrására és így tovább. Ha egy leképezőnek nincs szüksége erre a funkcióra, akkor nem kell elfogadnia ezt a két paramétert.

A `news` megjelenítő a következő JavaScript-részletben látható:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
A hírleképező függvény:
> [!div class="checklist"]
> * Létrehoz egy bekezdéscímkét, hozzárendeli az `news` osztályhoz, és leküldéssel továbbítja a HTML-tömbnek.
> * Kiszámítja a képminiatűr méretét (a szélesség 60 képpontban rögzített, a magasságot ezzel arányosan számítja ki).
> * Létrehozza az `<img>` HTML-címkét a képminiatűr megjelenítéséhez. 
> * Létrehozza az `<a>` HTML-címkéket, amelyek a képre és a képet tartalmazó oldalra hivatkoznak.
> * Létrehozza a leírást, amely információkat jelenít meg a képről és a képet tartalmazó oldalról.

A miniatűr méretét az `<img>` címke, illetve a miniatűr URL-címének `h` és `w` mezője is használja. A [Bing miniatűr-szolgáltatása](../bing-web-search/resize-and-crop-thumbnails.md) ezután egy pontosan ekkora méretű miniatűrt állít elő.

## <a name="persisting-client-id"></a>Ügyfél-azonosító megőrzése
A Bing Search API-k válaszai tartalmazhatnak egy `X-MSEdge-ClientID` fejlécet, amelyet egymást követő kérésekkel vissza kell küldeni az API-nak. Ha több Bing Search API-t is használ, mindegyikhez ugyanazt az ügyfél-azonosítót használja, ha lehetséges.

Az `X-MSEdge-ClientID` fejléc megadása lehetővé teszi, hogy a Bing API-k egymáshoz társítsák a felhasználó összes keresését, amelynek két fontos előnye van.

Egyrészt lehetővé teszi, hogy a Bing keresőmotorja korábbi kontextusokat is alkalmazzon a keresésekhez olyan találatok megjelenítése érdekében, amelyek jobban megfelelnek a felhasználó igényeinek. Ha például a felhasználó korábban vitorlázáshoz kapcsolódó kifejezésekre keresett rá, egy későbbi keresés a „csomó” kifejezésre nagy valószínűséggel a vitorlázásban használt csomókkal kapcsolatos információkat fog eredményezni.

Másrészt a Bing véletlenszerűen kiválaszthat felhasználókat, hogy új funkciókat próbálhassanak ki, mielőtt azok széles körben elérhetővé válnának. Ha minden kéréshez ugyanazt az ügyfél-azonosítót adja meg, akkor azok a felhasználók, akiknek megjelenik a funkció, mindig látni fogják. Az ügyfél-azonosító nélkül a felhasználó azt tapasztalhatja, hogy egy funkció látszólag véletlenszerűen hol megjelenik, hol eltűnik a keresési eredményeknél.

A böngészők biztonsági szabályzatai (CORS) megakadályozhatják, hogy a JavaScript hozzáférjen az `X-MSEdge-ClientID` fejléchez. Ez a korlátozás akkor léphet életbe, ha a keresési válasz eredete különbözik az azt lekérő oldalétól. Éles környezetben egy olyan kiszolgálóoldali szkript futtatásával oldhatja fel a szabályzat okozta korlátozást, amely a weboldaléval megegyező tartományból hívja meg az API-t. Mivel a szkript eredete megegyezik a weboldaléval, az `X-MSEdge-ClientID` fejléc elérhető lesz a JavaScript számára.

> [!NOTE]
> Éles webalkalmazásban kiszolgálói oldalról hajtsa végre a kérést. Ellenkező esetben a weboldalnak tartalmaznia kell a Bing Search API-kulcsot, ahol a forrást megtekintők is hozzáférhetnek. Az API előfizetési kulcsával történő összes használatért Ön fizet, még az illetéktelen felek által létrehozott kérésekért is, ezért fontos, hogy a kulcsot ne tegye elérhetővé.

Fejlesztési célokból a Bing Web Search API-kérést egy CORS-proxyn keresztül is végrehajthatja. Az ilyen proxytól kapott válasz `Access-Control-Expose-Headers` fejléce lehetővé teszi a válaszok fejléceit, és elérhetővé teszi őket a JavaScript számára.

CORS-proxyt könnyedén telepíthet annak érdekében, hogy oktatóalkalmazásunk hozzáférhessen az ügyfél-azonosító fejlécéhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután hajtsa végre egy parancsablakban a következő parancsot:

```console
npm install -g cors-proxy-server
```

Ezután módosítsa a HTML-fájlban lévő Bing Web Search végpontot a következőre: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Végül indítsa el a CORS-proxyt a következő paranccsal:

```console
cors-proxy-server
```

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Bing News Search API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)