---
title: Adatátviteli kérelmek továbbítása Microsoft Azure Maps mobilitási szolgáltatásokkal (előzetes verzió)
description: Megtudhatja, hogyan használhatja a Azure Maps mobilitási szolgáltatásokat (előzetes verzió) a nyilvános árutovábbítási adatforgalom (például a Metro terület azonosítói, a továbbítási leállások, az útvonalak és az útvonal-útvonalak) igényléséhez.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 740080d742f535f868b2ae194b24bebe5ac6ac24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906029"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>A Azure Maps mobilitási szolgáltatásokkal (előzetes verzió) kapcsolatos nyilvános adatátviteli kérelmek 

> [!IMPORTANT]
> Azure Maps mobilitási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ebből a cikkből megtudhatja, hogyan használhatja a Azure Maps [mobilitási szolgáltatásokat](/rest/api/maps/mobility) a nyilvános árutovábbítási adatforgalom igényléséhez. Az árutovábbítási adatok közé tartozik az átvitel leállítása, az útvonal-információ és az utazási idő becslése.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* Metro-körzet AZONOSÍTÓjának beolvasása a [Metro-körzet API](/rest/api/maps/mobility/getmetroareapreview) beolvasása használatával
* A közeli átvitel a [közeli tranzit](/rest/api/maps/mobility/getnearbytransitpreview) szolgáltatás használatával leáll.
* Lekérdezés a [tranzit Routes API](/rest/api/maps/mobility/gettransitroutepreview) -val a nyilvános átvitelt használó útvonal megtervezéséhez.
* A továbbítási útvonal geometriájának és az útvonal részletes ütemtervének kérése az [átviteli útvonal beolvasása API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)használatával.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="get-a-metro-area-id"></a>Metro-körzet AZONOSÍTÓjának beolvasása

Ahhoz, hogy részletes információkat kérjen a tranzit-ügynökségekről és egy adott nagyvárosi terület támogatott továbbítási típusairól, `metroId` erre a területre szüksége lesz. A [Metro Area API beolvasása](/rest/api/maps/mobility/getmetroareapreview) lehetővé teszi, hogy olyan metró területeket kérjen, amelyekben elérhetők a Azure Maps mobilitási szolgáltatások. A válasz olyan részleteket tartalmaz, mint a `metroId` , `metroName` és a Metro terület geometriájának ábrázolása GeoJSON formátumban.

Tegyük fel, hogy beolvassa a Metro területet a Seattle-Tacoma Metro területi AZONOSÍTÓhoz. A Metro-területek AZONOSÍTÓjának kéréséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Poster alkalmazást, és hozzon létre egy gyűjteményt a kérések tárolásához. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki azt a gyűjteményt, amelyet az előző lépésben hozott létre a kérelem mentési helyeként. Ezután válassza a **Mentés** lehetőséget.
  
    ![Kérelem létrehozása a Poster-ban](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a http **beolvasása** metódust a Builder (szerkesztő) lapon, majd a Get kérelem létrehozásához adja meg a következő URL-címet. Cserélje le a értékét a `{subscription-key}` Azure Maps elsődleges kulcsára.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Sikeres kérés után a következő választ kapja:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>A közeli tranzit leállási kérések

A Azure Maps a [közeli tranzit](/rest/api/maps/mobility/getnearbytransitpreview) szolgáltatás lehetővé teszi az átviteli objektumok keresését. Az API visszaadja az árutovábbítási objektum részleteit, például a nyilvános átvitel leállását és az adott hely körüli közös kerékpárokat. Ezután egy kérést küldünk a szolgáltatásnak, hogy megkeresse a közeli nyilvános tranzitot a megadott hely körüli 300 méteres sugaron belül.

Az alábbi lépések végrehajtásával teheti meg a lekéréses [továbbítást a közeli átvitelre](/rest/api/maps/mobility/getnearbytransitpreview):

1. A Poster területen kattintson az **új kérelem**  |  **Get kérelem** és név lehetőségre, amely a **közeli leáll**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz az alábbi kérelem URL-címét, majd kattintson a **Küldés** gombra.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Sikeres kérés után a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

Ha alaposan megfigyeli a válasz struktúráját, látni fogja, hogy minden egyes árutovábbítási objektumhoz paramétereket tartalmaz. Az egyes árutovábbítási objektumok olyan paraméterekkel rendelkeznek, mint a,,,, `id` `type` `stopName` `mainTransitType` `mainAgencyName` és az objektum koordinátáiban található pozíciója.

A tanulás érdekében a `id` következő szakaszban egy, az útvonalhoz tartozó busz leáll a forrásként.  

## <a name="request-a-transit-route"></a>Átviteli útvonal kérése

A Azure Maps [Transit Routes API](/rest/api/maps/mobility/gettransitroutepreview) lehetővé teszi az utazás megtervezését. A lehető legjobb útvonalat adja vissza a forrásról a célhelyre. A szolgáltatás különböző utazási módokat biztosít, többek között a gyaloglást, a kerékpározást és a nyilvános átvitelt. Ezután a legközelebbi buszmegállóból keresünk egy útvonalat a Seattle-i Space tű toronyba.

### <a name="get-location-coordinates-for-destination"></a>Hely koordinátáinak beolvasása célhelyként

A Space tű Tower hely koordinátáinak beszerzéséhez használjuk a Azure Maps [fuzzy Search szolgáltatást](/rest/api/maps/search/getsearchfuzzy).

Az alábbi lépések végrehajtásával teheti meg a kérést a fuzzy Search szolgáltatásnak:

1. A Poster területen kattintson az **új kérelem**  |  **Get kérelem** elemre, és nevezze el a **hely koordinátáit**.

2. A Builder (szerkesztő) lapon válassza a http **beolvasása** metódust, adja meg a kérelem URL-címét, majd kattintson a **Küldés** gombra.

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. Ha alaposan megtekinti a választ, több helyet is tartalmaz az eredmények között a Space tű kereséséhez. Minden eredmény tartalmazza a hely koordinátáit a **pozícióban**. Másolja a `lat` és az `lon` első eredmény **pozícióját** .

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>Kérelem útvonala

Egy útvonal-kérelem elvégzéséhez hajtsa végre az alábbi lépéseket:

1. A Poster lapon kattintson az **új kérelem**  |  **Get kérelem** elemre, és nevezze el az **útválasztási adatokat**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz az alábbi kérelem URL-címét, majd kattintson a **Küldés** gombra.

    A és a paraméterek megadásával egy buszra vonatkozó nyilvános átviteli útvonalakat fogunk kérni `modeType` `transitType` . A kérelem URL-címe tartalmazza az előző szakaszban lekért helyet. A esetében már `originType` van egy **stopId**. A esetében pedig `destionationType` a **pozíciónk**.

    Tekintse meg a kérésben használható [URI-paraméterek listáját](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters) a [Transit Routes API beszerzéséhez](/rest/api/maps/mobility/gettransitroutepreview).
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Sikeres kérés esetén a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. Ha alaposan megfigyel, több **busz** útvonala is van a válaszban. Minden útvonal egyedi útvonal- **azonosítóval** rendelkezik, amely az útvonal minden egyes szakaszát leírja, valamint egy, `itineraryFare` a Bus jegyek tételes és teljes árát is. A Route láb a két leállítási útpont közötti útvonal része. Ezután a válaszban a leggyorsabb útvonal részleteit fogjuk kérni `itineraryId` .

## <a name="request-fastest-route-itinerary"></a>Leggyorsabb útvonal-útvonal kérése

A Azure Maps [Transit útvonal](/rest/api/maps/mobility/gettransititinerarypreview) -szolgáltatás lehetővé teszi, hogy egy adott útvonalra vonatkozó adatokat igényeljen az [átviteli útvonalak API](/rest/api/maps/mobility/gettransitroutepreview) szolgáltatása által visszaadott útvonal útvonal- **azonosítójának** használatával. A kérelem végrehajtásához hajtsa végre az alábbi lépéseket:

1. A Poster lapon kattintson az **új kérelem**  |  **Get kérelem** elemre, és nevezze el az **átvitelhez szükséges adatokat**.

2. A Builder (szerkesztő) lapon válassza a http **beolvasása** módszert. Adja meg az API-végponthoz a következő kérelem URL-címét, majd kattintson a **Küldés** gombra.

    A `detailType` paramétert a **geometriai** értékre állítja be, hogy a válasz tartalmazza a nyilvános átvitel leállítási információit, és kapcsolja be a navigálást az útvonalon a gyaloglás és a kerékpáros lábak számára.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. Sikeres kérés esetén a válasz struktúrájának az alábbihoz hasonlóan kell kinéznie.  Ha betartja a JSON-választ, megfigyelheti, hogy minden egyes busz tartalmaz egy `legfare` elemet. Az `legfare` elem tartalmazza a külön megvásárolható autóbusz-útvonalak díjait (százalékban). A válasz végén egy olyan elem jelenik meg, amely a `itineraryFare` teljes útvonal költségeit (százalékban) tartalmazza. Ebben a példában négy buszos útvonal található `$2.75` . Ha azonban a teljes útvonalra egyetlen jegyet vásárol, a díj a következő: `$5.50` .

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet valós idejű adatszolgáltatásokat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatkérések igénylése](how-to-request-real-time-data.md)

A Azure Maps mobilitási szolgáltatások (előzetes verzió) API dokumentációjának megismerése

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatások dokumentációja](/rest/api/maps/mobility)