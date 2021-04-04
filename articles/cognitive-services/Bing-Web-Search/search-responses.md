---
title: Bing Web Search API a válasz szerkezete és a válaszok típusai
titleSuffix: Azure Cognitive Services
description: Amikor keresési kérelmet küld Bing Web Search, a `SearchResponse` Válasz törzsében egy objektumot ad vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380980"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Search API a válasz szerkezete és a válaszok típusai  

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Amikor keresési kérelmet küld Bing Web Search, a [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Válasz törzsében egy objektumot ad vissza. Az objektum tartalmaz egy mezőt minden olyan válaszhoz, amelyet a Bing meghatározott a lekérdezés szempontjából. Ez a példa egy Response objektumot mutat be, ha a Bing az összes választ adta vissza:

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

A Bing Web Search általában a válaszok egy részhalmazát adja vissza. Ha például a lekérdezési kifejezés a *vitorlázás mentőcsónakokra*, a válasz a, a és a is lehet `webPages` `images` `rankingResponse` . Hacsak nem használta a [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) a weblapok kiszűrésére, a válasz mindig tartalmazza a `webpages` és a `rankingResponse` válaszokat.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>A weblapok válasza

A [Weboldalok](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) válasza tartalmazza a lekérdezéssel kapcsolatban Bing Web Search weblapokra mutató hivatkozások listáját. A lista minden [weboldala](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) tartalmazni fogja a következőt: az oldal neve, URL-címe, MEGJELENÍTett URL-címe, a tartalom rövid leírása, valamint a Bing megtalálta a tartalmat.

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

A `name` és a használatával `url` hozzon létre egy hiperhivatkozást, amely a felhasználót a weblapra viszi.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>A képek válasza

A [lemezképek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) válasza tartalmazza azoknak a lemezképeknek a listáját, amelyeket a Bing gondolt a lekérdezés szempontjából. A listában szereplő összes [rendszerkép](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) tartalmazza a rendszerkép URL-címét, a méretét, a hozzá tartozó dimenziókat és a kódolási formátumát. A képobjektum a kép miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

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

A felhasználó eszközének függvényében általában a miniatűrök egy részhalmazát jeleníti meg, amely a felhasználó számára a többi rendszerképen [keresztüli lapra](./paging-search-results.md) mutat.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

A miniatűrt ki is bonthatja, ha a felhasználó fölé viszi az egérmutatót. Ügyeljen arra, hogy kibontáskor megjelenítse a kép forrását. Ha például Kinyeri a gazdagépet a `hostPageDisplayUrl` -ból, és megjeleníti azt a rendszerkép alatt. A miniatűrök átméretezésével kapcsolatos információkért lásd a [miniatűrök átméretezésével és körülvágásával](./resize-and-crop-thumbnails.md) foglalkozó szakaszt.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a használatával `webSearchUrl` felveheti a felhasználót a rendszerképek keresésének eredményei lapra, amely a képek kollázsát tartalmazza.

A képválasztó és a lemezképek részleteiért lásd: [Image Search API](../bing-image-search/overview.md).

## <a name="related-searches-answer"></a>Kapcsolódó keresések válasza

A [relatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) válasz tartalmazza a más felhasználók által készített legnépszerűbb kapcsolódó lekérdezések listáját. A listában szereplő összes [lekérdezés](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) tartalmaz egy lekérdezési karakterláncot ( `text` ), egy lekérdezési karakterláncot a találatok kiemelésével ( `displayText` ), valamint egy URL-címet ( `webSearchUrl` ) a Bing keresési eredmények oldalához a lekérdezéshez.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

A `displayText` lekérdezési karakterlánc és az `webSearchUrl` URL-cím használatával hozzon létre egy hiperhivatkozást, amely a felhasználót a kapcsolódó lekérdezés Bing Search Results lapjára viszi. Használhatja a `text` lekérdezési karakterláncot a saját Web Search API-lekérdezésében is, és saját maga is megjelenítheti az eredményeket.

További információ a kiemelési jelölők kezeléséről `displayText` : [találatok kiemelése](../bing-web-search/hit-highlighting.md).

Az alábbi példa a Bing.com-ben kapcsolódó lekérdezések használatát mutatja be.

![Kapcsolódó keresések – példa a Bingre](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videók válasza

A [videók](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) válasza azon videók listáját tartalmazza, amelyeket a Bing gondolt a lekérdezés szempontjából. A lista minden [videója](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) tartalmazza a videó URL-címét, annak időtartamát, a hozzá tartozó dimenziókat és a kódolási formátumát. A videóobjektum a videó miniatűrjének URL-címét, illetve a miniatűr méreteit is tartalmazza.

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

A felhasználó eszközének függvényében általában a videók egy részhalmazát jeleníti meg egy olyan lehetőséggel, amellyel a felhasználó megtekintheti a többi videót. Megjelenítheti a videó miniatűrjét a videó hosszával, a leírással (név) és a hozzárendeléssel (kiadó).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Ahogy a felhasználó a miniatűr fölé viszi, a `motionThumbnailUrl` videó miniatűr verzióját is használhatja. Ügyeljen arra, hogy megjelenítse a mozgó miniatűr forrását.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Ha a felhasználó a miniatűrre kattint, a következő videómegtekintési lehetőségek állnak rendelkezésre:

- A `hostPageUrl` (z) használatával megtekintheti a gazdagép webhelyén található videót (például YouTube)
- A használatával `webSearchUrl` megtekintheti a videót a Bing videó böngészőben
- `embedHtml`A videó saját felhasználói élményben való beágyazására használható

A videó válaszával és a videókkal kapcsolatos részletekért lásd: [Video Search API](../bing-video-search/overview.md).

## <a name="news-answer"></a>Hírek válasza

A [Hírek](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) válasza tartalmazza azon Hírek listáját, amelyeket a Bing gondolt a lekérdezésre. A lista minden [cikke](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) tartalmazza a cikk nevét, leírását és a cikket tartalmazó eredeti webhely URL-címét. Ha a cikk tartalmaz képet, az objektum tartalmazza a kép miniatűrjét.

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

A felhasználó eszköztől függően megtekintheti a cikkek egy részhalmazát, amely lehetőséget biztosít a felhasználó számára a fennmaradó cikkek megtekintésére. Használja a `name` és az `url` mezőket egy hivatkozás létrehozásához, amely átirányítja a felhasználót a cikkhez az eredeti webhelyen. Ha a cikk tartalmaz egy rendszerképet, a képfájlt a használatával lehet kijelölni `url` . Ügyeljen arra, hogy megjelenítse a cikk forrását a `provider` használatával.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

A hírek megválaszolásával és cikkeivel kapcsolatos részletekért lásd: [News Search API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Számítási válasz

Ha a felhasználó egy matematikai kifejezést vagy egy egység átalakítási lekérdezést ír be, a válasz [számítási](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) választ tartalmazhat. A `computation` Válasz a normalizált kifejezést és annak eredményét tartalmazza.

Az egység-átalakítási lekérdezés egy olyan lekérdezés, amely átalakítja az egyik egységet egy másikra. Például hány *láb van 10 méterben?* vagy hány *evőkanál egy 1/4-kupában?*

Az alábbiakban a `computation` *10 méteres számú láb közül* a válasz látható?

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Az alábbiakban a matematikai lekérdezésekre és a hozzájuk kapcsolódó kérdésekre mutatunk példákat `computation` .

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

A matematikai kifejezések a következő szimbólumokat tartalmazhatják:

|Szimbólum|Leírás|
|------------|-----------------|
|+|Összeadás|
|-|Kivonás|
|/|Részleg|
|*|Szorzás|
|^|Energiaellátás|
|!|Faktoriális|
|.|Tizedesjegy|
|()|Sorrend csoportosítása|
|[]|Függvény|

A matematikai kifejezések a következő állandókat tartalmazhatják:

|Szimbólum|Leírás|
|------------|-----------------|
|Pi|3,14159...|
|Fokú|Fokú|
|i|Képzeletbeli szám|
|e|e, 2,71828...|
|GoldenRatio|Arany arány, 1,61803...|

A matematikai kifejezések a következő függvényeket tartalmazhatják:

|Szimbólum|Leírás|
|------------|-----------------|
|Rendezés|Szögletes gyökér|
|Sin [x], cos [x], Tan [x]<br />CSC [x], MP [x], gyermekágy [x]|Trigonometriai függvények (radiánban megadott argumentumokkal)|
|ArcSin [x], ArcCos [x], ArcTan [x]<br />ArcCsc [x], ArcSec [x], ArcCot [x]|Inverz trigonometriai függvények (radiánban adja meg az eredményt)|
|Exp [x], E ^ x|Exponenciális függvény|
|Napló [x]|Természetes alapú logaritmus|
|Sinh [x], COSH [x], TANH [x]<br />Csch [x], Sech [x], Coth [x]|Hiperbolikus függvények|
|ArcSinh [x], ArcCosh [x], ArcTanh [x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Inverz hiperbolikus függvények|

A változókat tartalmazó matematikai kifejezések (például 4x + 6 = 18, ahol az x változó) nem támogatottak.

## <a name="timezone-answer"></a>Időzóna-válasz

Ha a felhasználó időpontra vagy dátumra vonatkozó lekérdezést ad meg, a válasz [időzóna](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) -választ is tartalmazhat. Ez a válasz implicit vagy explicit lekérdezéseket támogat. Egy implicit lekérdezés, például *mennyi idő van?*, a felhasználó tartózkodási helye alapján adja vissza a helyi időt. Egy explicit lekérdezés, például hogy *milyen idő van Seattle-ben?*, a helyi időt adja vissza Seattle, WA esetében.

A `timeZone` Válasz megadja a hely nevét, a jelenlegi UTC dátumot és időt a megadott helyen, valamint az UTC eltolását. Ha a hely határa több időzónán belül van, a válasz a határon belüli összes időzóna aktuális UTC-dátumát és időpontját tartalmazza. Például, mivel a floridai állam két időzónán belül esik, a válasz a helyi dátumot és időpontot tartalmazza mindkét időzónában.  

Ha a lekérdezés egy állam vagy ország/régió időpontját kéri, a Bing meghatározza az elsődleges várost a hely földrajzi határán belül, és visszaadja azt a `primaryCityTime` mezőben. Ha a határ több időzónát tartalmaz, a rendszer a hátralévő időzónákat adja vissza a `otherCityTimes` mezőben.

Az alábbi példákban a választ visszaadó lekérdezések láthatók `timeZone` .

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

Ha a Bing azt állapítja meg, hogy a felhasználó nem keres valami mást, a válasz tartalmaz egy [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) objektumot. Ha például a felhasználó *Carlos-tollat* keres, a Bing azt is meghatározhatja, hogy a felhasználó valószínűleg a Carlos Pena keresését tervezi (a korábbi, *Carlos Pen*-beli keresések alapján). A következő példa egy helyesírási választ mutat be.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Válaszfejlécek

A Bing Web Search API által küldött válaszok a következő fejléceket tartalmazhatják:

| Fejléc | Leírás |
|-|-|
|`X-MSEdge-ClientID`|A Bing által a felhasználóhoz hozzárendelt egyedi azonosító|
|`BingAPIs-Market`|A kérelem teljesítéséhez használt piac|
|`BingAPIs-TraceId`|A kéréshez tartozó Bing API-kiszolgálón található naplóbejegyzés (támogatáshoz)|

Különösen fontos az ügyfél-azonosító megőrzése, és a későbbi kérelmekkel való visszaküldése. Ha ezt teszi, a keresés a rangsorolás keresési eredményei között a múltbeli kontextust fogja használni, és egységes felhasználói élményt nyújt.

Ha azonban a Bing Web Search API a JavaScriptből hívja meg, a böngésző beépített biztonsági funkciói (CORS) megakadályozhatják a fejlécek értékének elérését.

A fejlécek eléréséhez a Bing Web Search API kérelmet CORS-proxyn keresztül teheti meg. Az ilyen proxytól kapott válasz egy `Access-Control-Expose-Headers` fejlécet tartalmaz, amely a válaszok fejléceit szűri, és elérhetővé teszi őket a JavaScript számára.

Egyszerűen telepíthet CORS-proxyt, hogy az oktatóanyag- [alkalmazás](tutorial-bing-web-search-single-page-app.md) hozzáférhessen a választható ügyfél-fejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

```console
npm install -g cors-proxy-server
```

Ezután módosítsa a HTML-fájlban lévő Bing Web Search API végpontot a következőre: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Végül indítsa el a CORS-proxyt a következő paranccsal:

```console
cors-proxy-server
```

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Válasz fejlécei éles környezetben

Az előző válaszban leírt CORS-proxy módszer a fejlesztés, a tesztelés és a tanulás számára megfelelő.

Éles környezetben olyan kiszolgálóoldali parancsfájlt kell üzemeltetni, amely a Bing Web Search APIt használó weboldallal azonos tartományban található. A szkriptnek API-hívásokat kell tennie a weblap JavaScript-kérelme alapján, és át kell adnia az összes találatot, beleértve a fejléceket, vissza kell térnie az ügyfélhez. Mivel a két erőforrás (lap és parancsfájl) megoszt egy forrást, a rendszer nem használja a CORS, és a speciális fejlécek elérhetők a JavaScript számára a weblapon.

Ez a megközelítés az API-kulcs védelmét is védi a nyilvánosság számára, mivel csak a kiszolgálóoldali parancsfájlra van szükség. A parancsfájl egy másik módszert is használhat, hogy a kérelem engedélyezve legyen.

Az alábbi ábrán látható, hogyan használja a Bing a helyesírási javaslatot.

![Bing – helyesírási javaslat – példa](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Következő lépések  

* Felülvizsgálati [kérelem szabályozási](throttling-requests.md) dokumentációja.  

## <a name="see-also"></a>Lásd még  

* [Bing Web Search API referenciája](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)