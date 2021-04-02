---
title: Pénznem előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk Language Understanding (LUIS) pénznemben előre felépített entitásokra vonatkozó információkat tartalmaz.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534336"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS-alkalmazás pénznemének előre felépített entitása
Az előre felépített pénznem entitások számos címletben és országban/régióban észlelik a pénznemet, függetlenül a LUIS-alkalmazás kulturális környezettől. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a pénznemet tartalmazó hosszúságú kimondott szöveg az Application-leképezésekhez. A pénznem entitások [számos kultúrában](luis-reference-prebuilt-entities.md)támogatottak.

## <a name="types-of-currency"></a>A pénznem típusai
A pénznem kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) adattárból történik.

## <a name="resolution-for-currency-entity"></a>Pénznem entitás feloldása

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **beépített. pénznem** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [dimenziót](luis-reference-prebuilt-dimension.md)és az [e-mail](luis-reference-prebuilt-email.md) entitásokat.
