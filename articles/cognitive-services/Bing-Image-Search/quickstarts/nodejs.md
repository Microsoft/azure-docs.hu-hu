---
title: 'Gyors útmutató: rendszerképek keresése a Bing Image Search REST API és a Node. js használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval képkeresési kérelmeket küldhet a Bing Image Search REST API JavaScript és JSON-válaszok használatával.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 597a12353fa573c628162b110f4e08e6d3a69b86
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118919"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Gyors útmutató: rendszerképek keresése a Bing Image Search REST API és a Node. js használatával

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet keresési kéréseket a Bing Image Search API. Ez a JavaScript-alkalmazás keresési lekérdezést küld az API-nak, és megjeleníti az eredményekben szereplő első rendszerkép URL-címét. Bár ez az alkalmazás JavaScript nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js).

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.

* A [JavaScript-kérelem könyvtára](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

További információ: [Cognitive Services díjszabása-BING Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és állítsa be a szigorú és a HTTPS-követelményeket.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Hozza létre az API-végpont, az Image API keresési útvonala, az előfizetési kulcs és a keresőkifejezés változóit. A (z) esetében `host` a globális végpontot használhatja a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot is.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása.

1. Az előző lépés változóit használva formázza a keresési URL-címet az API-kérelemhez. URL – a keresési kifejezés kódolása az API-hoz való elküldés előtt.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. A lekérési kódtár használatával küldje el a lekérdezést az API-nak. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>A válasz kezelése és elemzése

1. Definiáljon egy nevű függvényt, `response_handler` amely egy http-hívást ( `response` paraméterként) használ. 

2. A függvényen belül Definiáljon egy változót, amely tartalmazza a JSON-válasz törzsét. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. A válasz törzsét tárolja a `data` jelző meghívásakor.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Ha jelzőt `end` jelez, a JSON-válasz első eredményét kapja meg. Nyomtassa ki az első rendszerkép URL-címét, valamint a visszaadott lemezképek teljes számát.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Példa JSON-válaszra

A Bing Image Search API válaszai JSON formátumban érkeznek vissza. A mintaválasz egyetlen eredményre van csonkolva.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás létrehozása](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>További információ

* [Mi az a Bing Image Search API?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbáljon ki egy online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
* [A Bing Search API-k díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/). 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).
* Az [Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services).
* [Bing Image Search API hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
