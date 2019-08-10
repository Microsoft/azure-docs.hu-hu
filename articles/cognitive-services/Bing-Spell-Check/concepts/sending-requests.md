---
title: Kérelmek küldése a Bing Spell Check API-nak
titleSuffix: Azure Cognitive Services
description: Ismerje meg az API-val kapcsolatos Bing Spell Check módokat, beállításokat és egyéb információkat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 893317b8f46415b1df540d67ebf28b65c5ba6d32
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883451"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Kérelmek küldése a Bing Spell Check API-nak

Egy szöveges karakterlánc helyesírási és nyelvtani hibáinak megkereséséhez GET kérést kell küldenie a következő végpontra:  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
A kérelemnek a HTTPS protokollt kell használnia.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Az azonosítónak egy ügyfélalkalmazás részeként való terjesztése több lehetőséget ad arra, hogy rosszindulatú külső felek hozzáférjenek az azonosítóhoz. A kiszolgálók egyetlen frissítési pontot is biztosítanak az API jövőbeli verzióihoz.

A kérelemnek tartalmaznia kell a [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) lekérdezési paramétert, amely az ellenőrzendő szöveges karakterláncot adja meg. Nem kötelező, de a kérelemnek érdemes tartalmaznia egy [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretnénk kapni. Az opcionális lekérdezési paraméterek (például `mode`) listáját lásd a [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméter értékének URL-kódolásúnak kell lennie.  
  
A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) fejlécet. Bár nem kötelező, javasoljuk, hogy a következő fejléceket is megadja. Ezek a fejlécek segítenek a Bing Spell Check APInak pontosabb eredményeket adni:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers) cikkben.

A Bing Spell Check API JavaScript használatával való meghívásakor a böngésző beépített biztonsági funkciói megakadályozhatják a fejlécek értékének elérését.

A probléma megoldásához a Bing Spell Check API kérelmet CORS-proxyn keresztül teheti meg. Az ilyen proxytól kapott válasz egy `Access-Control-Expose-Headers` fejlécet tartalmaz, amely a válasz fejléceit, és elérhetővé teszi őket a JavaScript számára.

Egyszerűen telepíthet egy CORS-proxyt, amely lehetővé teszi, hogy az [oktatóanyag-alkalmazás](../tutorials/spellcheck.md) hozzáférhessen a választható ügyfél-fejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a HTML-fájlban lévő Bing Spell Check API végpontot a következőre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A keresési eredmények alatt a kibontható HTTP-fejlécek szakaszban láthatja a `X-MSEdge-ClientID` fejlécet (többek között), és ellenőrizheti, hogy minden kérelem esetében azonos-e.

## <a name="example-api-request"></a>Példa API-kérelemre

Az alábbiakban egy olyan kérelem látható, amely az összes javasolt lekérdezési paramétert és fejlécet tartalmazza. Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához. 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Az alábbiakban az előző kérelemre adott válasz látható. A példában a Bing-specifikus válaszfejlécek is láthatók.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>További lépések

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
