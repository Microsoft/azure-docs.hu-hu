---
title: A valós idejű és az előre jelzett időjárási idő kérése Azure Maps időjárási szolgáltatásokkal (előzetes verzió)
description: Megtudhatja, hogyan kérhet valós idejű (aktuális) és előre jelzett (percenkénti, óránkénti, napi) időjárási információkat Microsoft Azure Maps időjárási szolgáltatások (előzetes verzió) használatával
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680424"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>A valós idejű és az előre jelzett időjárási idő kérése Azure Maps időjárási szolgáltatásokkal (előzetes verzió) 

> [!IMPORTANT]
> Azure Maps időjárási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps az [időjárási szolgáltatások](/rest/api/maps/weather) olyan REST API-k, amelyek lehetővé teszik a fejlesztők számára, hogy a nagy dinamikus történelmi, valós idejű és előre jelzett időjárási adatok és vizualizációk integrálását a megoldásba. Ebben a cikkben bemutatjuk, hogyan kérheti le a valós idejű és az előre jelzett időjárási adatgyűjtést.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* Valós idejű (aktuális) időjárási idő kérése a [jelenlegi feltételek beolvasása API](/rest/api/maps/weather/getcurrentconditionspreview)használatával.
* Súlyos időjárási riasztások kérése a [Get súlyos időjárási riasztások API](/rest/api/maps/weather/getsevereweatheralertspreview)használatával.
* Napi előrejelzéseket kérhet a [napi előrejelzési API beszerzése](/rest/api/maps/weather/getdailyforecastpreview)paranccsal.
* Óránkénti előrejelzést kérhet a [Get óránkénti előrejelzési API](/rest/api/maps/weather/gethourlyforecastpreview)használatával.
* Kérelmek percenkénti előrejelzése a [Get minute előrejelzési API](/rest/api/maps/weather/getminuteforecastpreview)használatával.

Ez a videó példákat tartalmaz a Azure Maps időjárási szolgáltatásokhoz való REST-hívások kezdeményezésére.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >A [Get minute előrejelzés API](/rest/api/maps/weather/getminuteforecastpreview) -nak S1 díjszabási szintű kulcsra van szüksége. Minden egyéb API-nak szüksége van egy S0-díjszabási szintű kulcsra.

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="request-real-time-weather-data"></a>Valós idejű időjárási adatgyűjtés kérése

A [jelenlegi feltételek beolvasása API](/rest/api/maps/weather/getcurrentconditionspreview) részletes időjárási feltételeket ad vissza, például a csapadék, a hőmérséklet és a szél egy adott koordináta-helyhez. Az elmúlt 6 vagy 24 óra megjegyzéseit is lekérheti az adott helyre vonatkozóan. A válasz olyan részleteket tartalmaz, mint a megfigyelési dátum és idő, az időjárási feltételek rövid leírása, az időjárási ikon, a csapadék jelzője és a hőmérséklet. A rendszer a RealFeel™ hőmérsékletét és az ultraibolya (UV) indexet is visszaadja.

Ebben a példában a [jelenlegi feltételek beolvasása API](/rest/api/maps/weather/getcurrentconditionspreview) segítségével lekéri az aktuális időjárási feltételeket a Seattle-ben található Koordinátákban.

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot. Ezt a gyűjteményt a jelen dokumentum többi példájában is használhatja.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Kattintson a kék **Küldés** gombra. A válasz törzse aktuális időjárási adatokat tartalmaz.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Súlyos időjárási riasztások kérése

[Azure Maps a súlyos időjárási riasztások API](/rest/api/maps/weather/getsevereweatheralertspreview) beolvassa azokat a súlyos időjárási riasztásokat, amelyek világszerte elérhetők a hivatalos állami meteorológiai ügynökségektől, és globálisan a regionális időjárási riasztási szolgáltatók számára. A szolgáltatás adatokat adhat vissza, például a riasztás típusát, a kategóriát, a szintet és részletes leírást a kért helyre vonatkozó aktív súlyos riasztásokról, például a hurrikánok, a zivatarok, a villámlások, a hőhullámok vagy az erdőtüzek. A logisztikai vezetők például a térképeken, az üzleti helyszíneken és a tervezett útvonalakon is megjeleníthetők a súlyos időjárási feltételek, valamint az illesztőprogramok és a helyi feldolgozók további koordinálása.

Ebben a példában a [Get súlyos időjárási riasztások API](/rest/api/maps/weather/getsevereweatheralertspreview) -t használja a jelenlegi időjárási feltételek lekéréséhez a CHEYENNE, WY helyen található koordinátákon.

>[!NOTE]
>Ez a példa súlyos időjárási riasztásokat kér le az írás időpontjában. Valószínű, hogy a kért helyen már nincsenek súlyos időjárási riasztások. A tényleges súlyos riasztási adatok lekéréséhez a példa futtatásakor egy másik koordináta-helyen kell adatokat lekérnie.

1. Nyissa meg a Poster alkalmazást, kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kattintson a kék **Küldés** gombra. Ha nincsenek súlyos időjárási riasztások, a válasz törzse üres tömböt fog tartalmazni `results[]` . Súlyos időjárási riasztások esetén a válasz törzse a következő JSON-válaszhoz hasonlóan tartalmaz:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Napi időjárási előrejelzési adatszolgáltatások kérése

A [napi előrejelzés beolvasása API](/rest/api/maps/weather/getdailyforecastpreview) részletes napi időjárási előrejelzést ad vissza, például a hőmérsékletet és a széleket. A kérelemben megadható, hogy hány nap elteltével kell visszaadni: 1, 5, 10, 15, 25 vagy 45 nap egy adott koordináta-helyhez. A válasz olyan részleteket tartalmaz, mint például a hőmérséklet, a szél, a csapadék, a levegőminőség és az UV-index.  Ebben a példában a beállítással öt napig várjuk a kérést `duration=5` .

>[!IMPORTANT]
>A S0 díjszabási szintjein napi előrejelzést kérhet a következő 1, 5, 10 és 15 nap után. Az S1 díjszabási szinten napi előrejelzést is kérhet a következő 25 napra és 45 napra vonatkozóan.

Ebben a példában a [napi előrejelzési API](/rest/api/maps/weather/getdailyforecastpreview) beolvasása lehetőségre kattintva lekéri az öt napos időjárás-előrejelzést a Seattle-ben található koordinátákhoz.

1. Nyissa meg a Poster alkalmazást, kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kattintson a kék **Küldés** gombra. A válasz törzse az öt napos időjárás-előrejelzési adatát tartalmazza. A rövidség kedvéért az alábbi JSON-válasz az első nap előrejelzését jeleníti meg.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Óradíjas előrejelzési idő kérése

A [Get óránkénti előrejelzés API](/rest/api/maps/weather/gethourlyforecastpreview) részletes időjárás-előrejelzést ad vissza az óra alapján a következő 1, 12, 24 (1 nap), 72 (3 nap), 120 (5 nap) és 240 óra (10 nap) esetében a megadott koordináta-helyhez. Az API olyan részleteket ad vissza, mint például a hőmérséklet, a páratartalom, a szél, a csapadék és az UV-index.

>[!IMPORTANT]
>A S0 díjszabási szinten a következő 1, 12, 24 óra (1 nap) és 72 óra (3 nap) óradíjat is igényelhet. Az S1 árképzési szinten a következő 120 (5 nap) és a 240 óra (10 nap) óradíjat is igénybe veheti.

Ebben a példában a [Get óránkénti előrejelzés API](/rest/api/maps/weather/gethourlyforecastpreview) -val lekéri a következő 12 órában az óránkénti időjárás-előrejelzést a Seattle-ben található Koordinátákban.

1. Nyissa meg a Poster alkalmazást, kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kattintson a kék **Küldés** gombra. A válasz törzse a következő 12 órában időjárás-előrejelzési adatértéket tartalmaz. A rövidség kedvéért az alábbi JSON-válasz az első órában az előrejelzést mutatja.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Kérelem percenkénti időjárás-előrejelzési adatként

 A [Get minute előrejelzési API](/rest/api/maps/weather/getminuteforecastpreview) percenkénti előrejelzéseket ad vissza egy adott helyhez a következő 120 percre. A felhasználók az időjárási előrejelzéseket 1, 5 és 15 perc intervallumban kérhetik. A válasz olyan részleteket tartalmaz, mint például a csapadék típusa (beleértve az esőt, a havat vagy a kettő kombinációját), a kezdési időt és a csapadék intenzitásának értékét (dBZ).

Ebben a példában a [Get minute előrejelzés API](/rest/api/maps/weather/getminuteforecastpreview) -val beolvassa a Seattle-ben található koordinátákat percek alatt a percenkénti időjárás-előrejelzést. Az időjárás-előrejelzés a következő 120 percre van megadva. A lekérdezés azt kéri, hogy az előrejelzést 15 percenként adja meg, de beállíthatja, hogy a paraméter értéke 1 vagy 5 perc legyen.

1. Nyissa meg a Poster alkalmazást, kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Kattintson a kék **Küldés** gombra. A válasz törzse a következő 120 perc időjárás-előrejelzési értékeit 15 perces időközökben tartalmazza.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Maps időjárási szolgáltatások (előzetes verzió) – fogalmak](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps időjárási szolgáltatások (előzetes verzió) REST API](/rest/api/maps/weather)