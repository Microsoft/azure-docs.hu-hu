---
title: Sorszám v2 előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a sorszámú, előre felépített entitások adatait tartalmazza Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541934"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>A 2. sorszámú, előre elkészített entitás a LUIS-alkalmazáshoz
A sorszám v2 sorszáma kibontja a [sorszámot](luis-reference-prebuilt-ordinal.md) , hogy relatív hivatkozásokat (például `next` ,, `last` és) adjon meg `previous` . Ezeket a rendszer nem a sorszámmal elkészített, előre összeépített entitás használatával bontja ki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Az előre létrehozott sorszám v2 entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
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
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

Az alábbi példa a **beépített. ordinalV2** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [százalék](luis-reference-prebuilt-percentage.md), a [telefonszám](luis-reference-prebuilt-phonenumber.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitások ismertetése.
