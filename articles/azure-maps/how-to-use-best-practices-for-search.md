---
title: Ajánlott eljárások Azure Maps Search Servicehoz | Microsoft Azure térképek
description: Ismerje meg, hogyan alkalmazhatja az ajánlott eljárásokat a Search Service Microsoft Azure Maps-ből való használatakor.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895477"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Ajánlott eljárások Azure Maps Search Service

A Azure Maps [Search Service](/rest/api/maps/search) olyan API-kat tartalmaz, amelyek különböző képességeket biztosítanak a fejlesztők számára a címek, a helyek, az üzleti listák név vagy kategória szerinti kereséséhez, valamint más földrajzi információkhoz. A[zavaros keresési API](/rest/api/maps/search/getsearchfuzzy) például lehetővé teszi, hogy a felhasználók megkeressék az adott címeket vagy érdekes pontokat (POI).

Ez a cikk azt ismerteti, hogyan alkalmazhatók a helyes eljárások a Azure Maps Search Service adatainak meghívásakor. A következőket fogja megtanulni:
> [!div class="checklist"]
> * Lekérdezések készítése a megfelelő egyezések visszaküldéséhez
> * Keresési eredmények korlátozása
> * Az eredményhalmaz közötti különbségek megismerése
> * A címek keresése – válasz struktúra beolvasása

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

Ez a cikk a [Poster alkalmazást](https://www.postman.com/downloads/) használja a REST-hívások létrehozásához, de bármelyik API-fejlesztési környezetet kiválaszthatja.

## <a name="best-practices-to-geocode-addresses"></a>Ajánlott eljárások geocode-címekhez

Ha Azure Maps Search Service használatával keres teljes vagy részleges címeket, az API beolvassa a kulcsszavakat a keresési lekérdezésből. Ezután visszaadja a címe hosszúsági és szélességi koordinátáit. Ezt a folyamatot *helymeghatározáshoz* nevezzük.

Az ország/régió geocode képessége a közúti adatok rendelkezésre állásának és a helymeghatározáshoz szolgáltatás pontosságának függvénye. Ha további információt szeretne az ország vagy régió Azure Maps helymeghatározáshoz képességeiről, tekintse meg a [helymeghatározáshoz lefedettségét](./geocoding-coverage.md)ismertető témakört.

### <a name="limit-search-results"></a>Keresési eredmények korlátozása

 A Azure Maps Search API a keresési eredmények megfelelő korlátozásához nyújt segítséget. Korlátozza az eredményeket, hogy a kapcsolódó adatokat a felhasználók számára is megjelenítse.

> [!NOTE]
> A keresési API-k több paramétert is támogatnak, mint amit a cikkben tárgyal.

#### <a name="geobiased-search-results"></a>Geobiased keresési eredményei

Ahhoz, hogy a felhasználó a megfelelő területen geobias az eredményeket, mindig adja hozzá a lehető legtöbb helyet. Előfordulhat, hogy a keresési eredményeket néhány bemeneti típus megadásával szeretné korlátozni:

* Állítsa be a `countrySet` paramétert. Beállíthatja `US,FR` például a következőre:. Alapértelmezés szerint az API a teljes világot keresi, így szükségtelen eredményeket adhat vissza. Ha a lekérdezés nem rendelkezik `countrySet` paraméterrel, akkor előfordulhat, hogy a keresés pontatlan eredményeket ad vissza. Egy *Bellevue* nevű város keresése például az Egyesült Államok és Franciaország eredményét adja vissza, mivel mindkét ország/régió egy *Bellevue* nevű várost tartalmaz.

* A `btmRight` és a paramétereket a `topleft` határolókeret beállítására használhatja. Ezek a paraméterek a térképen meghatározott területekre korlátozzák a keresést.

* Az eredmények fontossági területének befolyásolásához adja meg a `lat` és a `lon` koordináta paramétert. A (z `radius` ) paraméter használatával állítsa be a keresési terület sugarát.


#### <a name="fuzzy-search-parameters"></a>Fuzzy keresési paraméterek

Javasoljuk, hogy használja a Azure Maps [Search FUZZY API](/rest/api/maps/search/getsearchfuzzy) -t, ha nem tudja, hogy a felhasználói bemenetek egy keresési lekérdezéshez. A felhasználótól származó bevitel például lehet egy címnek vagy egy érdekes helynek (POI) a típusa, például a *Shopping Mall*. Az API egy kanonikus *egysoros kereséshez* ÖTVÖZI a POI-kereséseket és a helymeghatározáshoz: 

* A `minFuzzyLevel` és `maxFuzzyLevel` paraméterek segítenek a releváns egyezések visszaadásában, még akkor is, ha a lekérdezési paraméterek nem egyeznek pontosan a felhasználó által kívánt információkkal. A teljesítmény maximalizálása és a szokatlan eredmények csökkentése érdekében a keresési lekérdezéseket a és a alapértelmezett értékeire állítsa be `minFuzzyLevel=1` `maxFuzzyLevel=2` . 

    Ha például a paraméter értéke `maxFuzzyLevel` 2, a  *restrant* kifejezés a következőhöz igazodik:. Szükség esetén felülbírálhatja az alapértelmezett fuzzy-szinteket. 

* A `idxSet` paraméterrel rangsorolhatja az eredményhalmaz pontos készletét. Az eredmények pontos halmazának rangsorolása érdekében elküldheti az indexek vesszővel tagolt listáját. A listában az elem sorrendje nem számít. A Azure Maps a következő indexeket támogatja:

* `Addr` - **Címtartományok: az** utca elejéről és végéről központilag interpolált címek. Ezek a pontok címtartományokként jelennek meg.
* `Geo` - **Földrajzi** területek: a földek adminisztratív részlege. A földrajzi hely lehet például ország/régió, állam vagy város.
* `PAD` - **Pont címei**: az utca nevét és számát tartalmazó címek. A pontok címei a tárgymutatóban találhatók. Példa: *Soquel Dr 2501*. A pont címe a legmagasabb szintű pontosságot biztosítja a címek számára.  
* `POI` - Érdekes **pontok**: a térképen olyan pontok láthatók, amelyek hasznosnak számítanak, vagy érdekesek lehetnek. A [keresési címek API](/rest/api/maps/search/getsearchaddress) nem ad vissza POI-ket.  
* `Str` - **Utcák**: utcák a térképen.
* `XStr` - **Több utca vagy** kereszteződések: olyan csomópontok vagy helyek, ahol két utca metszi egymást.


#### <a name="usage-examples"></a>Használati példák

* `idxSet=POI` – Csak keresési POI-k. 

* `idxSet=PAD,Addr` – Csak keresési címek. `PAD` Megadja a pont címeit, és `Addr` jelzi a címtartományt.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Geocode és szűrés egy földrajzi entitás típusához

Ha fordított geocode keresést végez a [keresési fordított API](/rest/api/maps/search/getsearchaddressreverse)-ban, a szolgáltatás a felügyeleti területekhez tartozó sokszögeket adhat vissza. Előfordulhat például, hogy le szeretné kérni egy város területének sokszögét. Ha a keresést egy adott földrajzi egység típusára szeretné szűkíteni, adja `entityType` meg a paramétert a kérésekben. 

Az eredményül kapott válasz tartalmazza a földrajzi azonosítót és az egyező entitás típusát. Ha egynél több entitást ad meg, a végpont az *elérhető legkisebb entitást* adja vissza. A visszaadott geometriai azonosító használatával lekérheti a földrajz geometriáját a [keresési sokszög szolgáltatáson](/rest/api/maps/search/getsearchpolygon)keresztül.

#### <a name="sample-request"></a>Példa a kérelemre

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Reagálás

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>Az eredmények nyelvének beállítása

Használja a `language` paramétert a visszaadott keresési eredmények nyelvének megadásához. Ha a kérés nem állítja be a nyelvet, akkor alapértelmezés szerint a Search Service az ország vagy régió leggyakoribb nyelvét használja. Ha a megadott nyelven nem érhető el adatérték, a rendszer az alapértelmezett nyelvet használja. 

További információ: [Azure Maps támogatott nyelvek](./supported-languages.md).


### <a name="use-predictive-mode-automatic-suggestions"></a>Prediktív mód használata (automatikus javaslatok)

Ha további egyezéseket szeretne találni a részleges lekérdezésekhez, állítsa a paramétert a következőre: `typeahead` `true` . A lekérdezés részleges bemenetként van értelmezve, és a keresés prediktív módba kerül. Ha nem állítja be a `typeahead` paramétert `true` , akkor a szolgáltatás feltételezi, hogy az összes releváns információ át lett adva a alkalmazásban.

A következő példában a *m* lekérdezése a keresési címtartomány lekérésére szolgál. Itt a `typeahead` paraméter értékre van `true` állítva. A válasz azt mutatja, hogy a keresési szolgáltatás részleges lekérdezésként értelmezte a lekérdezést. A válasz egy automatikusan javasolt lekérdezés eredményét tartalmazza.

#### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Reagálás

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>URI kódolása speciális karakterek kezeléséhez 

A Cross Street-címek kereséséhez kódolnia kell az URI-t a címben szereplő speciális karakterek kezelésére. Vegyük például a következő példát: *1st Avenue & Union Street, Seattle*. Itt kódolja a jel karaktert ( `&` ) a kérelem elküldése előtt. 

Azt javasoljuk, hogy kódolja a karaktereket egy URI-ban. Az URI-ban az összes karaktert egy százalékos előjel ( `%` ) és egy kétkarakteres hexadecimális érték használatával kódolja, amely megfelel az "UTF-8-kód" karakternek.

#### <a name="usage-examples"></a>Használati példák

Kezdje ezzel a címnek:

```
query=1st Avenue & E 111th St, New York
```

Kódolás a címben:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

A következő módszerekkel végezhető el.

JavaScript vagy írógéppel:
```javascript
encodeURIComponent(query)
```

C# vagy Visual Basic:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Lépjen
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Ajánlott eljárások a POI-kereséshez

A POI-keresésekben a POI-találatokat név szerint kérheti le. Megkeresheti például a vállalat nevét. 

Javasoljuk, hogy a `countrySet` paraméter használatával adja meg azokat az országokat/régiókat, ahol az alkalmazásnak lefedettségre van szüksége. Az alapértelmezett viselkedés a teljes világon való keresés. Ez a széles körű keresés felesleges eredményeket adhat vissza, és a keresés hosszú időt is igénybe vehet.

### <a name="brand-search"></a>Márka keresése

Az eredmények és a válaszban szereplő információk relevanciájának javítása érdekében a POI keresési válasza tartalmazza a márka információit. Ezeket az információkat tovább használhatja a válasz elemzéséhez.

Egy kérelemben elküldheti a márkanevek vesszővel tagolt listáját. A listával szűkítheti az eredményeket adott márkákra a paraméter beállításával `brandSet` . A listában az elemek sorrendje nem számít. Több márka-listát is megadhat, a visszaadott eredményeknek legalább az egyik listához kell tartozniuk.

A márka keresésének megismeréséhez hozzon igénybe egy [POI kategória keresési](/rest/api/maps/search/getsearchpoicategory) kérelmét. A következő példában a Microsoft Campus közelében található benzinkutak a Washington állambeli Redmondban. A válasz a visszaadott POI-ra vonatkozó összes adatot megjeleníti.

#### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Reagálás

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Repülőtér keresése

A Search POI API használatával a reptereket a hivatalos kódjával keresheti meg. Például a *Sea* használatával megkeresheti a Seattle-Tacoma nemzetközi repülőteret: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Közeli keresés

Ha egy adott helyre vonatkozó POI-eredményeket szeretne beolvasni, próbálja meg használni a [közeli keresés API](/rest/api/maps/search/getsearchnearby)-t. A végpont csak POI-eredményeket ad vissza. Nem egy keresési lekérdezési paramétert használja. 

Az eredmények korlátozásához azt javasoljuk, hogy állítsa be a RADIUS-t.

## <a name="understanding-the-responses"></a>A válaszok ismertetése

Keresse meg a Seattle-beli címeket, és keressen rá egy keresési kérést a Azure Maps Search Service. A következő kérelem URL-címében beállítjuk a `countrySet` paramétert az `US` USA-beli cím kereséséhez.

### <a name="sample-query"></a>Mintalekérdezés

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Támogatott típusú eredmények

* **Pont címe**: a térképen olyan pontok jelennek meg, amelyek egy utcanév és egy számmal megadott névvel rendelkeznek. A pont címe a legmagasabb szintű pontosságot biztosítja a címeknél. 

* **Címtartomány**: az utca elejéről és végéről központilag interpolált címtartomány.  

* **Földrajzi** hely: egy Térkép azon területei, amelyek egy adott földterület (például ország/régió, állam vagy város) felügyeleti részlegeit jelölik. 

* **POI**: egy térképen olyan pontok mutatnak, amelyek hasznosak, és érdekesek lehetnek.

* **Utca**: utcák a térképen. A címek a címet tartalmazó utca szélességi és hosszúsági koordinátáihoz vannak feloldva. Lehet, hogy a házszám nem dolgozható fel. 

* **Cross Street**: metszetek. A Cross Streets olyan csomópontokat jelöl, ahol két utca metszi egymást.

### <a name="response"></a>Reagálás

Tekintsük át a válasz struktúráját. Az alábbi válaszban az eredményül kapott objektumok típusai eltérőek. Ha alaposan meggondolja, három típusú eredmény-objektum jelenik meg:

* Pont címe
* Utca
* Cross Street

Figyelje meg, hogy a címek keresése nem ad vissza POI-ket.  

Az `Score` egyes Response objektumokhoz tartozó paraméter azt jelzi, hogy a megfelelő pontszám hogyan kapcsolódik az azonos válaszban lévő többi objektum pontszámához. A válasz objektum paramétereinek részletes ismertetését lásd: [keresési címek beolvasása](/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

A *geometriai* válasz típusa magában foglalhatja az objektumban a és a alatt visszaadott geometriai azonosítót is `dataSources` `geometry` `id` . A [keresési sokszög szolgáltatással](/rest/api/maps/search/getsearchpolygon) például GeoJSON formátumban kérheti le a geometriai adatokra. Ennek a formátumnak a használatával lekérheti a város vagy a repülőtér körvonalát az entitások egy halmazához. Ezt a határvonalat használhatja a [Geokerítésen beállításához](./tutorial-geofence.md) , vagy [a geometrián belüli kereséshez](/rest/api/maps/search/postsearchinsidegeometry).


A Search ( [Keresés](/rest/api/maps/search/getsearchaddress) ) API-hoz vagy a [Search fuzzy](/rest/api/maps/search/getsearchfuzzy) API-hoz tartozó válaszok közé tartozhat az objektumban a és a között visszaadott geometriai azonosító `dataSources` `geometry` `id` .


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Következő lépések

További információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Azure Maps Search Service kérelmek összeállítása](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Search Service API-dokumentáció](/rest/api/maps/search)