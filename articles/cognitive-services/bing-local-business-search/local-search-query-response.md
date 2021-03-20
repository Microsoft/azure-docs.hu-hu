---
title: API-lekérdezések és válaszok küldése és használata – Bing helyi üzleti keresés
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan küldhet és használhat keresési lekérdezéseket a Bing local Business Search API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487167"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Bing helyi üzleti keresési API-lekérdezések és válaszok küldése és használata

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Helyi eredményeket kaphat a Bing helyi üzleti keresési API-ból, ha keresési lekérdezést küld a végpontjának, és tartalmazza a `Ocp-Apim-Subscription-Key` fejlécet, amelyhez szükség van. A rendelkezésre álló [fejlécekkel](local-search-reference.md#headers) és [paraméterekkel](local-search-reference.md#query-parameters)együtt a keresések testreszabhatók a keresendő terület [földrajzi határainak](specify-geographic-search.md) , valamint a visszaadott helyek [kategóriáinak](local-search-query-response.md) megadásával.

## <a name="creating-a-request"></a>Kérelem létrehozása

Ha kérést szeretne küldeni a Bing local Business Search API-nak, fűzze hozzá a keresési kifejezést a `q=` paraméterhez, mielőtt hozzáadja azt az API-végponthoz, és tartalmazza a `Ocp-Apim-Subscription-Key` fejlécet is. Például:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A kérelem URL-címének teljes szintaxisa alább látható. A kérelmek küldésével kapcsolatos további információkért tekintse meg a Bing helyi üzleti [keresési API-](quickstarts/local-quickstart.md)gyors útmutatóit és a [fejlécek](local-search-reference.md#headers) és [Paraméterek](local-search-reference.md#query-parameters) hivatkozási tartalmát. 

A helyi keresési kategóriákkal kapcsolatos információkért lásd: [keresési kategóriák a Bing local Business Search API](local-categories.md)-hoz.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Válaszok használata

A Bing helyi üzleti keresési API-ból származó JSON-válaszok egy `SearchResponse` objektumot tartalmaznak. Az API a megfelelő keresési eredményeket fogja visszaadni a `places` mezőben. Ha nem található eredmény, a `places` program nem fogja tartalmazni a választ.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Keresési eredmények attribútumai

Az API által visszaadott JSON-eredmények a következő attribútumokat tartalmazzák:

* _type
* address
* entityPresentationInfo
* georedundáns
* id
* name
* routeablePoint
* telefonon
* url

A fejlécekkel, paraméterekkel, piaci kódokkal, válasz objektumokkal, hibákkal és egyéb adatokkal kapcsolatos általános információkért lásd a [Bing helyi keresési API v7](local-search-reference.md) dokumentációját.

> [!NOTE]
> Ön vagy egy harmadik fél az Ön nevében nem használhatja fel, nem őrzi meg, nem tárolja, nem osztja meg, nem oszthatja meg és nem terjesztheti a helyi keresési API adatait a nem a Microsofttól származó szolgáltatások vagy szolgáltatások tesztelésére, fejlesztésére, betanítására, terjesztésére vagy elérhetővé tételére. 


## <a name="example-json-response"></a>Példa JSON-válaszra

A következő JSON-válasz a lekérdezés által megadott keresési eredményeket tartalmazza `?q=restaurant+in+Bellevue` .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Következő lépések
- [Helyi üzleti keresés – rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresés – Java rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti keresési csomópont rövid útmutatója](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés – Python rövid útmutató](quickstarts/local-search-python-quickstart.md)