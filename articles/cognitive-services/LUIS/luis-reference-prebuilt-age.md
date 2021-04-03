---
title: Kora előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előre elkészített entitások adatait tartalmazza Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: 4e1a6b1b6fb99a2786de06e89960e8480a5e1338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91532924"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás kora előre összeépített entitása
Az előre elkészített életkorú entitás számszerűen és napokon, heteken, hónapokban és években is rögzíti a kor értékét. Mivel ez az entitás már be van tanítva, nem kell felvennie az hosszúságú kimondott szöveg tartalmazó példát. Az Age entitást [számos kulturális](luis-reference-prebuilt-entities.md)környezet támogatja.

## <a name="types-of-age"></a>Típusú életkor
A kor kezelése a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) adattárból történik

## <a name="resolution-for-prebuilt-age-entity"></a>Előre elkészített életkorú entitás feloldása



#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **beépített. Age** entitás feloldását mutatja be.

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Tudnivalók a [pénznemről](luis-reference-prebuilt-currency.md), a [datetimeV2](luis-reference-prebuilt-datetimev2.md)és a [dimenzió](luis-reference-prebuilt-dimension.md) entitásokról.
