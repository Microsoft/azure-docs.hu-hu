---
title: Gyakori kérdések (GYIK) – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API kapcsolódó fogalmakkal, kóddal és forgatókönyvekkel kapcsolatos gyakori kérdésekre adott válaszokat talál.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68881694"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Gyakran ismételt kérdések (GYIK) a Bing Image Search API

Válaszok az Azure-beli Microsoft Cognitive Services Bing Image Search API kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.

## <a name="response-headers-in-javascript"></a>Válaszok fejlécei a JavaScriptben

A következő fejlécek fordulhatnak elő a Bing Image Search API válaszában.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |A Bing által a felhasználóhoz hozzárendelt egyedi azonosító |
| `BingAPIs-Market`   |A kérelem teljesítéséhez használt piac |
| `BingAPIs-TraceId`  |A kéréshez tartozó Bing API-kiszolgálón található naplóbejegyzés (támogatáshoz) |

Különösen fontos az ügyfél-azonosító megőrzése, és a későbbi kérelmekkel való visszaküldése. Ha ezt teszi, a keresés a rangsorolás keresési eredményei között a múltbeli kontextust fogja használni, és egységes felhasználói élményt nyújt.

Ha azonban a Bing Image Search API a JavaScriptből hívja meg, a böngésző beépített biztonsági funkciói (CORS) megakadályozhatják a fejlécek értékének elérését.

A fejlécek eléréséhez a Bing Image Search API kérelmet CORS-proxyn keresztül teheti meg. Az ilyen proxyk válasza rendelkezik egy `Access-Control-Expose-Headers` fejléccel, amely engedélyezési listára teszi a válaszfejléceket, és elérhetővé teszi őket a JavaScript számára.

Egyszerűen telepíthet CORS-proxyt, hogy az oktatóanyag- [alkalmazás](tutorial-bing-image-search-single-page-app.md) hozzáférhessen a választható ügyfél-fejlécekhez. Első lépésként [telepítse a Node.js-t](https://nodejs.org/en/download/), ha még nem tette meg. Ezután írja be a következő parancsot a parancssorba.

    npm install -g cors-proxy-server

Ezután módosítsa a HTML-fájlban lévő Bing Image Search API végpontot a következőre:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Végül indítsa el a CORS-proxyt a következő paranccsal:

    cors-proxy-server

Ne zárja be a parancsablakot, amíg használja az oktatóalkalmazást; az ablak bezárása leállítja a proxyt. A bővíthető HTTP-fejlécek szakaszában, a keresési eredmények alatt, most már az `X-MSEdge-ClientID` fejléc is megjelenik, és ellenőrizheti, hogy ugyanaz a fejléc szerepel-e minden kérésnél.

## <a name="response-headers-in-production"></a>Válasz fejlécei éles környezetben

Az előző válaszban leírt CORS-proxy módszer a fejlesztés, a tesztelés és a tanulás számára megfelelő.

Éles környezetben azonban olyan kiszolgálóoldali parancsfájlt kell üzemeltetni, amely a Bing Web Search APIt használó weboldallal azonos tartományban található. Ennek a szkriptnek ténylegesen el kell végeznie az API-hívásokat a weblap JavaScript-kérelme alapján, és át kell adnia az összes találatot, beleértve a fejléceket, vissza kell térnie az ügyfélhez Mivel a két erőforrás (lap és parancsfájl) megosztja a forrást, a CORS nem válik lejátszásra, a speciális fejlécek pedig elérhetők a JavaScript számára a weblapon.

Ez a megközelítés az API-kulcs védelmét is védi a nyilvánosság számára, mivel csak a kiszolgálóoldali parancsfájlra van szükség. A parancsfájl egy másik módszert is használhat (például a HTTP-hivatkozót), hogy a kérelem engedélyezve legyen.

## <a name="next-steps"></a>További lépések

Egy hiányzó funkcióval vagy funkcióval kapcsolatos kérdése van? Javasoljuk, hogy a [felhasználói hang webhelyére](https://cognitive.uservoice.com/forums/555907-bing-search)kérje vagy szavazzon rá.

## <a name="see-also"></a>Lásd még

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)
