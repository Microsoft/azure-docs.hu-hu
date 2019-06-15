---
title: Tartalom alkalmazása-lemezképek – Computer Vision címkék
titleSuffix: Azure Cognitive Services
description: Ismerje meg, a címkézés funkcióját a Computer Vision API képeket kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: aeb03566a650fe46286d77913e0d36dcbb19f436
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759673"
---
# <a name="applying-content-tags-to-images"></a>Tartalomcímkék alkalmazása képekre

Számítógépes Látástechnológia alapján ad vissza címkéket ezer felismerhető objektumok, tárgy, táj és műveleteket. Amennyiben a címkék félreérthetőek vagy nem közismertek, az API válasza „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkékhez nincs besorolási rendszer és öröklési hierarchia. A tartalomcímkék gyűjteménye képezi a kép ember által olvasható nyelven, teljes mondatokban megformált „leírásának” alapját. Fontos tudni, hogy a képleírásokhoz jelenleg csak az angol nyelv támogatott.

Kép feltöltése vagy a kép URL-címének megadása után a Látástechnológiai algoritmus kimeneti címkék az objektumok, a tárgy és a műveletek azonosítja a kép alapján. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.

## <a name="image-tagging-example"></a>Kép címkézési példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha vizuális jellemzőket észlelhető a példaképen a címkézést.

![Egy kék ház és az első yard](./Images/house_yard.png).

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

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [lemezképek kategorizálásához](concept-categorizing-images.md) és [lemezképek leíró](concept-describing-images.md).
