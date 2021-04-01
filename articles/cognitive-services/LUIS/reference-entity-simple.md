---
title: Egyszerű entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Egy egyszerű entitás a gépi tanulási környezet egyetlen koncepcióját ismerteti. Ha egyszerű entitást használ az eredmények javítására, vegyen fel egy kifejezést tartalmazó listát.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: 384d3df2de551e7c79f13a0fe47ffb26c7825f1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91539286"
---
# <a name="simple-entity"></a>Egyszerű entitás

Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gépi tanulási környezetből tanulnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy [kifejezési listát](luis-concept-feature.md) , ha egyszerű entitást használ a használt nevek jelzésének növelésére.

**Az entitás jól illeszkedik, ha:**

* Az adat nem konzisztens formátumú, de ugyanazt a dolgot jelzi.

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON-ra

`Bob Jones wants 3 meatball pho`

Az előző lemondás során `Bob Jones` egyszerű entitásként van megjelölve `Customer` .

A végpont által visszaadott adatok között szerepel az entitás neve, a felderített szöveg a Kimondás után, a felderített szöveg helye és a pontszám:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Adatobjektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|`Customer`|`bob jones`|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A minta szintaxisának megismerése](reference-pattern-syntax.md)