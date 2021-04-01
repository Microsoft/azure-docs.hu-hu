---
title: Előre összeépített telefonszámok – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) telefonszámmal előre elkészített entitási információit tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 598ecaddbab3b70297a460521c7ec3386b390a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535389"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazáshoz tartozó előre összeépített entitás
Az `phonenumber` entitás többféle telefonszámot is Kinyer, beleértve az országkódot is. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia példa hosszúságú kimondott szöveg az alkalmazáshoz. Az `phonenumber` entitás csak a kulturális környezetekben támogatott `en-us` .

## <a name="types-of-a-phone-number"></a>A telefonszám típusa
`Phonenumber` felügyelt a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) adattárból

## <a name="resolution-for-this-prebuilt-entity"></a>Az előre elkészített entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)
A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

Az alábbi példa a **beépített. telefonszám** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [százalékos](luis-reference-prebuilt-percentage.md), a [szám](luis-reference-prebuilt-number.md)és a [hőmérséklet](luis-reference-prebuilt-temperature.md) entitások ismertetése.
