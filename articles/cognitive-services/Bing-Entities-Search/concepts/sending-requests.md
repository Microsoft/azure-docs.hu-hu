---
title: Keresési kérelmek küldése a Bing Entity Search APInak
titleSuffix: Azure cognitive Services
description: A Bing Entity Search API egy keresési lekérdezést küld a Bingnek, majd entitásokat és helyeket tartalmazó találatokat kap vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353272"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Keresési kérelmek küldése a Bing Entity Search APInak

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Entity Search API egy keresési lekérdezést küld a Bingnek, majd entitásokat és helyeket tartalmazó találatokat kap vissza. A helytalálatok lehetnek éttermek, szállodák vagy egyéb helyi vállalkozások. A helyekre vonatkozó lekérdezések meghatározhatják egy helyi vállalkozás nevét, vagy egy listát is lekérhetnek (például „éttermek a közelben”). Az entitástalálatok személyek, helyek vagy dolgok lehetnek. Ennek a kontextusnak a helyszíne turisztikai látványosságok, Államok, országok/régiók stb.

## <a name="the-endpoint"></a>A végpont

Az entitás- és helykeresés találatainak lekéréséhez egy GET kérelmet kell küldeni a következő végpontra:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

A kérelmeknek a HTTPS protokollt kell használniuk.

Javasoljuk, hogy minden kérelem egy kiszolgálóról induljon. Az azonosítónak egy ügyfélalkalmazás részeként való terjesztése több lehetőséget ad arra, hogy rosszindulatú külső felek hozzáférjenek az azonosítóhoz. Emellett a hívások kiszolgálóról való indítása egyetlen frissítési pontot teremt az API későbbi verziói számára.

## <a name="specifying-query-parameters-and-headers"></a>A lekérési paraméterek és fejlécek meghatározása

A kérelemnek tartalmaznia kell a [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) lekérdezési paramétert, amely a felhasználó keresési kifejezését adja meg. A kérelemnek tartalmaznia kell egy [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) lekérdezési paramétert is, amely azonosítja a piacot, ahonnan eredményeket szeretne kapni. Az opcionális lekérdezési paraméterek listáját lásd a [lekérdezési paramétereket](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) ismertető cikkben. Minden lekérdezési paraméternek URL-kódolásúnak kell lennie.  
  
A kérelemnek tartalmaznia kell az [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey) fejlécet. Nem kötelező, de javasolt a következő fejlécek megadása is:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Az ügyfél IP-címe és helye fontos a helyfüggő tartalmak visszaadása szempontjából.  

Az összes kérelem- és válaszfejléc listáját lásd a [Fejlécek](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) cikkben.

## <a name="the-request"></a>A kérelem

Az alábbiakban egy olyan entitáskeresési kérés látható, amely az összes javasolt lekérdezési paramétert és fejlécet tartalmazza. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Ha első alkalommal hívja meg bármelyik Bing API-t, ne használja az ügyfél-azonosító fejlécét. Csak akkor használja az ügyfél-azonosítót, ha korábban már meghívott egy Bing API-t, és visszakapott egy ügyfél-azonosítót a felhasználó és az eszköz kombinációjához.

## <a name="the-response"></a>A válasz

Az alábbiakban az előző kérelemre adott válasz látható. A példában a Bing-specifikus válaszfejlécek is láthatók. A válaszobjektumra vonatkozó további információért lásd: [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Következő lépések

* [Entitások keresése a Bing Entity API-val](search-for-entities.md)
* [A Bing API használatának és megjelenítési követelményei](../../bing-web-search/use-display-requirements.md)