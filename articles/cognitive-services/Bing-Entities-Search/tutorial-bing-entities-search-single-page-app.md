---
title: 'Oktatóanyag: Bing Entity Search egyoldalas webalkalmazás'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag bemutatja, hogyan használható a Bing Entity Search API egy egyoldalas webalkalmazásban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f2c15221268635ca1892a9292d5b0c208c13dd34
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426807"
---
# <a name="tutorial-single-page-web-app"></a>Oktatóanyag: Egyoldalas webalkalmazás

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Entity Search API-val *entitásokról* és *helyekről* kereshet információkat a weben. Egy adott lekérdezésben kérheti a helyeket, az entitásokat vagy mindkettőt. A helyek és entitások definícióját az alábbiakban találja.

| Eredmény | Description |
|-|-|
|Entitások|Ismert személyek, helyek és dolgok, amelyeket/akiket név alapján talál meg|
|Helyek|Éttermek, szállodák és egyéb helyi vállalkozások, amelyeket név *vagy* típus alapján talál meg (olasz éttermek)|

Ebben az oktatóanyagban létrehozunk egy egyoldalas webalkalmazást, amely a Bing Entity Search API-val jeleníti meg a keresési eredményeket közvetlenül az oldalon. Az alkalmazás HTML-, CSS- és JavaScript-összetevőkből áll.

Az API-val hely szerint állíthatja fontossági sorrendbe az eredményeket. Egy mobilalkalmazásban magától az eszköztől kérdezheti le a helyét. Egy webalkalmazásban használhatja a `getPosition()` függvényt. Ez a hívás azonban csak biztonságos környezetben működik, és nem mindig biztosít pontos helyet. Az is lehet, hogy a felhasználó a saját helyétől eltérő helyeken szeretne entitásokat keresni.

Az alkalmazás ezért meghívja a Bing Térképek szolgáltatást, amelyből megszerzi a felhasználó által megadott hely szélességi és hosszúsági koordinátáit. A felhasználó megadhatja egy nevezetesség nevét („Space Needle”), vagy egy teljes vagy részleges címet („New York City”), és a Bing Térképek API megadja a koordinátákat.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> A lap alján található JSON- és HTTP-fejlécek a JSON-válasz és a HTTP-kérés adatait mutatják, ha rájuk kattint. Ezek a részletek hasznosak, ha meg szeretne ismerkedni a szolgáltatással.

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:

> [!div class="checklist"]
> * Bing Entity Search API-hívás indítása a JavaScriptben
> * Bing Térképek `locationQuery` API-hívás indítása a JavaScriptben
> * Keresési beállítások továbbítása az API-hívásoknak
> * Keresési eredmények megjelenítése
> * A Bing-ügyfélazonosító és az API előfizetői kulcsok kezelése
> * Az esetlegesen előforduló problémák kezelése

Az oktatóanyag oldala teljesen önálló; nem használ semmilyen külső keretrendszert, stíluslapot vagy képfájlt. Egyedül széles körben támogatott JavaScript nyelvi funkciókat használ, és az összes nagyobb webböngésző aktuális verziójával kompatibilis.

Ebben az oktatóanyagban a forráskódnak csak egyes részeit fogjuk megtárgyalni. A teljes forráskód elérhető [egy külön oldalon](). Másolja a forráskódot egy szövegszerkesztőbe, és mentse `bing.html` néven.

> [!NOTE]
> Ez az oktatóanyag nagyrészt hasonlít az [egyoldalas Bing Web Search alkalmazás oktatóanyagához](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), de csak az entitásokra vonatkozó keresési eredményekkel foglalkozik.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy követni tudja az oktatóanyagot, előfizetési kulcsokra van szüksége a Bing Search API-hoz és a Bing Maps API-hoz. 

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel:
  * <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Hozzon létre egy Bing Search erőforrás "  target="_blank"> létrehozásához Bing Search erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Hozzon létre egy Computer Vision-erőforrást, "  target="_blank"> és hozzon létre egy Bing Maps-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.

## <a name="app-components"></a>Alkalmazás-összetevők

Mint minden egyoldalas webalkalmazás, ez az oktatóalkalmazás is három részből áll:

> [!div class="checklist"]
> * HTML – Meghatározza az oldal szerkezetét és tartalmát
> * CSS – Meghatározza az oldal megjelenését
> * JavaScript – Meghatározza az oldal viselkedését

Ez az oktatóanyag nem fedi le részletesen a HTML és CSS többségét, mivel ezek egyértelműek.

A HTML tartalmazza a keresési űrlapot, amelyen a felhasználó megad egy lekérdezést, és kiválasztja a keresési beállításokat. Az űrlap a JavaScripthez van csatlakoztatva, amely tulajdonképpen végrehajtja a keresést a `<form>` címke `onsubmit` attribútumával:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Az `onsubmit` kezelő `false` értéket ad vissza, ami megakadályozza az űrlap elküldését a kiszolgálóra. A JavaScript-kód tulajdonképpen begyűjti a szükséges adatokat az űrlapból, és végrehajtja a keresést.

A keresés két fázisban történik. Először, ha a felhasználó megadott egy helymegkötést, a rendszer egy Bing Térképek-lekérdezéssel koordinátákká alakítja át. A lekérdezésre érkező visszahívás ezután elindítja a Bing Entity Search-lekérdezést.

A HTML azokat a részlegeket (HTML `<div>` címkéket) is tartalmazza, amelyekben a keresési eredmények megjelennek.

## <a name="managing-subscription-keys"></a>Előfizetői kulcsok kezelése

> [!NOTE]
> Az alkalmazáshoz a Bing Search API és a Bing Térképek API előfizetési kulcsaira is szükség van.

Annak érdekében, hogy a Bing Search és a Bing Térképek API előfizetői kulcsait ki lehessen hagyni a kódból, a böngésző állandó tárolójában tároljuk a kulcsokat. Ha a rendszer nem tárolta valamelyik kulcsot, akkor rákérdezünk, és tároljuk későbbi használatra. Ha az API később elutasítja a kulcsot, akkor érvénytelenítjük a tárolt kulcsot, ezért a következő keresésnél újra rá kell kérdeznünk a felhasználónál.

Meghatározzuk a `storeValue` és `retrieveValue` függvényeket, amelyek vagy a `localStorage` objektumot használják (ha a böngésző támogatja), vagy egy cookie-t. A `getSubscriptionKey()` függvény a felhasználó kulcsának tárolására és lekérésére használja ezeket a függvényeket. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

A `<body>` HTML-címke tartalmaz egy `onload` attribútumot, amely a lap betöltődése után meghívja a `getSearchSubscriptionKey()` és `getMapsSubscriptionKey()` függvényeket. Ezek a hívások azt a célt szolgálják, hogy ha a felhasználó még nem adta meg a kulcsait, akkor a hívások azonnal kérik őket.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Keresési beállítások kiválasztása

![[Bing Entity Search-űrlap]](media/entity-search-spa-form.png)

A HTML-űrlap a következő vezérlőket tartalmazza:

| Szabályozás | Description |
|-|-|
|`where`|Legördülő menü a kereséshez használt piac (hely és nyelv) kiválasztásához.|
|`query`|Szövegmező a keresőkifejezések megadásához.|
|`safe`|Jelölőnégyzet, amely jelzi, hogy a SafeSearch be van-e kapcsolva (korlátozza a „felnőtteknek szánt” tartalmakat)|
|`what`|Menü, amelyből kiválaszthatja, hogy entitásokat, helyeket vagy mindkettőt szeretne keresni.|
|`mapquery`|Szöveges mező, amelyben a felhasználó megadhat egy részleges vagy teljes címet, egy látnivalót stb., hogy ennek segítségével a Bing Entity Search relevánsabb eredményeket jeleníthessen meg.|

> [!NOTE]
> A helyekre vonatkozó eredmények jelenleg csak az Egyesült Államokban érhetők el. A `where` és `what` menük kódja tartalmazza a korlátozás kényszerítését. Ha egy nem egyesült államokbeli piacon a `what` menüben ki van a Helyek beállítás van kiválasztva, a `what` Bármi értékre változik. Ha a Helyek lehetőséget választja, miközben egy nem Egyesült Államokbeli piac van kiválasztva a `where` menüben, a `where` Egyesült Államok értékre változik.

A `bingSearchOptions()` JavaScript-függvény részleges lekérdezési sztringgé alakítja ezeket a mezőket a Bing Search API számára.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

A SafeSearch funkció értéke például `strict`, `moderate` vagy `off` lehet. Ezek közül a `moderate` az alapértelmezett. A mi űrlapunk viszont egy jelölőnégyzetet használ, amelynek csak két állapota van. A JavaScript-kód ezt a beállítást `strict` vagy `off` értékké alakítja (a `moderate` nem használatos).

A `bingSearchOptions()` nem kezeli a `mapquery` mezőt, mert a rendszer ezt a Bing Térképek helylekérdezésekben használja, nem a Bing Entity Searchnél.

## <a name="obtaining-a-location"></a>Hely koordinátáinak beszerzése

A Bing Maps API egy [ `locationQuery` metódust](//msdn.microsoft.com/library/ff701711.aspx)kínál, amelyet a felhasználó által megadott hely szélességének és hosszúságának megkeresésére használunk. Ezeket a koordinátákat ezután a Bing Entity Search API-nak továbbítjuk a felhasználó kérésével együtt. A keresési eredmények a fontossági sorrendben előbbre helyezik azokat a helyeket, amelyek a megadott helyhez közel találhatók.

A Bing Térképek API nem érhető el egy átlagos `XMLHttpRequest` lekérdezéssel egy webalkalmazásban, mert a szolgáltatás nem támogatja az eltérő eredetű lekérdezéseket. Szerencsére viszont támogatja a JSONP-t (a „P” a „padded”, azaz kitöltött szót jelöli). A JSONP-válasz egy függvényhívásba ágyazott, átlagos JSON-válasz. A kérelem létrehozásához a rendszer egy `<script>` címkét szúr be a dokumentumba. (A szkriptek betöltését nem befolyásolják a böngésző biztonsági szabályzatai.)

A `bingMapsLocate()` függvény a lekérdezéshez létrehozza és beilleszti a `<script>` címkét. A lekérdezési sztring `jsonp=bingMapsCallback` szegmense megadja a válasszal meghívandó függvény nevét.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Ha a Bing Maps API nem válaszol, a rendszer soha nem hívja meg a `bingMapsCallBack()` függvényt. Ez normál esetben azt jelentené, hogy a rendszer nem hívja meg a `bingEntitySearch()` függvényt, és az entitáskeresés eredményei nem jelennek meg. A helyzet elkerülése érdekében a `bingMapsLocate()` egy időzítőt is beállít, amely öt másodperc után meghívja a `bingEntitySearch()` függvényt. A visszahívási függvényben van egy logika, amely nem engedi, hogy az entitáskeresés kétszer fusson le.

A lekérdezés befejezése után a rendszer a kérés szerint meghívja a `bingMapsCallback()` függvényt.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

A szélességi és hosszúsági koordináták mellett a Bing Entity Search-lekérdezésnek szüksége van egy *sugárra* is, amely a helyinformáció pontosságát jelzi. A sugarat a Bing Térképek válaszában megadott *határolókeret* alapján számítja ki a rendszer. A határolókeret egy téglalap, amely az egész helyet körülöleli. Ha egy felhasználó például megadja a `NYC` helyet, a válaszban benne lesznek nagyjából New York közepének koordinátái, valamint a várost körülölelő határolókeret. 

Először kiszámítjuk az elsődleges koordináták távolságát a határolókeret egyes sarkaitól való távolságot a `haversineDistance()` függvény használatával (itt nem látható). Ezek közül a távolságok közül a legnagyobbat használjuk sugárként. A legkisebb sugár egy kilométer lehet. A rendszer ugyanezt az értéket használja, ha a válaszban nincs meghatározva határolókeret.

A koordináták és a sugár ismeretében ezután meghívjuk a `bingEntitySearch()` függvényt, amely elvégzi a tulajdonképpeni keresést.

## <a name="performing-the-search"></a>Keresés végrehajtása

A lekérdezés, a hely, egy beállítási sztring és az API-kulcs ismeretében a `BingEntitySearch()` függvény végrehajtja a Bing Entity Search-kérést.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

A HTTP-kérés sikeres befejezése esetén a JavaScript meghívja a `load` eseménykezelőnket, a `handleBingResponse()` függvényt, hogy kezeljen egy, az API-hoz intézett sikeres HTTP GET kérést. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Egy sikeres HTTP-kérés *nem* feltétlenül jelenti azt, hogy maga a keresés is sikeres volt. Ha a keresési műveletben hiba merül fel, a Bing Entity Search API visszaad egy nem 200-as értékű HTTP-állapotkódot, és belefoglalja a hibára vonatkozó információkat a JSON-válaszba. Továbbá, ha a kérés sebessége korlátozva volt, az API egy üres választ ad vissza.

Az előző két függvény kódjainak nagy része a hibakezelésért felel. A következő fázisoknál léphetnek fel hibák:

|Fázis|Lehetséges hiba vagy hibák|Kezelő|
|-|-|-|
|JavaScript-kérésobjektum létrehozása|Érvénytelen URL-cím|`try`/`catch` blokk|
|Kérés végrehajtása|Hálózati hibák, megszakított kapcsolatok|`error` és `abort` eseménykezelők|
|Keresés végrehajtása|Érvénytelen kérés, érvénytelen JSON, sebességkorlátok|tesztek a `load` eseménykezelőben|

A hibák kezelése a `renderErrorMessage()` meghívásával történik a hibával kapcsolatos ismert részletek megadásával. Ha a válasz hibatesztek teljes skáláját továbbítja, meghívjuk a `renderSearchResults()` függvényt, hogy megjelenítse a keresési eredményeket az oldalon.

## <a name="displaying-search-results"></a>Keresési eredmények megjelenítése

A Bing Entity Search API számára [adott sorrendben kell megjelenítenie az eredményeket](../bing-web-search/use-display-requirements.md). Mivel az API két különböző választ adhat vissza, nem elég, ha a JSON-válasz legfelső szintjén `Entities` vagy `Places` gyűjteményén keresztül ismétli meg ezeket az eredményeket. (Ha csak egy típusú eredményt szeretne megkapni, használja a `responseFilter` lekérdezési paramétert.)

Ehelyett a `rankingResponse` gyűjteményt használjuk a keresési eredményeknél a megjelenített eredmények rendezéséhez. Ez az objektum az `Entitiess` és/vagy a `Places` gyűjtemények elemeire mutat.

A `rankingResponse` legfeljebb három keresési eredménygyűjteményt tartalmazhat, amelyek a következők: `pole`, `mainline` és `sidebar`. 

Ha jelen van, akkor a `pole` a legrelevánsabb keresési eredmény, és jól láthatóan kell megjeleníteni. A `mainline` a keresési eredmények nagyját teszi ki. A fő eredményeket közvetlenül a `pole` után kell megjeleníteni (vagy elsőként, ha a `pole` nincs jelen). 

Végül A `sidebar` a kiegészítő keresési eredményeket jelenti. Ezeket megjelenítheti egy tényleges oldalsávon, vagy egyszerűen a fő eredmények után. Az oktatóanyag alkalmazásánál az utóbbi mellett döntöttünk.

A `rankingResponse` gyűjtemények minden eleme a valós keresési eredményekre mutat két különböző, de egyenértékű módon.

| Elem | Leírás |
|-|-|
|`id`|Az `id` úgy néz ki, mint egy URL, de nem célszerű hivatkozásokhoz használni. A rangsoroló eredmények `id` típusa megegyezik egy válaszgyűjtemény keresési eredményeinek eleméhez *vagy* egy teljes válaszgyűjteményhez (mint például `Entities`) tartozó `id` típussal.
|`answerType`<br>`resultIndex`|Az `answerType` azt a legfelső szintű válaszgyűjteményt jelenti, amely az eredményt tartalmazza (például `Entities`). A `resultIndex` az eredmény adott gyűjteményen belüli indexét jelenti. Ha a `resultIndex` kimarad, a rangsorolási eredmény az egész gyűjteményre vonatkozik.

> [!NOTE]
> További információ a keresési válasz ezen részéről: [Eredmények rangsorolása](rank-results.md).

Bármelyik módszert használhatja, amellyel alkalmazása könnyebben megtalálja a hivatkozott keresési eredményt. Az oktatóanyag kódjában mi az `answerType` és a `resultIndex` használatával keressük meg az egyes keresési eredményeket.

Végezetül ideje, hogy vessünk egy pillantást a `renderSearchResults()` függvényre. Ez a függvény a keresési eredmények három szakaszát képviselő három `rankingResponse` gyűjteményen fut le. Minden szakaszban meghívjuk a `renderResultsItems()` függvényt az adott szakasz eredményeinek leképezéséhez.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Eredményelemek renderelése

A JavaScript-kódban a `searchItemRenderers` objektum *leképezőket* is tartalmazhat, olyan függvényeket, amelyek minden típusú keresési eredményhez HTML-kódot hoznak létre.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

A leképező függvények a következő paramétereket fogadhatják el:

| Paraméter | Leírás |
|-|-|
|`item`|A JavaScript-objektum, amely az elem tulajdonságait tartalmazza, például az URL-címét és a leírását.|
|`index`|Az eredményelem indexe a saját gyűjteményén belül.|
|`count`|Az eredményelem gyűjteményében található elemek száma.|

Az `index` és `count` paraméterek használhatók a találatok megszámozására, egy gyűjtemény elején vagy végén egy speciális HTML létrehozására, egy bizonyos számú elem utáni sortörés beszúrására és így tovább. Ha egy leképezőnek nincs szüksége erre a funkcióra, akkor nem kell elfogadnia ezt a két paramétert. Ami azt illeti, az oktatóanyag alkalmazásában nem is használjuk őket a leképezőknél.

Vizsgáljuk meg alaposabban az `entities` renderelőt:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Az entitásleképező függvény:

> [!div class="checklist"]
> * Létrehozza az `<img>` HTML-címkét a képminiatűr megjelenítéséhez, ha van. 
> * Létrehozza a képet tartalmazó oldalra hivatkozó `<a>` HTML -címkét.
> * Létrehozza a leírást, amely információkat jelenít meg a képről és a képet tartalmazó oldalról.
> * Magában foglalja az entitás besorolását a megjelenített tippekkel, ha vannak.
> * Tartalmaz egy Bing-keresésre mutató hivatkozást, ahol további információt talál az entitásról.
> * Megjeleníti az adatforrások számára szükséges összes licencelési vagy forráskövetési információt.

## <a name="persisting-client-id"></a>Ügyfél-azonosító megőrzése

A Bing Search API-k válaszai tartalmazhatnak egy `X-MSEdge-ClientID` fejlécet, amelyet egymást követő kérésekkel vissza kell küldeni az API-nak. Ha több Bing Search API-t is használ, mindegyikhez ugyanazt az ügyfél-azonosítót használja, ha lehetséges.

A `X-MSEdge-ClientID` fejléc megadása lehetővé teszi, hogy a Bing API-k az összes felhasználó keresését hozzárendelik, amelyek két fontos előnnyel rendelkeznek.

Egyrészt lehetővé teszi, hogy a Bing keresőmotorja korábbi kontextusokat is alkalmazzon a keresésekhez olyan találatok megjelenítése érdekében, amelyek jobban megfelelnek a felhasználó igényeinek. Ha például a felhasználó korábban vitorlázáshoz kapcsolódó kifejezésekre keresett rá, egy későbbi keresés a „dokkok” kifejezésre nagy valószínűséggel a vitorlások kikötésére alkalmas dokkokkal kapcsolatos információkat fog eredményezni.

Másrészt a Bing véletlenszerűen kiválaszthat felhasználókat, hogy új funkciókat próbálhassanak ki, mielőtt azok széles körben elérhetővé válnának. Ha minden kéréshez ugyanaz az ügyfél-azonosító van megadva, akkor azok a felhasználók, akik ki lettek választva egy funkció használatára, mindig látják azt. Az ügyfél-azonosító nélkül a felhasználó azt tapasztalhatja, hogy egy funkció látszólag véletlenszerűen hol megjelenik, hol eltűnik a keresési eredményeknél.

A böngészők biztonsági szabályzatai (CORS) megakadályozhatják, hogy a JavaScript hozzáférjen az `X-MSEdge-ClientID` fejléchez. Ez a korlátozás akkor léphet életbe, ha a keresési válasz eredete különbözik az azt lekérő oldalétól. Éles környezetben egy olyan kiszolgálóoldali szkript futtatásával oldhatja fel a szabályzat okozta korlátozást, amely a weboldaléval megegyező tartományból hívja meg az API-t. Mivel a szkript eredete megegyezik a weboldaléval, az `X-MSEdge-ClientID` fejléc elérhető lesz a JavaScript számára.

> [!NOTE]
> Éles webalkalmazásban a kérést ettől függetlenül is kiszolgálói oldalról érdemes végrehajtani. Ellenkező esetben a weboldalnak tartalmaznia kell a Bing Search API-kulcsot, ahol a forrást megtekintők is hozzáférhetnek. Az API előfizetési kulcsával történő összes használatért Ön fizet, még az illetéktelen felek által létrehozott kérésekért is, ezért fontos, hogy a kulcsot ne tegye elérhetővé.

Fejlesztési célokból a Bing Web Search API-kérést egy CORS-proxyn keresztül is végrehajthatja. Az ilyen proxytól kapott válasz `Access-Control-Expose-Headers` fejléce lehetővé teszi, hogy a felsorolja a válasz fejléceit, és elérhetővé tegye azokat a JavaScript számára.

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
> [Bing Entity Search API-referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing Maps API-dokumentáció](//msdn.microsoft.com/library/dd877180.aspx)