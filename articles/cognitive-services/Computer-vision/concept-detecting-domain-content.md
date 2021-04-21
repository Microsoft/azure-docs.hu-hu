---
title: Tartományspecifikus tartalom – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan határozható meg egy képkategorizálási tartomány, amely részletesebb információkat ad vissza egy rendszerképről.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 530ca81cedad06c949323889cc02d2a233dd0c02
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778822"
---
# <a name="detect-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

A címkézés és a magas szintű kategorizálás mellett a Computer Vision a speciális adatokkal betanított modelleket használó további tartományspecifikus elemzéseket is támogat.

A tartományspecifikus modellek kétféleképpen használhatók: önmagukban (hatókörrel kapcsolatos elemzés), vagy a kategorizálási funkció fejlesztéseként.

### <a name="scoped-analysis"></a>Hatókörrel alá tartozó elemzés

A képeket csak a kiválasztott tartományspecifikus modellel elemezheti a [Models/ \<model\> /Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API hívásával.

Az alábbiakban egy JSON-mintaválasz látható, amelyet a **models/celebrities/analyze** API adott képhez adott vissza:

![Álló Satya Nadella, mosolygó](./images/satya.jpeg)

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

Az általános képelemzés kiegészítéseként tartományspecifikus modelleket is használhat. Ezt magas szintű [kategorizálás](concept-categorizing-images.md) részeként kell megtennie, ha tartományspecifikus  modelleket ad meg az [Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API hívás details paraméterében.

Ebben az esetben először a 86 kategóriás besorolási osztályozót hívjuk meg. Ha az észlelt kategóriák bármelyikének van egyező tartományspecifikus modellje, a rendszer a képet is átküldi ezen a modellen, és hozzáadja az eredményeket.

A következő JSON-válasz bemutatja, hogyan használhatja csomópontként a tartományspecifikus elemzést egy szélesebb körű `detail` kategorizálási elemzésben.

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

## <a name="list-the-domain-specific-models"></a>A tartományspecifikus modellek felsorolása

A Computer Vision a következő tartományspecifikus modelleket támogatja:

| Név | Leírás |
|------|-------------|
| Hírességek | Hírességek felismerése, támogatott a kategóriában besorolt `people_` képekhez |
| Tereptárgyak | Nevezetességek felismerése, támogatott a vagy kategóriákba sorolt `outdoor_` `building_` képekhez |

A [Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f20e) API hívása ezeket az információkat adja vissza az egyes modellek által alkalmazható kategóriákkal együtt:

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

## <a name="next-steps"></a>Következő lépések

Megismeri a [képek kategorizálásának fogalmait.](concept-categorizing-images.md)
