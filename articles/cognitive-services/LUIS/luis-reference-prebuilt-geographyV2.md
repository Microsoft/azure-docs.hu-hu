---
title: Földrajz v2 előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) geographyV2 előre felépített entitási információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: ad6fd62178ec8e987e1e3b6e05cc961bbe15e377
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541959"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>GeographyV2 előre összeépített entitása egy LUIS-alkalmazáshoz
Az előre elkészített geographyV2 entitás észleli a helyeket. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a GeographyV2-t tartalmazó hosszúságú kimondott szöveg az Application szándékokhoz. Az GeographyV2 entitás angol [kulturális](luis-reference-prebuilt-entities.md)környezetekben támogatott.

## <a name="subtypes"></a>Altípusok
A földrajzi helyszínek altípusokkal rendelkeznek:

|Altípus|Cél|
|--|--|
|`poi`|érdekes pont|
|`city`|város neve|
|`countryRegion`|ország vagy régió neve|
|`continent`|a kontinens neve|
|`state`|állam vagy megye neve|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2-entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`Carol is visiting the sphinx in gizah egypt in africa before heading to texas.`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ]
}
```

Az előző JSON-ben az `poi` **érdekes pont** rövidítése.

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ],
    "$instance": {
        "geographyV2": [
            {
                "type": "builtin.geographyV2.poi",
                "text": "the sphinx",
                "startIndex": 18,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.city",
                "text": "gizah",
                "startIndex": 32,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.countryRegion",
                "text": "egypt",
                "startIndex": 38,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.continent",
                "text": "africa",
                "startIndex": 47,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.state",
                "text": "texas",
                "startIndex": 72,
                "length": 5,
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

Az alábbi példa a **beépített. geographyV2** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "the sphinx",
        "type": "builtin.geographyV2.poi",
        "startIndex": 18,
        "endIndex": 27
    },
    {
        "entity": "gizah",
        "type": "builtin.geographyV2.city",
        "startIndex": 32,
        "endIndex": 36
    },
    {
        "entity": "egypt",
        "type": "builtin.geographyV2.countryRegion",
        "startIndex": 38,
        "endIndex": 42
    },
    {
        "entity": "africa",
        "type": "builtin.geographyV2.continent",
        "startIndex": 47,
        "endIndex": 52
    },
    {
        "entity": "texas",
        "type": "builtin.geographyV2.state",
        "startIndex": 72,
        "endIndex": 76
    },
    {
        "entity": "carol",
        "type": "builtin.personName",
        "startIndex": 0,
        "endIndex": 4
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ az [e-mailek](luis-reference-prebuilt-email.md), a [számok](luis-reference-prebuilt-number.md)és a [sorszámok](luis-reference-prebuilt-ordinal.md) entitásokról.
