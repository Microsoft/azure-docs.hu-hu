---
title: Sorszámmal elkészített, előre összeépített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk Language Understanding (LUIS) sorszámmal elkészített, előre összeépített entitási információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535457"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre elkészített entitás sorszáma
A sorszám a készleten belüli objektumok numerikus ábrázolása: `first` , `second` , `third` . Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a sorszámot tartalmazó hosszúságú kimondott szöveg az Application-leképezésekhez. A besorszám entitás [számos kultúrában](luis-reference-prebuilt-entities.md)támogatott.

## <a name="types-of-ordinal"></a>Sorszámok típusai
A sorszám a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) adattárból felügyelhető

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Előre összeépített sorszámú entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`Order the second option`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

A következő példa a **beépített. sorszámú** entitás feloldását mutatja be.

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

Ismerje meg a [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), a [telefonszámot](luis-reference-prebuilt-phonenumber.md)és a [hőmérsékleti](luis-reference-prebuilt-temperature.md) entitásokat.
