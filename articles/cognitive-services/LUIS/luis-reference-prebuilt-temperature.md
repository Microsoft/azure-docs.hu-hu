---
title: Hőmérséklet-előkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk Language Understanding (LUIS) hőmérséklettel előre felépített entitási információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 46161a83d261ae23ca45b7293e48ff15e435f42d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535338"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>A hőmérséklet előre elkészített entitása egy LUIS-alkalmazáshoz
A hőmérséklet különböző hőmérséklet-típusokat gyűjt. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia az alkalmazáshoz hőmérsékletet tartalmazó hosszúságú kimondott szöveg. A hőmérsékleti entitások [számos kultúrában](luis-reference-prebuilt-entities.md)támogatottak.

## <a name="types-of-temperature"></a>Hőmérsékleti típusok
A hőmérséklet a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) adattárból kezelhető

## <a name="resolution-for-prebuilt-temperature-entity"></a>Előre elkészített hőmérsékleti entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

Az alábbi példa a **beépített. hőmérséklet** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [százalékos](luis-reference-prebuilt-percentage.md), a [szám](luis-reference-prebuilt-number.md)-és az [életkor](luis-reference-prebuilt-age.md) entitásokat.
