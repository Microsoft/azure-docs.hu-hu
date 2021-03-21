---
title: Az Bing Entity Search API végpont
titleSuffix: Azure Cognitive Services
description: A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338257"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API végpont

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).


A Bing Entity Search API egy végponttal rendelkezik, amely egy lekérdezés alapján a webes entitásokat adja vissza. Ezeket a keresési eredményeket a rendszer a JSON-ban adja vissza.

## <a name="get-entity-results-from-the-endpoint"></a>Entitás eredményeinek beolvasása a végpontból

Ha az entitások eredményeit a **BING API** használatával szeretné lekérni, küldjön egy `GET` kérelmet a következő végpontnak. A keresési kérelem személyre szabásához használja a [fejléceket](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) és a [lekérdezési paramétereket](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . A keresési kérelmeket a paraméter használatával lehet elküldeni `?q=` .

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi a Bing Entity Search API?](overview.md)

## <a name="see-also"></a>Lásd még 

További információ a fejlécekről, a paraméterekről, a piaci kódokról, a válaszok objektumairól és a hibákról: [Bing Entity Search API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) -útmutató.