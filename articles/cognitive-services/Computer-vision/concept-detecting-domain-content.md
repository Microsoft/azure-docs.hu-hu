---
title: Tartományi specifikus tartalom – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan adhat meg egy képkategorizálási tartományt a képekkel kapcsolatos részletesebb információk visszaadásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68946339"
---
# <a name="detect-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

A címkézés és a magas szintű kategorizálás mellett a Computer Vision a speciális adatokra képzett modellekkel is támogatja a további, tartományra jellemző elemzéseket.

A tartományszintű modelleket kétféleképpen használhatja: önmagukban (hatókörön belüli elemzés) vagy a kategorizálási funkció fejlesztésével.

### <a name="scoped-analysis"></a>Hatókörön belüli elemzés

A képeket a models [\</Model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API meghívásával elemezheti úgy, hogy csak a kiválasztott tartományhoz tartozó modellt használja.

Az alábbi példa egy, a **modell/hírességek/elemzés** API által visszaadott JSON-választ ad vissza az adott rendszerképhez:

![Németh Beáta állandó, mosolygós](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Továbbfejlesztett kategorizálási elemzés

Az általános képelemzés kiegészítéseként a tartományra jellemző modelleket is használhatja. Ezt a [magas szintű kategorizálás](concept-categorizing-images.md) részeként úgy teheti meg, hogy a tartományra jellemző modelleket az [elemzési API-](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) hívás *részleteit* tartalmazó paraméterben megadja.

Ebben az esetben az 86 kategóriába tartozó taxonómia-osztályozó a neve első. Ha az észlelt kategóriák bármelyike megfelel a tartományra jellemző modellnek, a rendszer átadja a rendszerképet a modellnek, és hozzáadja az eredményeket is.

A következő JSON-válasz azt mutatja be, hogy a tartomány-specifikus elemzések hogyan szerepelhetnek a `detail` szélesebb kategorizálási elemzések csomópontjaiban.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>A tartományhoz tartozó modellek listázása

A Computer Vision jelenleg a következő tartományszintű modelleket támogatja:

| Name (Név) | Leírás |
|------|-------------|
| hírességek | Híresség-felismerés, a `people_` kategóriába sorolt rendszerképek esetén támogatott. |
| arcrész | A tereptárgyak felismerése, amely a `outdoor_` vagy `building_` kategóriába sorolt rendszerképek esetén támogatott |

A [models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API meghívása ezeket az információkat az egyes modellek által alkalmazható kategóriákkal együtt fogja visszaadni:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

A [képek kategorizálásával](concept-categorizing-images.md)kapcsolatos fogalmak megismerése.
