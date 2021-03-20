---
title: Képkeresési lekérdezések testreszabása és javaslata – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: További információ a Bing Image Search API küldött keresési lekérdezések testreszabásáról.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 2566b2cf950df915f8ea843c34ea1fb6f8e7ea21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342005"
---
# <a name="customize-and-suggest-image-search-queries"></a>Képkeresési lekérdezések testreszabása és javaslata

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ebből a cikkből megtudhatja, hogyan szabhatja testre a lekérdezéseket, és hogyan küldhet a Bing Image Search APIba küldendő keresési kifejezéseket.

## <a name="suggest-search-terms"></a>Keresési kifejezések javaslata

Ha az alkalmazásban keresési feltételek vannak megadva, akkor a [Bing AutoSuggest API](../../bing-autosuggest/get-suggested-search-terms.md) segítségével javíthatja a felhasználói élményt. Az API valós időben képes megjeleníteni a javasolt keresési kifejezéseket. Az API a javasolt lekérdezési karakterláncokat a részleges keresési feltételek és a Cognitive Services alapján adja vissza.

## <a name="pivot-the-query"></a>A lekérdezés kimutatása

Ha a Bing képes az eredeti keresési lekérdezés szegmentálására, a visszaadott [képek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektum szerepel `pivotSuggestions` . A pivot javaslatai opcionális keresési kifejezésként is megjeleníthetők a felhasználó számára. Ha például az eredeti lekérdezés a *Microsoft Surface* volt, a Bing a lekérdezést a *Microsoft* és a *Surface* szolgáltatásban is megoszthatja, és mindegyikhez javasolt kimutatásokat adhat. Ezek a javaslatok opcionális lekérdezési feltételként is megjeleníthetők a felhasználó számára.

A következő példa a *Microsoft Surface* szolgáltatásra vonatkozó kimutatási javaslatokat mutatja be:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

A `pivotSuggestions` mező azoknak a szegmenseknek a listáját tartalmazza, amelyekre az eredeti lekérdezést a rendszer felbontotta. A válasz minden szegmenshez kínál olyan [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) objektumokat, amelyek a javasolt lekérdezéseket tartalmazzák. A `text` mező tartalmazza a javasolt lekérdezést. A `displayText` mező tartalmazza azt a kifejezést, amely felváltja a kimutatást az eredeti lekérdezésben. Ilyen például a Surface kiadásának dátuma.

Ha a kimutatás lekérdezési karakterlánca az a felhasználó, aki a felhasználót keresi, használja a `text` és a `thumbnail` mezőket a pivot lekérdezési karakterláncok megjelenítéséhez. Az `webSearchUrl` URL-cím vagy az URL-cím használatával kattintson a miniatűrre és a szövegre `searchLink` . A használatával `webSearchUrl` küldheti el a felhasználót a Bing keresési eredményeinek. Ha megadja a saját eredmények oldalát, használja a t `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>A lekérdezés kibontása

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, az [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektumban szerepelni fog a `queryExpansions` mező. Ha például a lekérdezés a *Microsoft Surface* volt, a kibontott lekérdezések a következőket tehetik:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **telefon**.
- Microsoft Surface **hub**.

Az alábbi példában a *Microsoft Surface* bővített lekérdezései láthatók.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

A `queryExpansions` mező a [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) objektumok listáját tartalmazza. A `text` mező tartalmazza a kibontott lekérdezést. A `displayText` mező tartalmazza a bővítési kifejezést. Ha a kibontott lekérdezési karakterláncot a felhasználó keresi, használja a `text` és a `thumbnail` mezőket a kibontott lekérdezési karakterláncok megjelenítéséhez. Az `webSearchUrl` URL-cím vagy az URL-cím használatával kattintson a miniatűrre és a szövegre `searchLink` . A használatával `webSearchUrl` küldheti el a felhasználót a Bing keresési eredményeinek. Ha megadja a saját eredmények oldalát, használja a t `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Következő lépések

Ha még nem próbálta meg a Bing Image Search APIt, próbálkozzon [egy rövid](../quickstarts/csharp.md)útmutatóval. Ha valami összetettebbt keres, próbálja meg az oktatóanyagot egy [egyoldalas webalkalmazás](../tutorial-bing-image-search-single-page-app.md)létrehozásához.