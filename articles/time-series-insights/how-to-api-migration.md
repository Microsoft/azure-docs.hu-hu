---
title: Áttelepítés új Azure Time Series Insights Gen2 API-verzióra | Microsoft Docs
description: Azure Time Series Insights Gen2-környezetek frissítése új általánosan elérhető verziók használatára.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97740555"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Áttelepítés új Azure Time Series Insights Gen2 API-verzióra

## <a name="overview"></a>Áttekintés

Ha a nyilvános előzetes verzióban (2020. július 16. előtt) létrehozott egy Azure Time Series Insights Gen2-környezetet, frissítse az ÁME-környezetet, hogy az új, általánosan elérhető API-kat használja a jelen cikkben ismertetett lépéseket követve. Ez a változás nem érinti azokat a felhasználókat, akik a Azure Time Series Insights Gen1 verzióját használják.

> [!IMPORTANT]
> A jelen cikkben ismertetett frissítések csak az ÁME-környezet által használt API-verziókat frissítik. Ez a változás nem kapcsolódik a Gen2-környezetekben bevezetett új [JSON-összeolvasztási és Escape-szabályokhoz](./concepts-json-flattening-escaping-rules.md) .

Az új API-verzió a `2020-07-31` és a frissített [idősorozat-kifejezés szintaxisát](/rest/api/time-series-insights/reference-time-series-expression-syntax)használja.

A felhasználóknak át kell telepíteniük a környezetük [idősorozat-modellezési változóit](./concepts-variables.md), a mentett lekérdezéseket, Power bi lekérdezéseket és az API-végpontokat meghívó egyéni eszközöket. Ha bármilyen kérdése vagy problémája van az áttelepítési folyamattal kapcsolatban, küldjön egy támogatási jegyet a Azure Portal, és nevezze el ezt a dokumentumot.

> [!IMPORTANT]
> Az előzetes verziójú API `2018-11-01-preview` -verzió az 2020-as október 31-ig továbbra is támogatott lesz. A szolgáltatásban felmerülő fennakadások elkerülése érdekében végezze el az áttelepítés összes vonatkozó lépését.

## <a name="migrate-time-series-model-and-saved-queries"></a>Idősorozat-modell és mentett lekérdezések migrálása

Annak érdekében, hogy a felhasználók áttelepítse az [Idősorozat-modell változóit](./concepts-variables.md) és a mentett lekérdezéseket, a [Azure Time Series Insights Explorerben](https://insights.timeseries.azure.com)elérhetővé válik egy beépített eszköz. Navigáljon az áttelepíteni kívánt környezethez, és kövesse az alábbi lépéseket. **Az áttelepítést részben elvégezheti, és később is visszatérhet a befejezéshez, de a frissítések egyike sem állítható vissza.**

> [!NOTE]
> A környezetnek közreműködőnek kell lennie az idősorozat-modell és a mentett lekérdezések frissítéseinek létrehozásához. Ha Ön nem közreműködő, csak a személyes mentett lekérdezéseket tudja áttelepíteni. A továbblépés előtt tekintse át a [környezeti hozzáférési házirendeket](./concepts-access-policies.md) és a hozzáférési szintet.

1. A rendszer arra kéri, hogy az Explorer frissítse az idősorozat-modell változói és a mentett lekérdezések által használt szintaxist.

    [![Adatkérés](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Ha véletlenül lezárta az értesítést, akkor az értesítési panelen található.

1. Kattintson a **frissítések megjelenítése** elemre az áttelepítési eszköz megnyitásához.

1. Kattintson a **típusok letöltése** elemre. Mivel a Migrálás felülírja a jelenlegi típusait a változó szintaxisának módosításához, meg kell mentenie a jelenlegi típusok másolatát. Az eszköz értesíti Önt, ha a rendszer letölti a típusokat.

    [![Letöltési típusok](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kattintson a **változók frissítése** elemre. Az eszköz értesíti, ha a változók frissítve lettek.

    > [!IMPORTANT]
    > Ha frissíti a típusait, a régebbi API-verziót () használó egyéni alkalmazásoknak `2018-11-01-preview` az új API-verziót () kell használniuk a `2020-07-31` működés folytatásához. Ha nem tudja biztosan, hogy melyik API-verziót használja, a frissítés előtt forduljon a rendszergazdához. Az áttelepítési eszközt lezárhatja, és később is visszatérhet ezekhez a lépésekhez. További információ az [egyéni alkalmazások áttelepítéséről](#migrate-custom-applications).

    [![Változók frissítése](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kattintson a **mentett lekérdezések frissítése** elemre. Az eszköz értesíti Önt, ha a mentett lekérdezések frissültek.

    [![Mentett lekérdezések frissítése](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Kattintson a **Kész** gombra.

    [![Áttelepítés befejezve](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Tekintse át a frissített környezetet az újonnan létrehozott változók és mentett lekérdezések ábrázolásával. Ha nem várt viselkedést lát a diagramon, küldjön visszajelzést az Explorer visszajelzési eszközének használatával.

## <a name="migrate-power-bi-queries"></a>Power BI lekérdezések migrálása

Ha a Power BI-összekötővel generált lekérdezéseket, a rendszer hívásokat kezdeményez a Azure Time Series Insights az előzetes verziójú API-verzió és a régi idősorozat-kifejezés szintaxisának használatával. Ezek a lekérdezések továbbra is sikeresen lekérik az adatlekérdezéseket, amíg az előnézeti API elavult nem lesz.

Ha frissíteni szeretné a lekérdezéseket az új API-verzió és az új idősorozat-kifejezés szintaxisának használatára, a lekérdezéseket újra létre kell hozni az Intézőből. További információ [a lekérdezések létrehozásáról az Power bi-összekötő használatával](./how-to-connect-power-bi.md).

> [!NOTE]
> Legalább a Power BI Desktop júliusi 2020-es verzióját kell használnia. Ha nem, az "érvénytelen lekérdezési tartalom verziója" hibaüzenet jelenhet meg.

## <a name="migrate-custom-applications"></a>Egyéni alkalmazások migrálása

Ha az egyéni alkalmazás a következő REST-végpontokra hívja fel a hívást, elegendő az API verziójának frissítése az `2020-07-31` URI-ban:

- Idősorozat-modell API-k
  - Modell beállítások API-k
    - [Lekérés](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Frissítés](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Példány API-k
    - [Az összes batch-művelet](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Search](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Ajánlás](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - Hierarchia API-k
    - [Az összes batch-művelet](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Típusok API-k
    - [Törlés, műveletek beolvasása](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

A következő REST-végpontok esetében frissítenie kell az API-verziót `2020-07-31` az URI-ban, és ellenőrizze, hogy a tulajdonság összes előfordulása `tsx` a frissített [Idősorozat-kifejezés szintaxisát](/rest/api/time-series-insights/reference-time-series-expression-syntax)használja-e.

- Típusok API-k
  - [Put művelet](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- Lekérdezési API-k
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Példák

#### <a name="typesbatchput"></a>TypesBatchPut

Régi kérelem törzse (által használt `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Frissített kérelem törzse (által használt `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Másik lehetőségként a is lehetséges `filter` `$event.Mode.String = 'outdoor'` . A `value` Speciális karakter () kiszökéséhez a kötelező zárójeleket kell használnia `_` .

#### <a name="getevents"></a>GetEvents

Régi kérelem törzse (által használt `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Frissített kérelem törzse (által használt `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Másik lehetőségként a is lehetséges `filter` `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>GetSeries

Régi kérelem törzse (által használt `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Frissített kérelem törzse (által használt `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Másik lehetőségként a is lehetséges `value` `$event['Bar-Pressure-Offset'].Double` . Ha nem ad meg adattípust, az adattípust a rendszer mindig dupla értékre veszi. A speciális karakter () megjelenítéséhez a zárójeles jelölést kell használni `-` .

#### <a name="aggregateseries"></a>AggregateSeries

Régi kérelem törzse (által használt `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Frissített kérelem törzse (által használt `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Másik lehetőségként a is lehetséges `value` `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Lehetséges hibák

#### <a name="invalidinput"></a>InvalidInput

Ha a következő hibaüzenet jelenik meg, akkor az új API-verziót () használja, `2020-07-31` de a TSX szintaxisa nem frissült. Tekintse át az [Idősorozat kifejezésének szintaxisát](/rest/api/time-series-insights/reference-time-series-expression-syntax) és az áttelepítési példákat. `tsx`Az API-kérelem újraküldése előtt győződjön meg arról, hogy az összes tulajdonság megfelelően frissül.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A környezetet a [Azure Time Series Insights Explorerrel](./concepts-ux-panels.md) vagy az egyéni alkalmazáson keresztül tesztelheti.