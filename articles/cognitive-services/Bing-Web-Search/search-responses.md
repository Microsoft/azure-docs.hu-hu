---
title: A Bing Web Search API válasz struktúra és válasz típusa
titleSuffix: Azure Cognitive Services
description: Ismerje meg a válasz típusát és a Bing Web Search API által használt válaszokat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 47a573fd4ed4d058b9f9a265c8774f0856371b94
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390100"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>A Bing Web Search API válasz struktúra és válasz típusa  

Ha egy keresési kérelmet küld a Bing Web Search, akkor adja vissza egy [ `SearchResponse` ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objektum a válasz törzsében. Az objektum, a Bing volt a lekérdezés minden válasz mező tartalmazza. Ebben a példában egy Válaszobjektum mutatja be, ha a Bing minden választ ad vissza:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Általában a Bing webes keresés a válaszok egy részhalmazát adja vissza. Például, ha a lekérdezési kifejezés volt *hajózási dinghies*, a válasz tartalmazhat `webPages`, `images`, és `rankingResponse`. Kivéve, ha használt már [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) weblapok kiszűréséhez, a válasz mindig tartalmazza a `webpages` és `rankingResponse` válaszokat.

## <a name="webpages-answer"></a>Weblapok válasz

A [weblapok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) választ, amelyek a Bing webes keresés határozza meg, voltak-e a lekérdezés a mutató hivatkozások listáját tartalmazza. Minden egyes [weblap](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) a lista tartalmazza: a lap neve, URL-címét, meg az URL-CÍMÉT, a tartalmat, és a Bing a tartalom található dátum rövid leírását.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Használat `name` és `url` egy hivatkozás, amely végigvezeti a felhasználót a képernyőn látható weblapon létrehozásához.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Képek válasz

A [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) válasz Bing úgy Gondoltuk, is a lekérdezést a rendszerképek listáját tartalmazza. Minden egyes [kép](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) a lista tartalmazza az URL-címét a lemezképet, annak méretét, a dimenziók és a kódolási formátum. A képobjektum a kép miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

A felhasználó eszközén, attól függően, általában jeleníti meg, a miniatűrök, melyre a felhasználó számára egy részhalmazát [oldalon keresztül](paging-webpages.md) a fennmaradó lemezképeket.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

A miniatűrt ki is bonthatja, ha a felhasználó fölé viszi az egérmutatót. Ügyeljen arra, hogy kibontáskor megjelenítse a kép forrását. Például oly módon, a tároló `hostPageDisplayUrl` és türközve legyen az kép alatt. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](./resize-and-crop-thumbnails.md) foglalkozó szakaszt.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Ha a felhasználó rákattint a miniatűr, `webSearchUrl` a felhasználót, hogy a Bing keresési eredményeket megjelenítő lap lemezképek, a képek egy kollázsra tartalmazó érvénybe.

A kép válasz és képek részleteiért lásd: [Image Search API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Kapcsolódó keresések válasz

A [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) válasz más felhasználók által végzett a legnépszerűbb kapcsolódó lekérdezések listáját tartalmazza. Minden egyes [lekérdezés](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) a lista tartalmazza a lekérdezési karakterlánc (`text`), egy lekérdezési karakterláncot a találatok kiemelése karakter (`displayText`), és a egy URL-címet (`webSearchUrl`) a Bing keresési eredmények oldalát az adott lekérdezés.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Használja a `displayText` lekérdezési karakterlánc és a `webSearchUrl` URL-címet, amely a felhasználót, hogy a Bing search hivatkozás létrehozása oldal, a kapcsolódó lekérdezés eredménye. Is használhatja a `text` lekérdezési karakterlánc a saját webes keresési API-lekérdezés, és megjeleníti az eredményeket.

További információ a kiemelését jelölők kezelése `displayText`, lásd: [találatok kiemelése](./hit-highlighting.md).

Az alábbiakban látható egy példa a kapcsolódó lekérdezések használatát a Bing.com.

![Kapcsolódó keresések erre példa a Bingen](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videók válasz

A [videók](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) válasz videókat a Bing úgy Gondoltuk, is a lekérdezést a listáját tartalmazza. Minden egyes [videó](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) a lista tartalmazza az URL-címét a videót, az időtartam, a dimenziók és a kódolási formátum. A videóobjektum a videó miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

A felhasználó eszközén, attól függően, általában jeleníti meg, a videókat, melyre a felhasználó számára a fennmaradó videók egy részét. A videó hossza, a videó miniatűrjét jeleníthető meg a leírást (név) és tesznek elérhetővé; ilyenek (közzétevő).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Ahogy a felhasználó rámutat a miniatűr használhatja `motionThumbnailUrl` lejátszani a videót egy miniatűr verzióját. Ügyeljen arra, hogy megjelenítse a mozgó miniatűr forrását.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a következő videómegtekintési lehetőségek állnak rendelkezésre:

- Használat `hostPageUrl` a videó megtekintéséhez a gazdagép-webhelyen (például YouTube-on)
- Használat `webSearchUrl` a videó megtekintéséhez a Bing video böngészőben
- Használat `embedHtml` a videó beágyazása a saját felhasználói élmény

A videó válasz és videók kapcsolatos részletekért lásd: [Video Search API](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Hírek válasz

A [hírek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) választ, amely a Bing úgy Gondoltuk, is a lekérdezést a hírek listáját tartalmazza. A lista minden [cikke](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) tartalmazza a cikk nevét, leírását és a cikket tartalmazó eredeti webhely URL-címét. Ha a cikk tartalmaz képet, az objektum tartalmazza a kép miniatűrjét.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Attól függően, a felhasználó eszközén egy részét a hírek, melyre a felhasználó a fennmaradó cikkek jeleníthető meg. Használja a `name` és az `url` mezőket egy hivatkozás létrehozásához, amely átirányítja a felhasználót a cikkhez az eredeti webhelyen. Ha a cikk tartalmaz egy képet, ellenőrizze a kattintható rendszerképpel `url`. Ügyeljen arra, hogy megjelenítse a cikk forrását a `provider` használatával.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

További hírek válasz és hírek kapcsolatos információkért lásd: [News Search API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Számítási válasz

Ha a felhasználó egy kifejezésnek vagy egység átalakítás lekérdezést ad, a válasz tartalmazhat egy [számítási](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) választ. A `computation` válasz tartalmazza a normalizált kifejezés és az eredményt.

Egység átalakítás lekérdezés egy lekérdezést, amely egy egység alakítja át egy másik. Például *10 méterben hány feet?* vagy *hány evőkanállá egy 1 és 4 Cup?*

Az alábbi látható a `computation` így *10 méterben hány feet?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

A következő példát matematikai lekérdezéseket és a kapcsolódó `computation` válaszokat.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

A kifejezésnek a következő szimbólumokat tartalmazhatja:

|Szimbólum|Leírás|
|------------|-----------------|
|+|Emellett|
|-|Kivonás|
|/|körzet|
|*|Szorzás|
|^|Energiagazdálkodási|
|!|Faktoriális|
|.|Decimal|
|()|Sorrend-csoportosítás|
|[]|Függvény|

A kifejezésnek a következő állandókat tartalmazhatnak:

|Szimbólum|Leírás|
|------------|-----------------|
|Pi|3.14159...|
|Párhuzamossági|Párhuzamossági|
|I|Képzeletbeli száma|
|e|e, 2.71828...|
|GoldenRatio|Hamisított Kerberos arány, 1.61803...|

Matematikai kifejezés tartalmazhatja a következő funkciókat:

|Szimbólum|Leírás|
|------------|-----------------|
|Rendezés|Négyzetgyök|
|Sin[x], Cos[x], Tan[x]<br />CSC [x], [x], mp Cot [x]|Trigonometriai függvények (argumentumokkal kifejezve)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc [x], [x], ArcSec ArcCot [x]|Más néven inverz trigonometriai függvények (eredményt adó kifejezve)|
|Exp [x], E ^ x|Exponenciális függvény|
|Log[x]|Természetes alapú logaritmus|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch [x], [x], Sech Coth [x]|Hiperbolikus függvények|
|ArcSinh [x], [x], ArcCosh ArcTanh [x]<br />ArcCsch [x], [x], ArcSech ArcCoth [x]|Más néven Inverz hiperbolikus függvények|

(A példában, 4 x + 6 = 18., ahol az x funkciófrissítés változó) változókat tartalmazó matematikai kifejezések használata nem támogatott.

## <a name="timezone-answer"></a>Időzóna-válasz

Ha a felhasználó sikeresen megadja egy idő- vagy lekérdezést, a válasz tartalmazhat egy [időzóna](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) választ. Ez a válasz explicit vagy implicit lekérdezéseket támogatja. Az implicit lekérdezést például *eldöntve is ez?* , adja vissza a helyi idő a felhasználó földrajzi helye alapján. Egy explicit lekérdezés például *eldöntve budapesti?* , Seattle, WA adja vissza a helyi idő.

A `timeZone` választ biztosít a megadott helyen a helyen, a jelenlegi UTC szerinti dátuma és ideje nevét, és az UTC eltolás. Ha a hely határain belül több időzónában van, a válasz tartalmazza az aktuális UTC szerinti dátuma és időpontja belül az összes időzóna. Például két időzóna Florida állapot alá tartozik, mert a válasz tartalmazza a helyi dátum és idő mindkét időzónákat.  

Ha a lekérdezési kérelmek az idő, egy állam vagy ország/régió, Bing meghatározza, hogy a hely földrajzi határ elsődleges város, és adja vissza a a `primaryCityTime` mező. Ha a határt több időzónákat tartalmaz, a fennmaradó időzónák rendszer adja vissza a `otherCityTimes` mező.

Ez a példa bemutatja, hogy a visszaadandó lekérdezi a `timeZone` választ.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion válasz

Ha a Bing határozza meg, hogy a felhasználó lehet, hogy rendelkezik szánt más kereséséhez, a válasz tartalmazza a [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) objektum. Például, ha a felhasználó keres *carlos toll*, a Bing határozhatja meg, hogy a felhasználó nagy valószínűséggel célja ehelyett Carlos Pena keresése (a mások által az elmúlt keresések alapján *carlos toll*). Az alábbiakban látható egy példa helyesírás választ.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

A következő bemutatja, hogyan Bing használja-e a helyesírási javaslatot.

![A Bing helyesírás-ellenőrzés javaslat példa](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="response-headers"></a>Válaszfejlécek

A Bing Web Search API érkező válaszokat a következő fejléceket tartalmazza:

|||
|-|-|
|`X-MSEdge-ClientID`|A Bing a felhasználó rendelkezik hozzárendelt egyedi azonosítója|
|`BingAPIs-Market`|A piacon, amellyel a kérés teljesítéséhez|
|`BingAPIs-TraceId`|A Bing API-kiszolgálón (a támogatási) a kérés a naplóbejegyzés|

Különösen fontos megőrizni az ügyfél-Azonosítót, és küldje vissza a későbbi kérelmeket. Ha így tesz, a Keresés a környezetben, a keresési eredmények rangsorolása korábbi, és egységes felhasználói élmény is biztosítanak.

Azonban amikor a Bing Web Search API hívása JavaScript, a böngésző beépített biztonsági funkciókat (CORS) előfordulhat, hogy megakadályozzák a fér hozzá a következő fejlécek értékeit.

A fejlécek eléréséhez, hogy a Bing Web Search API-kérelem CORS proxyn keresztül. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Egyszerű, hogy a CORS-proxy telepítéséhez a [oktatóanyag alkalmazása](tutorial-bing-web-search-single-page-app.md) eléréséhez a választható ügyféltanúsítvány-fejléceket. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Majd adja meg a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a Bing Web Search API-végpont lévő a HTML-fájl:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Éles környezetben válaszfejlécek

Az előző válasz leírt CORS proxy megközelítés a fejlesztés, tesztelés és tanulás a megfelelő.

Éles környezetben egy kiszolgálóoldali parancsfájl ugyanabban a tartományban, mint a weblapot, a Bing Web Search API használó kell üzemelteti. Ez a szkript kell a weblap JavaScript-alapú kérésre API-hívásokat, és adja át az összes eredmény, beleértve a fejléceket, az ügyfélnek. A két erőforrás (oldal és parancsfájl) oszt meg egy eredeti adatforrást, mert a CORS nincs használatban, és a speciális fejlécek elérhetők a JavaScript a weblapon.

Ez a megközelítés is védelmet nyújt az API-kulcsot a nyilvános való kitettség mivel csak a kiszolgálóoldali parancsprogram szükség van. A szkript egy másik módszer segítségével ellenőrizze, hogy a kérés engedélyezett.

A következő bemutatja, hogyan Bing használja-e a helyesírási javaslatot.

![A Bing helyesírás-ellenőrzés javaslat példa](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>További lépések  

* Felülvizsgálat [kérelemszabályozás](throttling-requests.md) dokumentációját.  

## <a name="see-also"></a>Lásd még  

* [A Bing Web Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
