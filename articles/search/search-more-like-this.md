---
title: moreLikeThis (előzetes verzió) lekérdezési funkció
titleSuffix: Azure Cognitive Search
description: Ismerteti a moreLikeThis (előzetes verzió) szolgáltatást, amely az Azure Cognitive Search REST API előzetes verzióiban érhető el.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1c2f8058a85bbc0643ed31a7dc910339d0f6d9dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94697050"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (előzetes verzió) az Azure Cognitive Search

> [!IMPORTANT] 
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2020-06-30-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál vagy .NET SDK-támogatás.

`moreLikeThis=[key]` a a [keresési dokumentumok API](/rest/api/searchservice/search-documents) lekérdezési paramétere, amely a dokumentum kulcsában megadott dokumentumhoz hasonló dokumentumokat keres. Ha keresési kérelem történik `moreLikeThis` , a rendszer egy lekérdezést hoz létre az adott dokumentumból kinyert keresési kifejezésekkel, amelyek az adott dokumentumot a legmegfelelőbb módon írják le. A rendszer ezután a generált lekérdezést használja a keresési kérelem elvégzéséhez. Alapértelmezés szerint az összes kereshető mező tartalma megtekinthető, mínusz a paraméterrel megadott korlátozott mezők `searchFields` . A `moreLikeThis` paraméter nem használható a keresési paraméterrel `search=[string]` .

Alapértelmezés szerint a rendszer a legfelső szintű kereshető mezők tartalmát veszi figyelembe. Ha inkább konkrét mezőket szeretne megadni, használhatja a `searchFields` paramétert. 

`MoreLikeThis`Egy [összetett típus](search-howto-complex-data-types.md)kereshető almezőiben nem használható.

## <a name="examples"></a>Példák

Az alábbi példák a gyors üzembe helyezési pontról származó szállodákat használják [: hozzon létre keresési indexet a Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Egyszerű lekérdezés

A következő lekérdezés megkeresi azokat a dokumentumokat, amelyek leírás mezőjének legtöbbje hasonlít a forrás dokumentum mezőjéhez a `moreLikeThis` paraméterben megadott módon:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

Ebben a példában a kérelem a 29-ös verzióhoz hasonló szállodákat keres `HotelId` .
A HTTP GET használata helyett a `MoreLikeThis` http post használatával is meghívhat:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Szűrők alkalmazása

`MoreLikeThis` kombinálható más gyakori lekérdezési paraméterekkel, például a következővel: `$filter` . A lekérdezés például csak olyan szállodákra korlátozható, amelyek kategóriája "költségvetés", és ahol a minősítés magasabb, mint 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Mezők kiválasztása és az eredmények korlátozása

A `$top` választó segítségével korlátozható, hogy egy lekérdezés hány eredményt adjon vissza `MoreLikeThis` . Emellett a mezőket is kiválaszthatja `$select` . Itt az első három Hotel van kiválasztva az AZONOSÍTÓval, a névvel és a minősítéssel együtt: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Következő lépések

A szolgáltatással való kísérletezéshez bármilyen webes tesztelési eszközt használhat.  Javasoljuk, hogy ehhez a gyakorlathoz a Poster-t használja.

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search REST API-k megismerése](search-get-started-rest.md)