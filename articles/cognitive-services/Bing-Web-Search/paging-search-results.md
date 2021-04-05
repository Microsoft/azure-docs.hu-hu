---
title: Az oldal keresési eredményeinek megjelenítése – Bing Search API-k
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan lapozhat a Bing Search API-k keresési eredményei között.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350619"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Az Bing Search API-k eredményeinek megjelenítése

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing web, Custom, képek, hírek vagy Video Search API-k hívásának elküldésekor a Bing a lekérdezéshez kapcsolódó összes eredmény részhalmazát adja vissza. A rendelkezésre álló eredmények becsült teljes számának beszerzéséhez nyissa meg a válasz objektum `totalEstimatedMatches` mezőjét. 

Például: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Lapozás a keresési eredmények között

Az elérhető eredmények között a `count` és a `offset` lekérdezési paramétereket használhatja a kérelem elküldésekor.  

> [!NOTE]
>
> * A Bing video, a képek és a News API-k lapozása csak általános videó ( `/video/search` ), Hírek ( `/news/search` ) és képek ( `/image/search` ) keresésekre vonatkozik. A lapozási témakörök és kategóriák nem támogatottak.  
> * A `TotalEstimatedMatches` mező az aktuális lekérdezés keresési eredményeinek teljes számát adja meg. A `count` és paraméterek beállításakor `offset` Ez a becslés változhat.

| Paraméter | Leírás                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Megadja a válaszban visszaadni kívánt eredmények számát. Vegye figyelembe, hogy az alapértelmezett érték `count` és a kérelmek maximális száma az API-tól függően változhat. Ezeket az értékeket a [következő lépések](#next-steps)szakaszban található hivatkozási dokumentációban találja. |
| `offset`  | A kihagyni kívánt eredmények számát adja meg. A `offset` nulla-alapú, és kisebbnek kell lennie, mint ( `totalEstimatedMatches`  -  `count` ).                                           |

Ha például 15 találat/oldal megjelenítését szeretné megjeleníteni, `count` `offset` a találatok első oldalának lekéréséhez 15 és 0-ra értéket kell beállítani. Minden további API-hívás esetében 15-re növekszik `offset` . Az alábbi példa 15 weblapot kér le a 45 eltolásnál kezdődően.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ha az alapértelmezett értéket használja `count` , csak a lekérdezési paramétert kell megadnia az `offset` API-hívásokban.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A Bing-rendszerkép és a videó API-k használatakor az `nextOffset` érték használatával elkerülhetők az ismétlődő keresési eredmények. Szerezze be az értéket a `Images` vagy a `Videos` Válasz objektumokból, és használja azt a paraméterrel rendelkező kérésekben `offset` .  

> [!NOTE]
> A Bing Web Search API olyan keresési eredményeket ad vissza, amelyek weblapokat, képeket, videókat és híreket is tartalmazhatnak. Ha a Bing Web Search API a keresési eredmények között lapoz át, [akkor csak a weblapok](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)lapozása történik meg, más típusú válaszok, például képek vagy hírek. Az objektumok keresési eredményei `WebPage` tartalmazhatják a más válaszokban megjelenő eredményeket is.
>
> Ha a `responseFilter` lekérdezési paramétert a szűrési értékek megadása nélkül használja, ne használja a `count` és a `offset` paramétert. 

## <a name="next-steps"></a>Következő lépések

* [Mik a Bing Web Search API-k?](bing-api-comparison.md)
* [Bing Web Search API 7-es verzió – referencia](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Custom Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)