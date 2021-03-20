---
title: LUIS előre elkészített entitások e-mail-referenciája
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) e-mailes előkészített entitás-információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 806357670bead54658f0b501ca20473293275d58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91533366"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail-előkészített entitás egy LUIS-alkalmazáshoz
Az e-mailek kinyerése magában foglalja a teljes e-mail-címet. Mivel ez az entitás már be van tanítva, nem kell felvennie például az hosszúságú kimondott szöveg tartalmazó e-mailt. Az e-mail-entitás csak a kulturális környezetekben támogatott `en-us` .

## <a name="resolution-for-prebuilt-email"></a>Előre elkészített e-mailek feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

A következő példa a **Builtin.email** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [szám](luis-reference-prebuilt-number.md), a [sorszám](luis-reference-prebuilt-ordinal.md)és a [százalék](luis-reference-prebuilt-percentage.md)megismerése.
