---
title: PersonName előre elkészített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) personName előre felépített entitási információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535423"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName előre összeépített entitása egy LUIS-alkalmazáshoz
Az előre elkészített personName entitás észleli a személyek nevét. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például a personName-t tartalmazó hosszúságú kimondott szöveg az Application szándékokhoz. az personName entitás angol és kínai [kulturális](luis-reference-prebuilt-entities.md)környezetekben támogatott.

## <a name="resolution-for-personname-entity"></a>PersonName-entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 válasz](#tab/V3)


A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

Az alábbi példa a **beépített. personName** entitás feloldását mutatja be.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ az [e-mailek](luis-reference-prebuilt-email.md), a [számok](luis-reference-prebuilt-number.md)és a [sorszámok](luis-reference-prebuilt-ordinal.md) entitásokról.
