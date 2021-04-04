---
title: Válaszok megjelenítése a rangsor használatával – Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a rangsor a Bing Entity Search API által visszaadott válaszok megjelenítéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94365635"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Rangsorolás használata az entitások keresési eredményeinek megjelenítéséhez  

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Minden entitás keresési válasza tartalmaz egy [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogyan kell megjeleníteni a Bing Entity Search API által visszaadott keresési eredményeket. A rangsorolási válaszok a pole, a fővonal és a Sidebar tartalmát eredményezik. A pole eredmény a legfontosabb vagy legjelentősebb eredmény, és elsőként kell megjelennie. Ha nem jeleníti meg a fennmaradó eredményeket egy hagyományos fővonal-és oldalsáv-formátumban, meg kell adnia a fővonali tartalmat, mint az oldalsáv tartalma. 
  
Az [Items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) Array az egyes csoportokon belül azonosítja azt a sorrendet, amelyben a tartalomnak szerepelnie kell. Mindegyik elem két módszert biztosít az eredmény azonosítására a válaszon belül.  
 

|Mező | Leírás  |
|---------|---------|
|`answerType` és `resultIndex` | `answerType` azonosítja a választ (vagy az entitást vagy a helyet), és `resultIndex` azonosítja a válaszon belüli eredményt (például egy entitást). Az index 0 karakternél kezdődik.|
|`value`    | `value` Egy olyan azonosítót tartalmaz, amely a válasz vagy eredmény AZONOSÍTÓjának felel meg a válaszon belül. Vagy a válasz vagy az eredmény tartalmazza az azonosítót, de nem mindkettőt. |
  
A `answerType` és a `resultIndex` két lépésből álló folyamat. Először a használatával `answerType` azonosíthatja a megjelenítendő eredményeket tartalmazó választ. Ezután `resultIndex` a paranccsal indexelheti a válasz eredményét, hogy megjelenjen az eredmény. (Az `answerType` érték a mező neve a [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektumban.) Ha azt szeretné, hogy a válasz összes eredménye együtt jelenjen meg, a rangsorolási válasz elem nem tartalmazza a `resultIndex` mezőt.

Az azonosító használatához meg kell egyeznie a rangsor azonosítójának és a válasz AZONOSÍTÓjának, vagy az egyik eredményének. Ha egy válasz objektum tartalmaz egy `id` mezőt, a válasz összes eredményét együtt jeleníti meg. Ha például az `Entities` objektum tartalmazza a `id` mezőt, az összes entitást együtt jeleníti meg. Ha az `Entities` objektum nem tartalmazza a `id` mezőt, akkor minden entitás tartalmaz egy `id` mezőt, és a rangsorolási válasz összekeveri az entitásokat a helyek eredményeivel.  
  
## <a name="ranking-response-example"></a>Rangsorolási válasz példája

A következő példa egy [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)mutat be.
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Ezen rangsorolási válasz alapján az oldalsáv megjeleníti a Jimi Hendrix-hez kapcsolódó két entitás eredményét.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-entities-search-single-page-app.md)