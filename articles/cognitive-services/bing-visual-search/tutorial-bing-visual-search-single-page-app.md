---
title: " Egyoldalas Webalkalmazás létrehozása – Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan integrálhatja a Bing Visual Search API egy egyoldalas webalkalmazásba.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b1ca88cd654b7bf373bee60b1e9b7a2e7a129fa2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499016"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Oktatóanyag: Visual Search egyoldalas Webalkalmazás létrehozása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Visual Search API egy rendszerkép elemzéseit adja vissza. Feltölthet egy képet, vagy megadhat egy URL-címet is. Az elemzések vizuálisan hasonló képek, vásárlási források, weblapok, amelyek tartalmazzák a képet, és így tovább. A Bing Visual Search API által visszaadott bepillantások hasonlóak a Bing.com/images-on láthatók számára.

Ez az oktatóanyag azt ismerteti, hogyan bővíthető egy egyoldalas webalkalmazás a Bing Image Search API számára. Az oktatóanyag megtekintéséhez vagy az itt használt forráskód beszerzéséhez tekintse meg [az oktatóanyag: egyoldalas alkalmazás létrehozása a Bing Image Search APIhoz](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)című témakört.

Az alkalmazás teljes forráskódja (miután kiterjeszti a Bing Visual Search API használatára), elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>A Bing Visual Search API meghívása és a válasz kezelése

Szerkessze a Bing Image Search oktatóanyagot, és adja hozzá a következő kódot az `<script>` elem végéhez (és a záró `</script>` címke előtt). A következő kód egy vizuális keresési választ kezel az API-ból, megismétli az eredményeket, és megjeleníti őket:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

A következő kód egy keresési kérelmet küld az API-nak egy esemény-figyelő használatával `handleVisualSearchResponse()` :

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Megállapítási jogkivonat rögzítése

Adja hozzá a következő kódot az `searchItemsRenderer` objektumhoz. Ez a kód hozzáad egy **hasonló keresése** hivatkozást, amely meghívja a `bingVisualSearch` függvényt, ha rákattintanak. A függvény argumentumként fogadja a `imageInsightsToken` paramétert.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Hasonló képek megjelenítése

Adja hozzá a következő HTML-kódot a 601. sorban. Ez a jelölő kód egy elemet hoz létre a Bing Visual Search API hívás eredményének megjelenítéséhez:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

A rendelkezésre álló új JavaScript-kódok és HTML-elemek segítségével a keresési eredmények egy **hasonló keresése** hivatkozással jelennek meg. Kattintson a hivatkozásra a kiválasztott képhez hasonló képeket tartalmazó **Hasonló** szakasz feltöltéséhez. Lehetséges, hogy ki kell bontania a **Hasonló** szakaszt a képek megjelenítéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: kép körülvágása a C Bing Visual Search SDK-val #](tutorial-visual-search-crop-area-results.md)