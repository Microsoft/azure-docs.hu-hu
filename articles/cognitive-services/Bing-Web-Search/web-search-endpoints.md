---
title: Webes keresés végpont
titleSuffix: Azure Cognitive Services
description: A webes keresési API-végpont összefoglalása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: e91f798e6bfae33f8f4c8b5aa2d0f6ddc0047389
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798264"
---
# <a name="web-search-endpoint"></a>Webes keresés végpont

A **Web Search API** adja vissza a weblapok, hírek, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Az entitásnak egy személy, hely vagy a témakör összefoglaló adatait.

## <a name="endpoint"></a>Végpont

Első webes találatok, a Bing API-val, küldjön egy `GET` kérelem a következő végpont. További specifikációk meghatározása a fejlécek és URL-paraméter.

**Végpont**: Adja vissza, amely a felhasználó keresési lekérdezés által meghatározott a webes találatokat `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Végpont: A fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák és további részleteiért lásd: a [a Bing webes-API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) hivatkozást.

## <a name="response-json"></a>Válasz JSON

Keresés webes kérelemre adott válasz JSON-objektumként minden eredmény tartalmazza. Az eredmény-elemzés szükséges eljárásokat, amelyek az egyes elemek kezelésére. Tekintse meg a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) és [forráskódját](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) példákat.

## <a name="next-steps"></a>További lépések

A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és egy helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A webes keresési API-t használó alapszintű kérések példákért lásd [keresés a webes használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
