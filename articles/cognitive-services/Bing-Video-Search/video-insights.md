---
title: A Bing Video Search API használatával videó-felismeréseket kaphat
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Bing Video Search APIt a videókkal, például a kapcsolódó videókkal kapcsolatos további információk eléréséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cbbde354c1bc68a2b1017c3ccba61b846fa62916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380759"
---
# <a name="get-insights-about-a-video"></a>Videókra vonatkozó információk lekérése

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Video Search API által visszaadott összes videó tartalmaz egy videó-azonosítót, amellyel további információkat kaphat, például a kapcsolódó videókat. A videóval kapcsolatos információk beszerzéséhez szerezze be az [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) -tokent az API-válaszban. 

```json
    "value" : [
        {
            . . .
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear...",
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHBZ--g",
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63",
            . . .
        }
    ],
```

Ezt követően küldjön egy GET-kérést a videó részleteit tartalmazó végpontra az AZONOSÍTÓval. Állítsa az [ID](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#id) lekérdezési paramétert a `videoId` tokenre. A lekérdezni kívánt adatfelismerések megadásához állítsa be a [modulok](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested) lekérdezési paraméterét. Az összes bepillantást az összes értékre állíthatja `modules` . A válasz tartalmazza az összes kért bepillantást, ha van ilyen.

```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-related-videos-insights"></a>Kapcsolódó videó-felismerések beolvasása  

A megadott videóhoz kapcsolódó videók beszerzéséhez állítsa a [modulok](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested) lekérdezési paraméterét a következőre: `RelatedVideos` .
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=RelatedVideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A kérelemre adott válasz felső szintű [VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails) objektummal fog rendelkezni a [videók](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) objektum helyett.  
  
```json
{
    "_type" : "Api.VideoDetails.VideoDetails",
    "relatedVideos" : {
        "value" : [
            {
                "name" : "How to sail - Reefing a Sail",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OVP.zt...",
                "datePublished" : "2014-03-04T16:11:09",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?...",
                "duration" : "PT4M56S",
                "motionThumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OM...",
                "allowHttpsEmbed" : true,
                "viewCount" : 21756,
                "videoId" : "AC1A157A4DDB571D03D6AC157A4DDB571D03D6",
                "allowMobileEmbed" : false,
                "isSuperfresh" : false
            },
            . . .
        ]
    }
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Trendek keresése a videók között](trending-videos.md)