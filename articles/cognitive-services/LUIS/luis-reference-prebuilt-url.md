---
title: URL-alapú előre összeépített entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) URL-címen előre felépített entitásokra vonatkozó információkat tartalmaz.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535253"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>A LUIS-alkalmazás URL-re épülő előkészített entitása
URL-entitás kinyeri az URL-címeket tartománynevek vagy IP-címekkel. Mivel ez az entitás már be van tanítva, nem kell hozzáadnia például az alkalmazás URL-címeit tartalmazó hosszúságú kimondott szöveg. Az URL-entitás csak a kulturális környezetekben támogatott `en-us` .

## <a name="types-of-urls"></a>URL-típusok
Az URL-cím a [felismerők – Text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) adattárból kezelhető

## <a name="resolution-for-prebuilt-url-entity"></a>Az előre elkészített URL-entitás feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméter értéke `false` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméter értéke `true` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
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

A következő példa bemutatja, hogy az a https://www.luis.ai mesterséges intelligencia kiváló kognitív szolgáltatásainak példája

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

További információ a [sorszámok](luis-reference-prebuilt-ordinal.md), a [számok](luis-reference-prebuilt-number.md)és a [hőmérsékleti](luis-reference-prebuilt-temperature.md) entitásokról.
