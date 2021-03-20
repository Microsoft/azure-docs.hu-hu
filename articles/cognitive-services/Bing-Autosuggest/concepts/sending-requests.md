---
title: Kérelmek küldése a Bing Autosuggest APInak
titleSuffix: Azure Cognitive Services
description: A Bing Autosuggest API visszaadja a javasolt lekérdezések listáját a keresőmező részleges lekérdezési karakterlánca alapján. További információ a kérelmek küldéséről.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353391"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Kérelmek küldése a Bing Autosuggest APInak.

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ha az alkalmazás a Bing Search API-k bármelyikére küld lekérdezéseket, a Bing Autosuggest API segítségével javíthatja a felhasználók keresési élményét. A Bing Autosuggest API visszaadja a javasolt lekérdezések listáját a keresőmező részleges lekérdezési karakterlánca alapján. Ahogy a karakterek bekerülnek az alkalmazás egy keresési mezőjébe, a javaslatok a legördülő listában jelennek meg. Ebből a cikkből megtudhatja, hogyan küldhet kérelmeket erre az API-ra. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Autosuggest API végpont

A **Bing AutoSuggest API**  egy végpontot tartalmaz, amely egy részleges keresési kifejezésből származó javasolt lekérdezések listáját adja vissza.

Ha a Bing API használatával szeretne felvenni a javasolt lekérdezéseket, küldjön egy `GET` kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

**Végpont:** A keresési javaslatokat adja vissza JSON-eredményekként, amelyek az által definiált felhasználó által megadott bemenetre vonatkoznak `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

A fejlécekről, paraméterekről, piaci kódokról, válasz-objektumokról, hibákról és egyéb adatokról a [Bing AutoSuggest API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) -dokumentációban talál további információt.

A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.
Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és/vagy helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
Az automatikus kiegészítési API-t használó alapszintű kérelmekre vonatkozó példákért [tekintse](/azure/cognitive-services/Bing-Autosuggest)meg az automatikus kiegészítési útmutatókat.

## <a name="bing-autosuggest-api-requests"></a>Bing Autosuggest API kérelmek

> [!NOTE]
> * A Bing Autosuggest API kérelmeknek a HTTPS protokollt kell használniuk.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Ha egy ügyfélalkalmazás részeként terjeszti a kulcsot, a rosszindulatú külső felektől származó hozzáférés nagyobb lehetőséget biztosít. Emellett a kiszolgálókon kezdeményezett hívások egyetlen frissítési pontot biztosítanak a jövőbeli frissítésekhez.

A kérelemnek tartalmaznia kell a [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) lekérdezési paramétert, amely a felhasználó részleges keresési kifejezését adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek listáját lásd a [lekérdezési paramétereket](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.

A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) cikkben.

> [!NOTE]
> Ha a Bing Autosuggest API a JavaScriptből hívja meg, a böngésző beépített biztonsági funkciói megakadályozhatják a fejlécek értékének elérését.

Ennek megoldásához a Bing Autosuggest API kérelmet egy CORS-proxyn keresztül teheti meg. Az ilyen proxytól kapott válasz egy `Access-Control-Expose-Headers` fejlécet tartalmaz, amely a válaszok fejléceit szűri, és elérhetővé teszi őket a JavaScript számára.

Egyszerűen telepíthet CORS-proxyt, hogy az oktatóanyag- [alkalmazás](../tutorials/autosuggest.md) hozzáférhessen a választható ügyfél-fejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

```console
npm install -g cors-proxy-server
```

Ezután módosítsa a HTML-fájlban lévő Bing Autosuggest API végpontot a következőre:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Végül indítsa el a CORS-proxyt a következő paranccsal:

```console
cors-proxy-server
```

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

A kérelmeknek tartalmaznia kell az összes javasolt lekérdezési paramétert és fejlécet. 

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosító fejlécét, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

A következő webes javaslati csoport a fenti kérelemre adott válasz. A csoport a keresési lekérdezési javaslatok listáját tartalmazza, és minden javaslatot tartalmaz, beleértve a `displayText` , a `query` és a mezőt is `url` .

A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.  

Ha a felhasználó egy lekérdezést választ ki a legördülő listából, használhatja a [Bing Search API-k](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) egyikének meghívására, és saját maga is megtekintheti az eredményeket, vagy a visszaadott mező használatával elküldheti a felhasználót a Bing eredmények oldalára `url` .

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Következő lépések

- [Mi a Bing Autosuggest?](../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Javasolt keresési kifejezések beolvasása a Bing Autosuggest APIból](get-suggestions.md)