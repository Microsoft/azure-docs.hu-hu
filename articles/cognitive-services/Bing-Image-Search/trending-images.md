---
title: Trendek a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Keresse meg a mai trendek képeit a webről a Bing Image Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341614"
---
# <a name="get-trending-images-from-the-web"></a>Trendek a webes képekből

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A mai trendek beszerzéséhez küldje el a következő GET kérelmet:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A trend images API jelenleg csak a következő piacokat támogatja:  

- en-US (angol, Egyesült Államok)  
- en-CA (angol, Kanada)  
- EN-AU (angol, Ausztrália)  
- zh-CN (kínai, Kína)

A válasz egy olyan [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) objektumot tartalmaz, amely kategória szerint sorolja fel a lemezképeket. A kategória `title` segítségével csoportosíthatja a képeket a felhasználói élményben. A kategóriák változhatnak naponta.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Minden csempe tartalmaz egy képet és egy lehetőséget a kapcsolódó képek beolvasásához. A kapcsolódó rendszerképek lekéréséhez a lekérdezést használhatja a `text` [Image Search API](./overview.md) meghívásához és a kapcsolódó rendszerképek megjelenítéséhez. A-ben az URL-cím használatával a `webSearchUrl` felhasználót a Bing képkeresés eredményeinek oldalára is felhasználhatja, amely tartalmazza a kapcsolódó képeket.

Ha a kapcsolódó rendszerképek beolvasásához a Image Search API-t hívja meg, állítsa be az [ID](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) lekérdezési paramétert a mezőben lévő azonosítóra `id` . Az azonosító megadásával biztosíthatja, hogy a válasz tartalmazza a képet (ez az első kép a válaszban) és a kapcsolódó képeket. Emellett állítsa a [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) lekérdezési paramétert az `query` objektum mezőjében szereplő szövegre `text` .

Az alábbi példa bemutatja, hogyan használható a rendszerkép-azonosító a Mr. Smith kapcsolódó képeinek beolvasására az előző trend images API-válaszban.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```