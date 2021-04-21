---
title: Tartalomcímkék – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megismeri az Computer Vision API képcímkézési funkciójának fogalmait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3ef2d07b736d391fc3509266dea77fdc1b325ff2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778714"
---
# <a name="applying-content-tags-to-images"></a>Tartalomcímkék alkalmazása képekre

Computer Vision több ezer felismerhető tárgy, élőlény, táj és tevékenység alapján ad vissza címkéket. Amennyiben a címkék félreérthetőek vagy nem közismertek, az API válasza „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkék nincsenek rendszerezve, és nincs öröklési hierarchiájuk. A tartalomcímkék gyűjteménye képezi a kép ember által olvasható nyelven, teljes mondatokban megformált „leírásának” alapját. Fontos tudni, hogy a képleírásokhoz jelenleg csak az angol nyelv támogatott.

Egy kép feltöltése vagy URL-címének megadása után Computer Vision-algoritmusok adnak meg címkéket a képeken felismert tárgyak, élőlények és tevékenységek alapján. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.

## <a name="image-tagging-example"></a>Képcímkézési példa

Az alábbi JSON-válasz bemutatja, Computer Vision mit ad vissza a példaképben észlelt vizuális jellemzők címkézésekor.

![Egy kék ház és az elülső ház](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Az API használata

A címkézési funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Tags` bele a **visualFeatures lekérdezési** paraméterbe. Ezután a teljes JSON-válasz után egyszerűen elemezze a szakasz tartalmának `"tags"` sztringet.

* [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Következő lépések

Ismerje meg a képek [kategorizálásának](concept-categorizing-images.md) és [leírásának kapcsolódó fogalmait.](concept-describing-images.md)
