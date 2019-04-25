---
title: Képek – Computer Vision kategorizálása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a lemezkép kategorizálási funkciója a Computer Vision API kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203322"
---
# <a name="categorize-images-by-subject-matter"></a>Képek kategorizálása téma szerint

Címkék és a egy leírást, mellett a Computer Vision észlelt a képet a besorolás-alapú kategóriák adja vissza. Ellentétben a címkék és kategóriák örökletes szülő-gyermek hierarchiában vannak rendezve, és kevesebb ilyen (ellentétben a címkék ezer 86). Alkalmazáskategória-neveket angolul jelennek meg. Kategorizálási teheti meg saját maga, vagy az újabb címke-modell mellett.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

Számítógépes látástechnológia is kategorizálása kép széles körben vagy kifejezetten, 86 kategóriák listája segítségével az alábbi ábrán. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![a kategória besorolás minden kategória csoportosított listája](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Kép kategorizálási példák

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen kategorizálásához.

![Egy nő a tető egy apartman épület](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Az alábbi táblázatban egy jellemző kép beállítása és az egyes rendszerképek a Computer Vision által visszaadott kategória mutatja be.

| Image | Category |
|-------|----------|
| ![Négy személyt együttesen, egy csomagcsalád feltételéről](./Images/family_photo.png) | people_group |
| ![Egy grassy mező úgy ételadagot](./Images/cute_dog.png) | animal_dog |
| ![Egy személy állandó az egy Hegyi rock napnyugta:](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy táblán kenyér szerepkörök tárházát](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek leíró](concept-describing-images.md).
