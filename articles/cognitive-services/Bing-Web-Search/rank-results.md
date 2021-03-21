---
title: A keresési eredmények megjelenítésére szolgáló rangsorok használata – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan jelenítheti meg a keresési eredményeket a Bing Web Search API a rangsorolás használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94380449"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>A rangsor használata Bing Web Search API eredmények megjelenítéséhez  

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Minden keresési válasz tartalmaz egy [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) választ, amely megadja, hogy a keresési eredményeket hogyan kell megjeleníteni. A rangsorolási válaszok egy hagyományos keresési eredmények oldalának fővonali tartalma és oldalsáv-tartalma eredményei. Ha nem jeleníti meg az eredményeket hagyományos fővonal-és oldalsáv-formátumban, meg kell adnia a fővonali tartalmat, mint az oldalsáv tartalma.  

Az egyes csoportokon (fővonalon vagy oldalsávon) belül az [Items](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) Array azonosítja azt a sorrendet, amelyben a tartalomnak szerepelnie kell. Az egyes elemek a következő két módszert biztosítják az eredmény azonosítására a válaszon belül.  

-   `answerType` és `resultIndex` – a `answerType` mező azonosítja a választ (például a weboldalt vagy híreket), és `resultIndex` a válaszon belül azonosítja az eredményeket (például egy újságcikket). Az index nulla alapú.  

-   `value` – A `value` mező olyan azonosítót tartalmaz, amely a válasz vagy eredmény azonosítójának felel meg a válaszon belül. Vagy a válasz vagy az eredmény tartalmazza az azonosítót, de nem mindkettőt.  

Az azonosító használata egyszerűbb, mert csak a válasz AZONOSÍTÓjának vagy az egyik eredményének kell megegyeznie. Ha egy válasz objektum tartalmaz egy `id` mezőt, a válasz összes eredményét együtt jeleníti meg. Ha például az `News` objektum tartalmazza a `id` mezőt, az összes újságcikk együtt jelenjen meg. Ha az `News` objektum nem tartalmazza a `id` mezőt, akkor minden újságcikk tartalmaz egy mezőt, `id` és a rangsorolási válasz összekeveri a híreket más válaszok eredményeivel.  

A és a használata `answerType` `resultIndex` valamivel bonyolultabb. A segítségével `answerType` azonosíthatja a megjelenítendő eredményeket tartalmazó választ. Ezután a `resultIndex` Válasz eredményei alapján indexelheti az eredményt, hogy megjelenjen az eredmény. (Az `answerType` érték a mező neve a [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objektumban.) Ha azt szeretné, hogy a válasz összes eredménye együtt jelenjen meg, a rangsorolási válasz elem nem tartalmazza a `resultIndex` mezőt.  

## <a name="ranking-response-example"></a>Rangsorolási válasz példája

A következő példa egy [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)mutat be. Mivel a webes válasz nem tartalmaz `id` mezőt, a rangsorolás alapján egyenként jeleníti meg az összes weblapot (minden weblap tartalmaz egy `id` mezőt). Mivel a képek, videók és a kapcsolódó keresések választ adnak a `id` mezőre, az egyes válaszok eredményét a rangsorolás alapján fogja megjeleníteni.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

A rangsorolási válasz alapján a fővonal a következő keresési eredményeket jeleníti meg:  

-   Az első weblap eredménye
-   Minden rendszerkép  
-   A második és a harmadik weblap eredményei  
-   Minden videó  
-   A 4., 5. és 6. weblap eredményei  

Az oldalsáv pedig a következő keresési eredményeket jeleníti meg:  

-   Az összes kapcsolódó keresés  


## <a name="next-steps"></a>Következő lépések

További információ a nem rangsorolt eredmények előléptetéséről: [a nem rangsorolt válaszok előléptetése](./filter-answers.md#promoting-answers-that-are-not-ranked).

További információ a válaszban rangsorolt válaszok számának korlátozásáról: [a válaszban szereplő válaszok számának korlátozása](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Az eredmények megjelenítéséhez rangsort használó C#-példa: [C# ranking oktatóanyag](./csharp-ranking-tutorial.md).