---
title: A LUIS előre összeállított entitások hőmérséklet referencia – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ez a cikk tartalmazza a hőmérséklet előre összeállított entitások információ a Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 2716600346e65f2ca0b97e56cc156fdfb10ff3a4
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440958"
---
# <a name="temperature-entity"></a>Hőmérséklet entitás
Hőmérséklet kibontja a hőmérséklet-típusok különböző. Az entitás már be van tanítva, mert nem kell az alkalmazás hőmérséklet tartalmazó példa beszédmódok hozzáadása. Hőmérséklet-entitás támogatott [számos országban](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Hőmérséklet-típusok
Hőmérséklet felügyelje a [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github-adattár

## <a name="resolution-for-prebuilt-temperature-entity"></a>Előre összeállított hőmérséklet entitás feloldása
Az alábbi példa bemutatja a feloldása a **builtin.temperature** entitás.

```JSON
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a [százalékos](luis-reference-prebuilt-percentage.md), [szám](luis-reference-prebuilt-number.md), és [kora](luis-reference-prebuilt-age.md) entitásokat. 