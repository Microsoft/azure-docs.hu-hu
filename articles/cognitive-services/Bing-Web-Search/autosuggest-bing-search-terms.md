---
title: Automatikus kiegészítés keresőkifejezéseket – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Párosítsa a Bing Web Search API a Bing Autosuggest API olyan továbbfejlesztett keresési élményt biztosít.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384862"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatikus kiegészítési Bing keresési kifejezéseket az alkalmazásban

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja egy keresési kifejezést, URL-kódolású előtt kell lennie a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) lekérdezési paraméter értéke. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés egy gépelési hibát tartalmaz, a keresési válasz tartalmazza a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objektum. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Ezen információk használatával lehetővé teszik a felhasználó ismeri, a lekérdezési karakterlánc módosítani, ha a keresési eredmények megjelenítéséhez.

![Lekérdezési környezet UX példa](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>További lépések  

* Felülvizsgálat minta [Bing Web Search API-válaszok](search-responses.md).  

## <a name="see-also"></a>Lásd még  

* [A Bing Web Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
