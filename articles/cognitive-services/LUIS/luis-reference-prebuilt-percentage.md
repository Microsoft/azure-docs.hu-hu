---
title: Előre elkészített entitás százalékos aránya – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előre elkészített entitások százalékos arányát tartalmazza Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541696"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitások százalékos aránya
A százalékos számok töredékként, `3 1/2` vagy százalékként is megjelenhetnek `2%` . Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a százalékos értéket tartalmazó hosszúságú kimondott szöveg az alkalmazás-leképezésekhez. A százalékos entitások [számos kultúrában](luis-reference-prebuilt-entities.md)támogatottak.

## <a name="types-of-percentage"></a>A százalékos típusok
A százalékos arányt a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) adattár kezeli

## <a name="resolution-for-prebuilt-percentage-entity"></a>Előre összeépített százalékos entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

Az alábbi példa a **beépített. százalék** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ a [sorszámok](luis-reference-prebuilt-ordinal.md), a [számok](luis-reference-prebuilt-number.md)és a [hőmérsékleti](luis-reference-prebuilt-temperature.md) entitásokról.
