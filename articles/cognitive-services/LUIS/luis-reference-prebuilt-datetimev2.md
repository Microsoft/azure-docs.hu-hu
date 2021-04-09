---
title: DatetimeV2-előkészített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) datetimeV2 előre elkészített entitási információit tartalmazta.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96013071"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 előre összeépített entitása egy LUIS-alkalmazáshoz

A **datetimeV2** előre elkészített entitása Kinyeri a dátum-és időértékeket. Ezek az értékek az ügyfélalkalmazások által használt szabványosított formátumban oldhatók fel. Ha egy Kimondás olyan dátummal vagy időponttal rendelkezik, amely nem fejeződött be, LUIS a végponti válaszban a _múltbeli és a jövőbeli értékeket_ is tartalmazza. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a datetimeV2-t tartalmazó hosszúságú kimondott szöveg az Application szándékokhoz.

## <a name="types-of-datetimev2"></a>DatetimeV2 típusai
A DatetimeV2 kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) tárházból történik.

## <a name="example-json"></a>Példa JSON-ra

A következő kifejezés és a részleges JSON-válasza alább látható.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 válasz](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Tulajdonság neve |Tulajdonság típusa és leírása|
|---|---|
|Entitás|**karakterlánc** – a kinyert szöveg a dátum, az idő, a dátumtartomány vagy az időtartomány típusával.|
|típus|**String** – a datetimeV2 egyik [altípusa](#subtypes-of-datetimev2)
|startIndex|**int** – az entitás megkezdésének teljes alapjául szolgáló index.|
|endIndex|**int** – az az index, amelyben az entitás véget ér.|
|felbontás|Olyan `values` tömbvel rendelkezik, amely egy, kettő vagy négy [feloldási értékkel](#values-of-resolution)rendelkezik.|
|befejezés|Egy időpont vagy dátumtartomány záró értéke a formátummal megegyező formátumban `value` . Csak akkor használható, ha `type` a, `daterange` `timerange` , vagy `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>DatetimeV2 altípusai

A **datetimeV2** előre összeállított entitás a következő altípusokkal rendelkezik, és az egyes példák az alábbi táblázatban találhatók:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>A feloldási értékek
* A tömbnek egy eleme van, ha a Kimondás dátuma vagy időpontja teljesen meg van adva, és nem egyértelmű.
* A tömb két elemet tartalmaz, ha a datetimeV2 értéke nem egyértelmű. A kétértelműség nem tartalmaz adott évet, időt vagy időtartományt. Példák a [kétértelmű dátumok](#ambiguous-dates) megtekintésére. Ha az idő nem egyértelmű vagy P.M., mindkét értéket tartalmazza.
* A tömbnek négy eleme van, ha a Kimondás két, kétértelmű elemet tartalmaz. Ez a kétértelmű elem a következőkkel rendelkezik:
  * Dátum vagy dátumtartomány, amely nem egyértelmű az év értékében
  * Olyan idő-vagy időintervallum, amely nem egyértelmű vagy délután. Például 3:00 április 3.

A tömb minden eleme `values` a következő mezőkkel rendelkezhet:

|Tulajdonság neve|Tulajdonság leírása|
|--|--|
|Timex|az idő, a dátum vagy a dátumtartomány TIMEX formátumban kifejezve, amely az [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601) követi, valamint a jegyzet TIMEX3 attribútumait a TimeML nyelv használatával.|
|mod|az érték (például:) használatának leírására szolgáló kifejezés `before` `after` .|
|típus|Az altípus, amely a következő elemek egyike lehet:,,, `datetime` `date` ,, `time` `daterange` `timerange` `datetimerange` , `duration` , `set` .|
|érték|**Választható.** Egy datetime objektum a következő formátumban: éééé-hh-dd (Date), óó: PP: mm (Time) éééé-hh-nn óó: PP: mm (datetime). Ha a értéke `type` `duration` , akkor az érték a másodpercek száma (időtartam) <br/> Csak akkor használható, ha `type` az a, a, `datetime` vagy a `date` `time` időtartam.|

## <a name="valid-date-values"></a>Érvényes dátumérték

A **datetimeV2** a következő tartományok közötti dátumokat támogatja:

| Min | Max |
|----------|-------------|
| Január 1., 1900   | 2099. december 31-ig |

## <a name="ambiguous-dates"></a>Nem egyértelmű dátumok

Ha a dátum a múltban vagy a jövőben is lehetséges, a LUIS mindkét értéket megadja. Ilyen például egy olyan Kimondás, amely az év nélküli hónapot és dátumot is tartalmazza.

Például a következő Kimondás miatt:

`May 2nd`

* Ha a mai dátum május 3rd 2017, a LUIS értékként "2017-05-02" és "2018-05-02" értéket is biztosít.
* Ha a mai dátum május 1-től 2017, a LUIS értékként "2016-05-02" és "2017-05-02" értéket is biztosít.

Az alábbi példa a "május 2nd" entitás feloldását mutatja be. Ez a megoldás azt feltételezi, hogy a mai dátum az 2017. május 2. és május 2018 1. között megjelenő dátum.
A mezővel rendelkező mezők azon `X` `timex` dátum részei, amely nem explicit módon van meghatározva a teljes szövegben.

## <a name="date-resolution-example"></a>Példa a felbontásra


A következő kifejezés és a részleges JSON-válasza alább látható.

`May 2nd`

#### <a name="v3-response"></a>[V3 válasz](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Dátumtartomány-feloldási példák a numerikus dátumhoz

Az `datetimeV2` entitás kibontja a dátum és idő tartományokat. A `start` és a `end` mezők a tartomány elejét és végét határozzák meg. A teljes támogatáshoz a `May 2nd to May 5th` Luis az aktuális évhez és a következő évhez is biztosít **daterange** értékeket. A `timex` mezőben az `XXXX` értékek jelzik az év kétértelmű értékét. `P3D` azt jelzi, hogy az időtartam három nap.

A következő kifejezés és a részleges JSON-válasza alább látható.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 válasz](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Dátumtartomány-feloldási példák a hét napja

Az alábbi példa azt szemlélteti, hogyan használja a LUIS a **datetimeV2** -t a Kimondás feloldására `Tuesday to Thursday` . Ebben a példában az aktuális dátum június 19. A LUIS mindkét **daterange** tartalmazza, amelyek megelőzik és követik az aktuális dátumot.

A következő kifejezés és a részleges JSON-válasza alább látható.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 válasz](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Nem egyértelmű idő
Az értékek tömbje két időponttal rendelkezik, ha az idő vagy az időtartomány nem egyértelmű. Ha nem egyértelmű idő van, az értékek mind az am és délután alkalommal.

## <a name="time-range-resolution-example"></a>Időtartomány-feloldási példa

DatetimeV2 JSON-válasz módosult az API v3-ben. Az alábbi példa azt szemlélteti, hogyan használja a LUIS a **datetimeV2** -t az időtartományhoz tartozó teljes érték feloldásához.

Változások az API v2-ből:
* `datetimeV2.timex.type` a tulajdonságot a rendszer már nem adja vissza, mert a szülő szinten adja vissza `datetimev2.type` .
* A `datetimeV2.value` tulajdonság át lett nevezve a következőre: `datetimeV2.timex` .

A következő kifejezés és a részleges JSON-válasza alább látható.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 válasz](#tab/5-1)

A következő JSON a `verbose` paraméter értéke `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/5-2)

A következő JSON a `verbose` paraméter értéke `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Példa az időbeli felbontásra

A következő kifejezés és a részleges JSON-válasza alább látható.

`8am`

#### <a name="v3-response"></a>[V3 válasz](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Elavult előre elkészített datetime

Az `datetime` előre elkészített entitás elavult, és a **datetimeV2** váltja fel.

Ha a `datetime` -t a `datetimeV2` Luis-alkalmazásban szeretné cserélni, hajtsa végre a következő lépéseket:

1. Nyissa meg a LUIS webes felület **entitások** paneljét.
2. Törölje a **datetime** előre elkészített entitást.
3. Kattintson az **előre összeépített entitás hozzáadása** lehetőségre.
4. Válassza a **datetimeV2** elemet, majd kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [dimenziót](luis-reference-prebuilt-dimension.md), az [e-mail](luis-reference-prebuilt-email.md) -entitásokat és a [számokat](luis-reference-prebuilt-number.md).

