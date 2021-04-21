---
title: Képleírások – Computer Vision
titleSuffix: Azure Cognitive Services
description: Az Computer Vision API képleírási funkciójának fogalmai.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c517fa98bfc17d4702a51d4990e860b2ed7aaefd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778930"
---
# <a name="describe-images-with-human-readable-language"></a>Képek írása ember által olvasható nyelvvel

Computer Vision képet elemezhet, és létrehozhat egy emberi olvasásra használható mondatot, amely leírja annak tartalmát. Az algoritmus valójában több leírást ad vissza különböző vizuális jellemzők alapján, és minden leírás megbízhatósági pontszámot kap. A végső kimenet a leírások listája, amelyek a legmagasabb megbízhatósági szinttől a legalacsonyabb megbízhatóságig vannak megrendelve.

## <a name="image-description-example"></a>Példa képleírásra

A következő JSON-válasz bemutatja, Computer Vision mit ad vissza, amikor a vizuális jellemzők alapján leírja a példaképet.

![Az épületek fekete-fehér képe a Következőben:](./Images/bw_buildings.png)

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

A képleírási funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Description` bele a **visualFeatures lekérdezési** paraméterbe. Ezután a teljes JSON-válasz után egyszerűen elemezze a szakasz tartalmának `"description"` sztringet.

* [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Következő lépések

Megismeri a képek [címkézésének és](concept-tagging-images.md) a képek [kategorizálásának kapcsolódó fogalmait.](concept-categorizing-images.md)
