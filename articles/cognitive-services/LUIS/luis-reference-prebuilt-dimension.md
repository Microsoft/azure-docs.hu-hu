---
title: Dimenzió előre elkészített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk Language Understanding (LUIS) dimenzió előre felépített entitási információit tartalmazza.
services: cognitive-services
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 0b5f081dc8c0bc203ccafde4a513371d11e36879
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91538347"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>A dimenzió előre elkészített entitása egy LUIS-alkalmazáshoz
Az előre elkészített dimenzió entitás különféle típusú dimenziókat észlel, függetlenül a LUIS alkalmazás kulturális környezettől. Mivel ez az entitás már be van tanítva, nem kell felvennie például a dimenziókat tartalmazó hosszúságú kimondott szöveg az alkalmazás-leképezésekhez. A dimenzió entitások [számos kultúrában](luis-reference-prebuilt-entities.md)támogatottak.

## <a name="types-of-dimension"></a>Dimenzió típusai

A dimenzió kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) tárházból történik.

## <a name="resolution-for-dimension-entity"></a>Dimenzió entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

A következő példa a **beépített. Dimension** entitás feloldását mutatja be.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ az [e-mailek](luis-reference-prebuilt-email.md), a [számok](luis-reference-prebuilt-number.md)és a [sorszámok](luis-reference-prebuilt-ordinal.md) entitásokról.
