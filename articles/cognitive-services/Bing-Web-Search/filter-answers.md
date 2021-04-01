---
title: Keresési eredmények szűrése – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Az "responseFilter" lekérdezési paraméter használatával szűrheti a válaszban a Bing által megadott válaszok típusait (például képek, videók és Hírek).
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 571314009b6f58e5c2ab6aac02cfebc82c53f42f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351861"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>A keresési választ tartalmazó válaszok szűrése  

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Amikor lekérdezi a webet, a Bing visszaadja a kereséshez talált összes releváns tartalmat. Ha például a keresési lekérdezés "Sailing + mentőcsónakokra", akkor a válasz a következő válaszokat tartalmazhatja:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Lekérdezési paraméterek

A Bing által visszaadott válaszok szűréséhez használja az alábbi lekérdezési paramétereket az API meghívásakor.  

### <a name="responsefilter"></a>ResponseFilter

A válaszban a Bing által megadott válaszok (például képek, videók és Hírek) alapján szűrheti a válaszokat a [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) lekérdezési paraméter használatával, amely a válaszok vesszővel tagolt listája. A válasz tartalmazza a választ, ha a Bing megkeresi a megfelelő tartalmat. 

Ha ki szeretne zárni a válaszból (például képekből) származó konkrét válaszokat, a előtagot `-` a válasz típusaként adja meg. Például:

```
&responseFilter=-images,-videos
```

Az alábbiakban bemutatjuk `responseFilter` , hogyan kérhet képeket, videókat és híreket a vitorlás mentőcsónakokra. A lekérdezési karakterlánc kódolásakor a vesszők a következőre változnak:% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Az alábbiakban az előző lekérdezésre adott válasz látható. Mivel a Bing nem találta meg a kapcsolódó videók és hírek eredményeit, a válasz nem tartalmazza azokat.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Bár a Bing nem adott vissza videót és híreket az előző válaszban, nem jelenti azt, hogy a videó-és hírek tartalma nem létezik. Ez egyszerűen azt jelenti, hogy a lap nem tartalmazza azokat. Ha azonban több találat is van a [lapon](./paging-search-results.md) , a következő lapok valószínűleg belefoglalják őket. Emellett, ha a [Video Search API](../bing-video-search/overview.md) -t és [News Search API](../bing-news-search/search-the-web.md) -végpontokat közvetlenül hívja meg, a válasz valószínűleg eredményeket tartalmaz.

A használatával nem lehet `responseFilter` egyetlen API-ból eredményeket szerezni. Ha egyetlen Bing API-val szeretne tartalmat használni, hívja meg az API-t közvetlenül. Ha például csak képeket szeretne kapni, küldjön egy kérést az Image Search API-végpontnak `https://api.cognitive.microsoft.com/bing/v7.0/images/search` vagy egy másik [lemezkép](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) -végpontnak. Az egyetlen API meghívása nem csak a teljesítmény szempontjából fontos, hanem azért, mert a tartalom-specifikus API-k gazdagabb eredményeket nyújtanak. Használhat például olyan szűrőket, amelyek nem érhetők el a Web Search API számára az eredmények szűréséhez.  

### <a name="site"></a>Hely

Egy adott tartomány keresési eredményeinek beszerzéséhez a lekérdezési `site:` karakterláncban adja meg a lekérdezési paramétert.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Ha a lekérdezési operátort használja, a lekérdezéstől függően `site:` Előfordulhat, hogy a válasz csak felnőtt tartalmat tartalmazhat, a [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) beállítástól függetlenül. Csak akkor használja a `site:` operátort, ha ismeri a webhely tartalmát, és a felnőtteknek szóló tartalmak megjelenítése nem okoz problémát.

### <a name="freshness"></a>Frissesség

Ha korlátozni szeretné a webválaszok eredményeit arra a weblapra, amelyet a Bing adott időszakban észlelt, állítsa a [frissesség](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) lekérdezési paraméterét a következő kis-és nagybetűket megkülönböztető értékek egyikére:

* `Day` – A Bing által az elmúlt 24 órában felderített weblapok visszaküldése
* `Week` – A Bing által az elmúlt 7 napban felderített weblapok visszaküldése
* `Month` – Az elmúlt 30 napban felderített weblapok visszaküldése

Ezt a paramétert egy egyéni dátumtartomány is megadhatja az űrlapon `YYYY-MM-DD..YYYY-MM-DD` . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Ha az eredményeket egyetlen dátumra szeretné korlátozni, állítsa a frissesség paramétert egy adott dátumra:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Az eredmények tartalmazhatnak a megadott időszakon kívül eső weblapokat is, ha a szűrési feltételeknek megfelelő weblapok száma kevesebb, mint a kért weblapok száma (vagy a Bing által visszaadott alapértelmezett szám).

## <a name="limiting-the-number-of-answers-in-the-response"></a>A válaszban szereplő válaszok számának korlátozása

A Bing több választ adhat vissza a JSON-válaszban. Ha például a *Sailing + mentőcsónakokra* lekérdezést kérdezi le, a Bing visszatérhet a következőre:, `webpages` `images` `videos` és `relatedSearches` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Ha korlátozni szeretné a Bing által az első két válaszra (weblapok és képek) vonatkozó válaszok számát, állítsa a [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) lekérdezési paramétert 2 értékre.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A válasz csak a `webPages` és a-t tartalmazza `images` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Ha a `responseFilter` lekérdezési paramétert hozzáadja az előző lekérdezéshez, és azt weblapokra és hírekre állítja be, a válasz csak weblapokat tartalmaz, mivel a hírek nem rangsorolva vannak.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>A nem rangsorolt válaszok előléptetése

Ha a legfelső szintű válasz, amelyet a Bing visszaadott egy lekérdezéshez, a weblapok, a képek, a videók és a relatedSearches, a válasz tartalmazni fogja ezeket a válaszokat. Ha a [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) két (2) értékre állítja be, a Bing az első két rangsorolt választ adja vissza: weblapok és képek. Ha azt szeretné, hogy a Bing a válaszban képeket és videókat is tartalmazzon, adja meg az [előléptetés](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) lekérdezési paramétert, és állítsa be a képeket és a videókat.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A fenti kérelemre adott válasz a következő: A Bing a legfontosabb két választ, weblapokat és képeket adja vissza, és támogatja a videókat a válaszban.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Ha a `promote` News (Hírek) értékre van állítva, a válasz nem tartalmazza a hírek választ, mert nem rangsorolt válasz, &mdash; csak rangsorolt válaszokat lehet előléptetni.

Az előléptetni kívánt válaszok nem számítanak bele a `answerCount` korlátba. Ha például a rangsorolt válaszok a hírek, a képek és a videók, és az 1. és a hírek lehetőségre van állítva, `answerCount` `promote` a válasz híreket és képeket tartalmaz. Vagy ha a rangsorolt válaszok videók, képek és hírek, a válasz videókat és híreket tartalmaz.

Csak akkor használható, `promote` Ha megadja a `answerCount` lekérdezési paramétert.