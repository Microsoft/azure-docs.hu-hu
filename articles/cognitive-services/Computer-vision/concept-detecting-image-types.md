---
title: Képtípus észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API képtípus-észlelési funkciójának fogalmai.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dc24788ddd21ca2b7df1f9f92238c776dee33016
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778876"
---
# <a name="detecting-image-types-with-computer-vision"></a>Képtípusok észlelése Computer Vision

A [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API Computer Vision segítségével elemezheti a képek tartalomtípusát, jelezve, hogy a kép ClipArt vagy vonalrajz.

## <a name="detecting-clip-art"></a>ClipArt észlelése

Computer Vision egy képet elemez, és az alábbi táblázatban leírtak szerint 0 és 3 között skálán megméri annak valószínűségét.

| Érték | Értelmezés |
|-------|---------|
| 0 | Nem ClipArt |
| 1 | Kétértelmű |
| 2 | Normál ClipArt |
| 3 | Jó ClipArt |

### <a name="clip-art-detection-examples"></a>ClipArt-észlelési példák

Az alábbi JSON-válaszok azt szemléltetik, Computer Vision milyen értéket ad vissza, amikor a példaként megadott képek valószínűségét a ClipArt típusú képként minősíti.

![Egy sajtszelet ClipArt-képe](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Egy kék ház és az elülső ház](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Vonalrajzok észlelése

Computer Vision egy képet, és egy logikai értéket ad vissza, amely jelzi, hogy a kép vonalrajz-e.

### <a name="line-drawing-detection-examples"></a>Vonalrajzészlelési példák

Az alábbi JSON-válaszok azt szemléltetik, Computer Vision mit ad vissza, amikor jelzi, hogy a példaképek vonalrajzok-e.

![Egy vonal, amely egy oroszlánt rajzol](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Fehér virág zöld háttérrel](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Az API használata

A képtípus-észlelési funkció a Analyze Image [RÉSZE.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Ezt az API-t egy natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `ImageType` bele a **visualFeatures lekérdezési** paraméterbe. Ezután, amikor a teljes JSON-választ kap, egyszerűen elemezze a szakasz tartalmának `"imageType"` sztringet.

* [Rövid útmutató: Computer Vision REST API ügyfélkódtárak létrehozása](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
