---
title: Szemantikai válasz szerkezete
titleSuffix: Azure Cognitive Search
description: Ismerteti a Cognitive Search szemantikai rangsorolási algoritmusát, valamint azt, hogy miként lehet egy eredményhalmaz "szemantikai válaszokat" és "szemantikai feliratokat" létrehozni.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679868"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Szemantikai rangsorolás és válaszok az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai rangsorolási algoritmus és a szemantikai válaszok/feliratok nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők.

A szemantikai rangsorolás javítja a keresési eredmények pontosságát azáltal, hogy a legfelső egyezéseket egy olyan szemantikai rangsorolási modell használatával alakítja át, amely természetes nyelven, a kulcsszavak helyett a lekérdezésekhez van betanítva.

Ez a cikk ismerteti a szemantikai rangsorolási algoritmust, valamint azt, hogy a szemantikai válasz hogyan legyen formázva. A válasz tartalmaz feliratokat is, egyszerű szövegben, valamint a csúcsfényekkel és a válaszokkal (opcionális).

+ A szemantikai feliratok a keresési eredményekből kinyert lekérdezéshez kapcsolódó szöveges szövegrészek. Segíthetnek összefoglalni az eredményeket, ha az egyes tartalmi mezők túl nagyok az eredmények laphoz. A feliratok funkció szemantikai kiemelik, így a felhasználók gyorsan lekérhetik a lekérdezés eredményét, hogy megtalálják a legfontosabb dokumentumokat, ami javítja a teljes felhasználói élményt.

+ A szemantikai válaszok gépi tanulási modelleket használnak a Bingtől, hogy válaszokat alakítsanak ki a kérdésekre hasonlító lekérdezésekre. A válaszok a lekérdezéshez leginkább kapcsolódó, a lekérdezési eredményhalmaz legfelső dokumentumában kinyert részek listájából vannak kiválasztva. A válaszok független, legfelső szintű objektumként lesznek visszaadva a lekérdezési válasz adattartalmában, amelyet a keresési lapokon, az oldalsó keresési eredmények mentén lehet megjeleníteni.

## <a name="prerequisites"></a>Előfeltételek

+ A szemantikai lekérdezési típussal kialakított lekérdezések. További információt a [szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md)című témakörben talál.

## <a name="understanding-a-semantic-response"></a>Szemantikai válasz megértése

A szemantikai válasz a pontszámok, a feliratok és a válaszok új tulajdonságait tartalmazza. A [teljes szöveges keresőmotor](search-lucene-query-architecture.md)által visszaadott első 50 eredmény, amely a szemantikai rangsorban újra rangsorolva van, és a szabványos válaszból épül fel. Ha több mint 50 van megadva, a további eredmények is visszakerülnek, de nem lesznek szemantikailag újra rangsorolva.

Ahogy az összes lekérdezés esetében, a válasz a beolvasható, vagy csak a SELECT utasításban felsorolt mezőkből áll. Ide tartozik a "válasz" és a "feliratok" is.

+ Az egyes szemantikai eredmények esetében alapértelmezés szerint egyetlen "válasz" szerepel, amelyet a rendszer a keresési oldalon megjeleníthető különálló mezőként ad vissza. Legfeljebb öt adható meg. A válasz létrehozása automatizált: a kezdeti eredmények összes dokumentumának olvasása, a kinyert összefoglalás futtatása, majd a gépi olvasási szövegértés, végül a válasz mezőben a felhasználó kérdéseire irányuló közvetlen válasz előléptetése.

+ A "Caption" a dokumentumok tartalmának kinyert összegzése, amely egyszerű szövegben vagy kiemeli a kiemeléseket. A feliratok automatikusan szerepelnek, és nem lehet letiltani. A Kiemelt hangfelismerések a gépi olvasási felolvasással határozzák meg, hogy mely karakterláncok legyenek kiemelve. Kiemeli a legfontosabb részek figyelmét, így gyorsan beolvashatja az eredmények oldalát, hogy megtalálja a megfelelő dokumentumot.

A szemantikailag újra rangsorolt eredményhalmaz az érték alapján állítja be az eredményeket @search.rerankerScore . Ha hozzáad egy OrderBy záradékot, a rendszer HTTP 400-es hibát ad vissza, mivel ez a záradék szemantikai lekérdezésekben nem támogatott.

## <a name="example"></a>Példa

A a @search.rerankerScore standard mellett létezik, @search.score és az eredményeket újra rangsorolja.

A következő lekérdezésnek kell megadnia:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A következő eredmény jelenik meg, amely egy szemantikai választ reprezentál. Ezek az eredmények csak az első három választ mutatják, amelyet a " @search.rerankerScore " rangsorol. Figyelje meg, hogy a Oceanside Resort mostantól elsőként van rangsorolva. A "" alapértelmezett rangsorolása alatt ezt az eredményt a rendszer @search.score második helyen adja vissza, a nyomvonalak befejezése után.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Következő lépések

+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)
+ [Hasonlósági algoritmus](index-ranking-similarity.md)
+ [Szemantikai lekérdezés létrehozása](semantic-how-to-query-request.md)
+ [Alapszintű lekérdezés létrehozása](search-query-create.md)