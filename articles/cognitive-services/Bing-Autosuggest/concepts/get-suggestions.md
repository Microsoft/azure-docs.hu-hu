---
title: Keresési kifejezések javaslata a Bing Autosuggest API
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan lehet a lekérdezési kifejezéseket a Bing Autosuggest API és a lekérdezési hosszra gyakorolni a relevancia alapján.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: be7686c4d8a676d2a1d85516d2e4aa6abe3f3bfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353408"
---
# <a name="suggesting-query-terms"></a>Lekérdezési kifejezések javaslása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Általában a Bing Autosuggest API minden alkalommal, amikor egy felhasználó új karaktert keres az alkalmazás keresési mezőjében. A lekérdezési sztring teljessége hatással van az API által visszaadott javasolt lekérdezési kifejezések relevanciájára. Minél teljesebb a lekérdezési sztring, annál relevánsabb lesz a javasolt lekérdezési kifejezések listája. Előfordulhat például, hogy az API által visszaadott javaslatok `s` valószínűleg kevésbé lesznek érvényesek, mint a által visszaadott lekérdezések `sailing dinghies` .

## <a name="example-request"></a>Példakérelem

A következő példa egy olyan kérést mutat be, amely a *sail* sztringre adja vissza a javasolt lekérdezési sztringeket. Ne felejtse el URL-címként kódolni a felhasználó részleges lekérdezési kifejezését a [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) lekérdezési paraméter beállításakor. Ha például a felhasználó a *sailing les* sztringet adta meg, állítsa a `q` paramétert `sailing+les` vagy `sailing%20les` értékre.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Az alábbi válasz [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction)-objektumok egy listáját tartalmazza, amelyek a javasolt lekérdezési kifejezéseket tartalmazzák.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Javasolt lekérdezési feltételek használata

Minden javaslat tartalmaz egy `displayText`, `query` és `url` mezőt. A `displayText` mező tartalmazza a javasolt lekérdezést, amelyet a keresőmező legördülő listájának feltöltéséhez használ. A válaszban szereplő összes javaslatot meg kell jelenítenie az adott sorrendben.

A következő példa egy legördülő keresőmezőt mutat be a Bing Autosuggest API javasolt lekérdezési kifejezéssel.

![Autosuggest legördülő keresőmező-lista](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Ha a felhasználó kiválaszt egy javasolt lekérdezést a legördülő listából, használja a lekérdezési kifejezést a `query` mezőben a [Bing Web Search API](../../bing-web-search/overview.md) meghívásához és a találatok megjelenítéséhez. Használhatja az URL-címet is az `url` mezőben, ha a felhasználót inkább a Bing keresési eredményeinek oldalára szeretné küldeni.

## <a name="next-steps"></a>Következő lépések

* [Mi az a Bing Autosuggest API?](../get-suggested-search-terms.md)