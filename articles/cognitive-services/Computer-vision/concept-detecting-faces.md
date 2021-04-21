---
title: Arcfelismerés – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megismeri a Computer Vision API arcészlelési funkciójának fogalmait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778840"
---
# <a name="face-detection-with-computer-vision"></a>Arcfelismerés Computer Vision

Computer Vision felismeri az emberi arcokat a képen, és elő tudja hozni az egyes észlelt arcok korát, nemét és téglalapját. 

> [!NOTE]
> Ezt a funkciót az Azure Face szolgáltatás [is](../face/index.yml) felkínálja. Ez az alternatíva részletesebb arcelemzést is kínál, beleértve az arcfelismerést és a testek észlelését. 

## <a name="face-detection-examples"></a>Arcfelismerési példák

Az alábbi példa egy egyetlen emberi arcot Computer Vision képre adott válaszát mutatja be.

![Tetőn álló nő arcának vizuális elemzése](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

A következő példa egy több emberi arcot tartalmazó képhez visszaadott JSON-választ mutatja be.

![Család fényképes arcának látáselemzése](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Az API használata

Az arcfelismerési funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t egy natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Faces` bele a **visualFeatures lekérdezési** paraméterbe. Ezután, amikor a teljes JSON-választ kap, egyszerűen elemezze a szakasz tartalmának `"faces"` sztringet.

* [Rövid útmutató: Computer Vision REST API ügyfélkódtárak létrehozása](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
