---
title: Képtípus észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API képtípus-észlelési funkciójával kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6d2ed00f3fc6f5b52a9a13a96f1e1659e30f02d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532601"
---
# <a name="detecting-image-types-with-computer-vision"></a>Rendszerképek típusának észlelése Computer Vision

A [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API-val a Computer Vision képes elemezni a képek tartalmának típusát, jelezve, hogy egy kép ClipArt vagy vonalas rajz-e.

## <a name="detecting-clip-art"></a>ClipArt-elemek észlelése

Computer Vision elemez egy rendszerképet, és az alábbi táblázatban leírtak szerint a képfájlnak azt a valószínűségét határozza meg, hogy a kép egy 0 és 3 közötti skálán legyen.

| Érték | Értelmezés |
|-------|---------|
| 0 | Nem ClipArt |
| 1 | Nem egyértelmű |
| 2 | Normál – ClipArt |
| 3 | Jó – ClipArt |

### <a name="clip-art-detection-examples"></a>ClipArt-észlelési példák

A következő JSON-válaszok azt szemléltetik, hogy milyen Computer Vision ad vissza, ha a példa képének valószínűségét a Clip Art ábrázolja.

![Egy szeletes sajttal rendelkező ClipArt-kép](./Images/cheese_clipart.png)

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

![Egy kék ház és az első udvar](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Vonalas rajzok észlelése

Computer Vision egy képet elemez, és egy logikai értéket ad vissza, amely azt jelzi, hogy a képen egy vonal típusú rajz található-e.

### <a name="line-drawing-detection-examples"></a>Példák a vonalas rajzolás észlelésére

A következő JSON-válaszok azt szemléltetik, hogy milyen Computer Vision ad vissza, ha azt jelzi, hogy a képek vonalas rajzok-e.

![Egy oroszlánból álló vonalas rajzolási ábra](./Images/lion_drawing.png)

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

![Fehér virág Zöld háttérrel](./Images/flower.png)

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

A rendszerkép típusú észlelési funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `ImageType` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"imageType"` .

* [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
