---
title: 'Oktatóanyag: Kép feltöltése – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Lebontja a kép a Bingbe való feltöltésének folyamatát, melynek során a rendszer megállapításokat ad vissza a képről, majd elemzi és megjeleníti a választ.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: scottwhi
ms.openlocfilehash: 84a8219221525400a9d3241c2f183d24344c2f6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689177"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Oktatóanyag: Képek feltöltése a Bing Visual Search API-ba

A Bing Visual Search API lehetővé teszi, hogy a feltöltött képekhez hasonló képeket keressen az interneten. Az oktatóanyag segítségével hozzon létre egy webalkalmazást, amely képes küldeni egy képet az API-ba, és megjeleníti a weblapon belül visszaadott elemzéseket. Vegye figyelembe, hogy ez az alkalmazás nem felel meg az API használatához vonatkozó összes [Bing-használati és megjelenítési követelménynek.](../bing-web-search/use-display-requirements.md)

A minta teljes forráskódját további hibakezeléssel és jegyzetekkel találja a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html)

Az oktatóanyag részeként összeállított alkalmazás a következők bemutatására szolgál:

> [!div class="checklist"]
> * Kép feltöltése a Bing Visual Search API-ba
> * Képkeresési eredmények megjelenítése webes alkalmazásban
> * Fedezze fel az API által biztosított különböző elemzéseket

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>A weblap létrehozása és strukturálása

Hozzon létre egy HTML-lapot, amely képet küld a Bing Visual Search API-nak, elemzéseket kap, és megjeleníti azokat. A kedvenc szerkesztővagy IDE, hozzon létre egy fájlt nevű "uploaddemo.html". Adja hozzá a következő alapvető HTML-struktúrát a fájlhoz:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Ossza fel az oldalt egy kérelemszakaszra, ahol a felhasználó megadja a kéréshez szükséges összes információt, és egy válaszszakaszt, ahol az elemzések megjelennek. Adja hozzá `<div>` a `<body>`következő címkéket a hoz. A `<hr>` címke vizuálisan elválasztja a kérés szakaszt a válaszszakasztól:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Adjon `<script>` hozzá egy `<head>` címkét a címkéhez, amely tartalmazza az alkalmazás JavaScript-jét:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>A feltöltési fájl beszerezése

Ha azt szeretné, hogy a felhasználó kijelöljön egy feltöltendő képet, az alkalmazás a `<input>` címkét használja, amelynek típusattribútuma a . `file` A felhasználói felületnek egyértelművé kell tennie, hogy az alkalmazás a Bing segítségével kapja meg a keresési eredményeket.

Adja hozzá `<div>` a `requestSection` `<div>`következőt a hoz. A bemeneti fájl egyetlen képet fogad el, amely bármilyen típusú lehet (például .jpg, .gif, .png). Az `onchange` esemény megadja a kezelőt, amelyet a rendszer akkor hív meg, amikor a felhasználó kiválaszt egy fájlt.

A `<output>` címke a kijelölt kép miniatűrjének megjelenítésére szolgál:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Fájlkezelő létrehozása

Hozzon létre egy kezelőfüggvényt, amely képes olvasni a feltölteni kívánt képen. Az `FileList` objektumban lévő fájlok on-át történő iteráció közben a kezelőnek meg kell győződnie arról, hogy a kijelölt fájl képfájl, és mérete legkisebb. Ha a kép nagyobb, a feltöltés előtt csökkentenie kell a méretét. Végül a kezelő megjeleníti a kép miniatűrjét:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Előfizetési kulcs hozzáadása és tárolása

Az alkalmazásnak előfizetési kulcsra van szüksége a Bing Visual Search API hívásához. Ebben az oktatóanyagban a felhasználói felületen fogja megadni. Adja hozzá `<input>` a következő címkét (a szöveghez `<body>` beállított type attribútummal) a fájl címkéjéhez: `<output>`

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

A képés az előfizetési kulcs segítségével a Bing Visual Search hívásával betekintést nyerhet a képbe. Ebben az oktatóanyagban a hívás`en-us`az alapértelmezett piacot ( ) és a biztonságos keresési értéket (`moderate`) használja.

Ez az alkalmazás rendelkezik ezen értékek módosítására. Adja hozzá `<div>` a következőt az előfizetési kulcs `<div>`alatt. Az alkalmazás `<select>` egy címkét használ, hogy legördülő listát adjon a piaci és biztonságos keresési értékekhez. Mindkét lista megjeleníti az alapértelmezett értéket.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Keresési beállítások hozzáadása a weblaphoz

Az alkalmazás elrejti a listákat egy összecsukható, `<div>` amely a Lekérdezési beállítások hivatkozással szabályozott. Ha a Lekérdezési beállítások `<div>` hivatkozásra kattint, a kibontások kibővülnek, így láthatja és módosíthatja a lekérdezési beállításokat. Ha ismét a Lekérdezési beállítások `<div>` hivatkozásra kattint, az összecsukódik, és rejtve marad. A következő kódrészlet a Lekérdezési `onclick` beállítások hivatkozás kezelőjét mutatja. A kezelő határozza `<div>` meg, hogy a ki van-e bontva vagy össze csukva van-e. Adja hozzá ezt `<script>` a kezelőt a szakaszhoz. A kezelőt a demó összes összecsukható `<div>` szakasza használja.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Hívja `onclick` a kezelőt

Adja hozzá `"Get insights"` a következő `<div>` gombot a testben lévő lehetőségek alatt. A gomb lehetővé teszi a hívás kezdeményezését. Amikor a gombra kattint, a kurzor a forgó várakozási `onclick` kurzorra változik, és a kezelő neve meg lesz hívva.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Adja hozzá a `onclick` gomb `handleQuery()` kezelőjét a `<script>` címkéhez.

## <a name="handle-the-query"></a>A lekérdezés kezelése

A `handleQuery()` kezelő biztosítja, hogy az előfizetési kulcs jelen legyen, és 32 karakter hosszú legyen, és hogy egy kép ki legyen jelölve. Eltávolítja az előző lekérdezésből származó megállapításokat is. Ezt követően meghívja a `sendRequest()` funkciót a hívás kezdeményezéséhez.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

A `sendRequest()` függvény formázza a végpont `Ocp-Apim-Subscription-Key` URL-címét, beállítja a fejlécet az előfizetési kulcshoz, hozzáfűzi a feltöltendő kép bináris részét, megadja a válaszkezelőt, és kezdeményezi a hívást:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Az API-válasz beszerezni és kezelni

A `handleResponse()` függvény kezeli a Bing Visual Search hívásból érkező választ. Ha a hívás sikeres, elemzi a JSON-választ az egyes címkéknek megfelelően, amelyek a megállapításokat tartalmazzák. Ezután hozzáadja a keresési eredményeket az oldalhoz. Az alkalmazás ezután `<div>` minden címkéhez létrehoz egy összecsukható támagot, hogy megtudja oldani, hogy mennyi adat jelenjen meg. Adja hozzá a `<script>` kezelőt a szakaszhoz.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>A válasz feldolgozása

A `parseResponse` függvény a JSON-választ szótárobjektummá alakítja `json.tags`át a segítségével történő iterációval.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Címkeszakasz létrehozása

A `buildTagSections()` függvény végighalad az elemzési JSON-címkéken, és meghívja a `buildDiv()` függvényt, hogy minden egyes címkéhez hozzon létre egy `<div>` függvényt. Minden címke hivatkozásként jelenik meg. Amikor a hivatkozásra kattintanak, a címke kitágul, és megjelenik a címkéhez társított elemzési adatok. Ha ismét a hivatkozásra kattint, a szakasz összecsukódik.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>A keresési eredmények megjelenítése a weblapon

A `buildDiv()` függvény `addDivContent` meghívja a függvényt, hogy az `<div>`egyes tagok összecsukható tartalmának felépítéséhez.

A címke tartalmazza a címkéhez tartozó válaszból származó JSON-t. Kezdetben csak a JSON első 100 karaktere jelenik meg, de a JSON-karakterláncra kattintva megjelenítheti az összes JSON-t. Ha ismét rákattint, a JSON-sztring összecsukódik, és ismét csak 100 karakter látható.

Következő lépésként adja hozzá a címkében található művelettípusokat. Minden művelettípushoz hívja meg a megfelelő függvényeket az elemzések hozzáadásához:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Elemzési adatok megjelenítése különböző műveletekhez

A következő függvények különböző műveletek elemzési adatait jelenítik meg. A funkciók vagy egy kattintható képet vagy kattintható linket biztosítanak, amely egy weboldalra küldi, amely további információt tartalmaz a képről. Ez az oldal vagy házigazdája Bing.com vagy a kép eredeti honlapján. Nem minden az insights adatai jelennek meg ebben az alkalmazásban. Az összes rendelkezésre álló mező megtekintéséhez tekintse meg a [Képek – Vizuális keresés](https://aka.ms/bingvisualsearchreferencedoc) hivatkozást.

> [!NOTE]
> Az oldalon meg kell jelenítenie az elemzési adatok at. További információkért tekintse meg a [Bing Search API használatára és megjelenítési követelményeire vonatkozó követelményeket.](../bing-web-search/use-display-requirements.md)

### <a name="relatedimages-insights"></a>RelatedImages elemzések

A `addRelatedImages()` függvény létrehoz egy címet a kapcsolódó képet üzemeltető webhelyek mindegyikéhez, a műveletek listájának iterációjával, `RelatedImages` és egy `<img>` címkét fűzve a külső `<div>` höz mindegyikhez:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>OldalakBeleértve az elemzéseket

A `addPagesIncluding()` funkció létrehoz egy hivatkozást a feltöltött képet üzemeltető webhelyek hez `PagesIncluding` a műveletek listájának `<img>` iterációjával, és egy címkét fűz a külső `<div>` höz mindegyikhez:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches elemzések

A `addRelatedSearches()` függvény létrehoz egy hivatkozást a webhely otthont a képet, iteráció révén a `RelatedSearches` műveletek listáját, és hozzáfűzve egy `<img>` címkét a külső `<div>` minden:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Receptek elemzése

A `addRecipes()` függvény létrehoz egy hivatkozást minden egyes receptek által `Recipes` visszaadott iteráció `<img>` révén `<div>` a műveletek listáját, és hozzáfűzve egy címkét a külső minden:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Vásárlási elemzések

A `addShopping()` függvény létrehoz egy hivatkozást a visszaadott vásárlási `RelatedImages` eredményekhez a műveletek `<img>` listájának `<div>` mentálásával, és egy címkét fűz a külső höz mindegyikhez:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>A termékek betekintése

A `addProducts()` függvény a `Products` visszaküldött termékek eredményéhez hivatkozást hoz létre a műveletek `<img>` listájának `<div>` segítségével, és mindegyikhez hozzáfűz egy címkét a külső höz:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult elemzések

A `addTextResult()` függvény a képen felismert szöveget jeleníti meg:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

A `addEntity()` függvény egy hivatkozást jelenít meg, amely a felhasználót Bing.com, ahol a képen található entitástípussal kapcsolatos részleteket kaphat, ha a rendszer észlelte az okat:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

A `addImageWithWebSearchUrl()` funkció egy kattintható `<div>` képet jelenít meg, amely a felhasználót a keresési eredményekhez viszi Bing.com:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>CSS-stílus hozzáadása

A weblap `<style>` elrendezésének `<head>` rendszerezéséhez adja hozzá a következő szakaszt a címkéhez:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
> [Oktatóanyag: Hasonló képek keresése a korábbi keresésekről az ImageInsightsToken használatával](./tutorial-visual-search-insights-token.md)
