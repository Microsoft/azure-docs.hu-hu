---
title: Webes keresési végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési eredmények beszerzéséhez küldjön egy `GET` kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94381252"
---
# <a name="web-search-endpoint"></a>Web Search végpont

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **Web Search API** weblapokat, híreket, képeket, videókat és [entitásokat](../bing-entities-search/overview.md)ad vissza. Az entitások összegző információkkal rendelkeznek egy személyről, helyről vagy témakörről.

## <a name="endpoint"></a>Végpont

Ha a Bing API használatával szeretné lekérni a webes keresési eredményeket, küldjön egy `GET` kérelmet a következő végpontnak. A fejlécek és az URL-paraméterek további specifikációkat határoznak meg.

**Végpont**: olyan webes eredményeket ad vissza, amelyek az által definiált felhasználó keresési lekérdezésére vonatkoznak `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Végpont: a fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák és egyéb információk részleteiért tekintse meg a [Bing web API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) dokumentációját.

## <a name="response-json"></a>Válasz JSON

A webes keresési kérelemre adott válasz a JSON-objektumok összes eredményét tartalmazza. Az eredmény elemzéséhez olyan eljárások szükségesek, amelyek az egyes típusok elemeit kezelik. Példákért tekintse meg az [oktatóanyagot](./tutorial-bing-web-search-single-page-app.md) és a [forráskódot](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Következő lépések

A **Bing** API-k olyan keresési műveleteket támogatnak, amelyek típusaik alapján adják vissza az eredményeket. A keresési végpontok az eredményeket JSON-válasz objektumokként adják vissza.  Minden végpont támogatja a lekérdezéseket, amelyek adott nyelvet és helyet adnak vissza a földrajzi hosszúság, a szélesség és a keresési sugár alapján.

Az egyes végpontok által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes típusok hivatkozási oldalait.
Példák a webes keresési API-t használó alapszintű kérelmekre: [Keresés a weben – gyors indítás](./overview.md).