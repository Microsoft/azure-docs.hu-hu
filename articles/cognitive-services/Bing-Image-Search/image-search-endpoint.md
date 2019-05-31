---
title: A Bing képkeresési API végpontjai
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API elérhető végpontok listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 076dbb26c267cf65a0d6f3a9835375b09951f2aa
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388512"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>A Bing képkeresési API végpontjai

A **Image Search API** három végpontokat tartalmazza.  1. végpont képeket a weben lekérdezés alapján adja vissza. 2. végpont adja vissza [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Végpont 3 felkapott képeket ad vissza.

## <a name="endpoints"></a>Végpontok

A Bing API-val képtalálatok lekéréséhez kérést küldhet az alábbi végpontok egyikét. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

**1. végpont:** Adja vissza, amely a felhasználó keresési lekérdezés által meghatározott a lemezképek `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**2. végpont:** Adja vissza a képet, vagy észrevételeket `GET` vagy `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Egy GET kéréssel információival, kép, például a lemezképet tartalmazó weblapokra adja vissza. Tartalmazza a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) paraméterrel egy `GET` kérelmet.

Vagy megadhat egy bináris törzsében egy `POST` kérelem, és állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) paramétert `RecognizedEntities`. Ez visszaad egy [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) használni egy későbbi paraméterként `GET` kérelmet, amelyet a képen személyek információt ad vissza.  Állítsa be `modules` a `All` összes elemzések lekérése, kivéve a `RecognizedEntities` eredményei a `POST` anélkül, hogy egy másik átlépéséhez használja a `insightsToken`.


**3. végpont:** Értéket ad vissza rendszerképek pedig kedvelheti keresési kéréseket a mások által végrehajtott alapján. A képek elkülönül egymástól különböző kategóriákba például méltó személyek és események alapján.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Olyan piacon, amely támogatja a felkapott képek egy listája: [népszerű képek](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [a Bing Image Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) hivatkozás.
## <a name="response-json"></a>Válasz JSON
A válasz egy lemezkép-keresési kérelem eredményeket JSON-objektumként tartalmazza. Elemzési eredmények példát talál a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) és [forráskódját](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A képkeresési API-t használó alapszintű kérések példákért lásd [Képkeresés használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
