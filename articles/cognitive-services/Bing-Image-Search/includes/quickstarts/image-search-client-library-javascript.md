---
title: Bing Image Search JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625405"
---
Ezzel a rövid útmutatóval elvégezheti az első képkeresést az Bing Image Search ügyféloldali kódtár használatával, amely az API burkolója, és ugyanazokat a szolgáltatásokat tartalmazza. Ez az egyszerű JavaScript-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) , és további hibákat és megjegyzéseket is biztosít.

## <a name="prerequisites"></a>Előfeltételek

* A [Node.js](https://nodejs.org/en/download/) legújabb verziója.
* A [javascripthez készült BING Image Search SDK](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  A telepítéséhez futtassa a következőt `npm install @azure/cognitiveservices-imagesearch`
* A `CognitiveServicesCredentials` `@azure/ms-rest-azure-js` csomagból a-ügyfelet hitelesítő osztály.
     * A telepítéséhez futtassa a következőt `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE-környezetében vagy szerkesztőjében, és állítsa be a szigorúságot, a https-t és az egyéb követelményeket.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. A projekt fő metódusában hozzon létre változókat érvényes előfizetési kulcsához, a Bing által visszaadandó képtalálatokhoz és egy keresési kifejezéshez. Ezután példányosítsa a képkeresési ügyfelet a kulcs használatával.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Aszinkron segítő függvény létrehozása

1. Hozzon létre egy függvényt, amely aszinkron módon hívja meg az ügyfelet, és visszaadja a Bing Image Search szolgáltatás válaszát.
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Lekérdezés küldése és a válasz kezelése

1. Hívja meg a segítő függvényt, és kezelje annak `promise` elemét a válaszban visszaadott képtalálatok elemzéséhez.

    Ha a válasz tartalmaz keresési eredményeket, tárolja az első találatot, és jelenítse meg annak részleteit, például a miniatűr URL-címét és az eredeti URL-címet a visszaadott képek teljes számával együtt.
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>További információ

* [Mi a Bing Image Search?](../../overview.md)
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Node.js-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Az Azure Cognitive Services dokumentációja](../../../index.yml)
* [Bing Image Search API – referencia](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)