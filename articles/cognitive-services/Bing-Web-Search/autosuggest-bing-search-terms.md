---
title: Keresési feltételek automatikus ajánlása – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API és a Bing Autosuggest API párosításával biztosíthatja a felhasználók számára a továbbfejlesztett keresési élményt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 5bd5ff0e49b125277255ec8e5c216583d75043a0
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381133"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Bing-keresési kifejezések automatikus kiegészítése az alkalmazásban

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](https://aka.ms/cogsvcs/bingmove)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó beírja a keresési kifejezést, a [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) lekérdezési paraméter beállítása előtt kódolni kell az URL-címet. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés helyesírási hibát tartalmaz, a keresési válasz egy [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objektumot tartalmaz. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Ezekkel az adatokkal engedélyezheti, hogy a felhasználó tudja, hogy módosította a lekérdezési karakterláncot a keresési eredmények megjelenítésekor.

![Példa a lekérdezés kontextusára – UX](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Következő lépések  

* Tekintse át a minta [Bing Web Search API válaszokat](search-responses.md).  

## <a name="see-also"></a>Lásd még  

* [Bing Web Search API referenciája](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)