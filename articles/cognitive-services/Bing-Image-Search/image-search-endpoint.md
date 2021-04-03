---
title: A Bing Image Search API végpontjai
titleSuffix: Azure Cognitive Services
description: A Image Search API három végpontot tartalmaz. Az 1. végpont a webről származó képeket ad vissza. A 2. végpont a ImageInsights adja vissza. A 3. végpont a trendi képeket adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94593518"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>A Bing Image Search API végpontjai

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **Image Search API**  három végpontot tartalmaz.  Az 1. végpont a webről származó képeket ad vissza egy lekérdezés alapján. A 2. végpont a [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse)adja vissza.  A 3. végpont a trendi képeket adja vissza.

## <a name="endpoints"></a>Végpontok

Ha a Bing API használatával szeretné beolvasni a képkeresési eredményeket, küldjön egy kérelmet a következő végpontok egyikére. A fejlécek és az URL-paraméterek használatával további specifikációkat határozhat meg.

**1. végpont:** Azokat a képeket adja vissza, amelyek az által definiált felhasználó keresési lekérdezéséhez szükségesek `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** Egy képpel kapcsolatos elemzéseket ad vissza a vagy a `GET` használatával `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
A GET-kérelem egy képpel kapcsolatos információkat ad vissza, például a képet tartalmazó weblapokat. Adja meg a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) paramétert egy `GET` kéréssel.

Vagy egy bináris képet is hozzáadhat egy kérelem törzséhez, `POST` és a modules paramétert [a](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) következőre állíthatja: `RecognizedEntities` . Ez egy olyan [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) ad vissza, amelyet paraméterként fog használni egy későbbi `GET` kérelemben, amely a rendszerképben szereplő személyekre vonatkozó adatokat adja vissza.  Állítsa be az értékre az `modules` `All` összes bepillantást, kivéve a `RecognizedEntities` eredményét anélkül, hogy `POST` egy másik hívást kellene tennie a használatával `insightsToken` .


**3. végpont:** Azokat a képeket adja vissza, amelyek a mások által végzett keresési kérelmek alapján alakulnak. A képek különböző kategóriákba vannak elkülönítve, például a figyelemreméltó személyek vagy események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

A Trends lemezképeket támogató piacok listáját a következő témakörben tekintheti meg: [trendek képek](./trending-images.md).

A fejlécekről, paraméterekről, piaci kódokról, válasz-objektumokról, hibákról és egyéb adatokról a [Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -dokumentációban talál további információt.
## <a name="response-json"></a>Válasz JSON
A képkeresési kérelemre adott válasz az eredményeket JSON-objektumokként tartalmazza. Az eredmények elemzésére példákat az [oktatóanyag](./tutorial-bing-image-search-single-page-app.md) és a [forráskód](./tutorial-bing-image-search-single-page-app.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések
A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és/vagy helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
A képkeresési API-t használó alapszintű kérelmekre például a következő témakörben talál további információt: [Image Search Quick-starts](./overview.md).