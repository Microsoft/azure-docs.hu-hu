---
title: Keresési kategóriák a Bing helyi üzleti keresési API-hoz
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan adhatja meg a keresési kategóriákat a Bing local Business Search API-végponthoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487184"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Keresési kategóriák a Bing helyi üzleti keresési API-hoz

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing helyi üzleti keresési API lehetővé teszi a helyi üzleti entitások különböző kategóriákban való keresését, és prioritást biztosít a felhasználó helyének bezárásához. Ezeket a kereséseket a `localCircularView` és a `localMapView` [paraméterekkel](specify-geographic-search.md)együtt is felveheti.


## <a name="toplevel-categories"></a>TopLevel-kategóriák 

A következő típusok a keresés főbb kategóriáit határozzák meg.  Több kategória is megadható a paraméterhez rendelt vesszővel tagolt lista használatával `localCategories` .  
- EatDrink 
- SeeDo 
- Shop 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Ideiglenes 
- Kórházak 

## <a name="sub-categories"></a>Alkategóriák
Az alkategóriák ugyanolyan módon lesznek átadva, mint a `localCategories` . Az alkategóriák konkrétabb kategóriák. Az alárendeltek abban az értelemben, hogy ha a C kategóriát és annak egyik alkategóriáját adja meg ugyanazon a vesszővel tagolt listában, akkor ugyanazokat az eredményeket kapja meg, mint ha csak a C értéket adta meg.

### <a name="eat-drink"></a>Egyél inni

> BreweriesAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, bagels, BarbecueRestaurants, kocsmák, SportsBars, bárok, BarsGrillsAndPubs, BuffetRestaurants | BelgianRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChineseRestaurants, CoffeeAndTea, csemegeüzletekben, DeliveryService, Diners, DiscountStores, fánkok, gyorsétterem, FrenchRestaurants, FrozenYogurt, GermanRestaurants, szupermarketek, GreekRestaurants, élelmiszerboltok, HawaiianRestaurants, HungarianRestaurants, IceCreamAndFrozenDesserts, IndianRestaurants, ItalianRestaurants, JapaneseRestaurants, Juice, KoreanRestaurants, LiquorStores, MexicanRestaurants, MiddleEasternRestaurants, pizza, PolishRestaurants, PortugueseRestaurants, perec, éttermek, RussianAndUkrainianRestaurants, szendvicsek, SeafoodRestaurants, SpanishRestaurants, SteakHouseRestaurants, SushiRestaurants, elvihető, ThaiRestaurants, TurkishRestaurants, VegetarianAndVeganRestaurants, VietnameseRestaurants

### <a name="see-do"></a>Lásd: do

> AmusementParks, attractions, karnevál, kaszinók, LandmarksAndHistoricalSites, MiniatureGolfCourses, MovieTheaters, múzeumok, parkok, SightseeingTours, TouristInformation, állatkertek

### <a name="shop"></a>Shop

> AntiqueStores, könyvesboltok, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores


## <a name="examples-of-local-categories-search"></a>Példák a helyi kategóriák keresésére

Az alábbi példák a paraméternek megfelelően SZEREZnek eredményt `localCategories` :

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

A következő lekérdezés korlátozza a "kórházi" eredmények számát a Bing helyi üzleti keresési API-ból az első három visszaadott értékre:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

A következő példában szereplő JSON-válasz három kórházat tartalmaz a Seattle területén:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Következő lépések
- [Földrajzi keresési határok](specify-geographic-search.md)
- [Lekérdezés és válasz](local-search-query-response.md)
- [Gyors útmutató C-ben #](quickstarts/local-quickstart.md)