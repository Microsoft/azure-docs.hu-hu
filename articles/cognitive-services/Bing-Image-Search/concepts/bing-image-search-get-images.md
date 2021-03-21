---
title: Képek beolvasása a web-Bing Image Search API
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API használatával megkeresheti és lekérheti a kapcsolódó képeket a webről.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342056"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Képek letöltése a webről a Bing Image Search API

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ha a Bing Image Search REST API használja, a következő GET kérelem elküldésével beolvashatja a keresési kifejezéshez kapcsolódó képeket a webről:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Használja az URL-kódolású keresési kifejezés [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) Query paraméterét. Ha például a *vitorlás mentőcsónakokra* adja meg, akkor a következőre: `q` `sailing+dinghies` vagy `sailing%20dinghies` .

> [!IMPORTANT]
> * Az összes kérelmet egy-kiszolgálóról kell elvégezni, nem pedig az ügyféltől.
> * Ha első alkalommal hívja meg a Bing Search API-kat, ne adja meg az ügyfél-azonosító fejlécét. Csak akkor adja meg az ügyfél-azonosítót, ha korábban olyan Bing API-t hívott, amely a felhasználó és az eszköz kombinációjának ügyfél-AZONOSÍTÓját adta vissza.

## <a name="get-images-from-a-specific-web-domain"></a>Képek beolvasása egy adott webes tartományból

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) lekérdezési operátort.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Az operátort használó lekérdezésekre adott válaszok a `site:` [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) beállítástól függetlenül is tartalmazhatnak felnőtt tartalmat. Csak akkor használja `site:` , ha ismeri a tartalmat a tartományban.

## <a name="filter-images"></a>Képek szűrése

 Alapértelmezés szerint a Image Search API a lekérdezéshez kapcsolódó összes lemezképet adja vissza. Ha szűrni szeretné a Bing által visszaadott lemezképeket (például csak transzparens háttérrel vagy adott mérettel rendelkező képeket kíván visszaadni), használja a következő lekérdezési paramétereket:

* [aspektus](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)– képek szűrése oldalarány alapján (például standard vagy széles képernyős kép).
* [Color (szín](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)) – a képeket domináns színnel vagy fekete-fehérre szűri.
* [frissesség](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)– képek szűrése életkor szerint (például a Bing által az elmúlt héten felderített képek).
* [magasság](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [szélesség](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)– képek szűrése szélesség és magasság alapján.
* [imageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)– képek szűrése tartalom alapján (például képek, amelyek csak a személy arcát mutatják).
* [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)– képek szűrése típus szerint (például ClipArt, animált GIF vagy transzparens hátterek).
* [licenc](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)– a rendszerképek szűrése a helyhez társított licenc típusa alapján.
* [Size (méret](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)) – a képek méret szerint szűrhetők, például a kis képek akár 200x200 képpont is.

Ha egy konkrét tartományban található képeket szeretne lekérni, használja a [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) lekérdezési operátort.

Az alábbi példa bemutatja, hogyan szerezhet be kisméretű képeket a Bing által az elmúlt héten felderített ContosoSailing.com.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bing Image Search válasz formátuma

A Bing válaszüzenete olyan [rendszerképeket](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) tartalmaz, amelyek a lekérdezés szempontjából fontosnak Cognitive Services rendszerképek listáját tartalmazzák. A listában szereplő összes képobjektum a következő információkat tartalmazza a [rendszerképpel kapcsolatban](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) : az URL-cím, a mérete, a méretei, a kódolási formátuma, a rendszerkép MINIATŰRje URL-címe és a miniatűr méretei.

> [!NOTE]
> * A rendszerképeket a válaszban megadott sorrendben kell megjeleníteni.
> * Mivel az URL-formátumok és a paraméterek előzetes értesítés nélkül változhatnak, használja az összes URL-címet. Ne vegyen fel függőségeket az URL-formátumra vagy a paraméterekre, kivéve, ha ez nincs feltüntetve.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Amikor meghívja a Bing Image Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. A válasz `totalEstimatedMatches` mezője az összes megtekinthető kép becsült számát adja meg. További információ a képek többi részéről: [lapozási képek](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Következő lépések

Ha még nem próbálta meg a Bing Image Search APIt, próbálkozzon [egy rövid](../quickstarts/csharp.md)útmutatóval. Ha valami összetettebbt keres, próbálja meg az oktatóanyagot egy [egyoldalas webalkalmazás](../tutorial-bing-image-search-single-page-app.md)létrehozásához.