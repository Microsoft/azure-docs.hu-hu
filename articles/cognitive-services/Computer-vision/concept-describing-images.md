---
title: Képleírások – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API rendszerkép-leírási funkciójával kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4fd328dad3544697ba1f4896f7383be857b097a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96530663"
---
# <a name="describe-images-with-human-readable-language"></a>Képek leírása emberi olvasásra alkalmas nyelvvel

Computer Vision elemezheti a rendszerképet, és létrehozhat egy olyan, emberi olvasásra alkalmas mondatot, amely leírja annak tartalmát. Az algoritmus a különböző vizualizációs funkciók alapján több leírást ad vissza, és mindegyik Leírás megbízhatósági pontszámot kap. A végső kimenet a legmagasabbtól a legalacsonyabb megbízhatóságig rendezett leírások listája.

## <a name="image-description-example"></a>Példa a képek leírására

A következő JSON-válasz azt szemlélteti, hogy milyen Computer Vision ad vissza, amikor leírja a képet a vizualizáció funkciói alapján.

![A Manhattanben található épületek fekete-fehér képe](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Az API használata

A rendszerkép leírása funkció a [képelemzési](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Description` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"description"` .

* [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [címkézheti a képeket](concept-tagging-images.md) és [kategorizálhatja a képeket](concept-categorizing-images.md).
