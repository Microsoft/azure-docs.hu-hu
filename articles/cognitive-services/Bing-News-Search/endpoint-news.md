---
title: Bing News Search-végpontok
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Hírek keresési API-végpontok összegzését tartalmazza; Hírek, legfontosabb hírek és trendek a hírekhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94366400"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API végpontok

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **News Search API** híreket, weblapokat, képeket, videókat és [entitásokat](../bing-entities-search/overview.md)ad vissza. Az entitások egy személy, hely vagy témakör összegző információit tartalmazzák.

## <a name="endpoints"></a>Végpontok

Ha a Bing News Search API használatával szeretné beolvasni a keresési eredményeket, küldjön egy `GET` kérelmet a következő végpontok egyikére. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

### <a name="news-items-by-search-query"></a>Hírek keresési lekérdezés szerint

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Egy keresési lekérdezésen alapuló híreket ad vissza. Ha a keresési lekérdezés üres, az API különböző kategóriákból származó legfontosabb híreket ad vissza. A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és fűzze hozzá a `q=""` paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Legfontosabb hírek kategóriánként

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

A legfontosabb híreket adja vissza kategóriánként. A, a, a vagy a használatával a legfontosabb üzleti, sport-vagy szórakoztató cikkeket kérheti le `category=business` `category=sports` `category=entertainment` .  A `category` paraméter csak az `/news` URL-cím használatával használható. Néhány formális követelmény a kategóriák megadásához; `category` a [lekérdezési paraméter](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) dokumentációjában tájékozódhat. A keresési kifejezés URL-címének kódolásával küldje el a lekérdezést, és fűzze hozzá a `q=""` paraméterhez. A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Trendek – Hírek 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

A közösségi hálózatokra jelenleg kapcsolódó híreket adja vissza. A beállítás megadása esetén a `/trendingtopics` Bing Search figyelmen kívül hagyja a többi paramétert, például a és a elemet `freshness` `?q=""` . A rendelkezésre állást lásd: [támogatott országok/régiók és piacok](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Következő lépések

A fejlécekkel, paraméterekkel, piaci kódokkal, válasz-objektumokkal, hibákkal és egyéb adatokkal kapcsolatos részletekért tekintse meg a [Bing News Search API v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) dokumentációját.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
A News Search API-t használó alapszintű kérelmekre például a következő témakörben talál további információt: [Bing News Search Quick-starts](/azure/cognitive-services/bing-news-search).