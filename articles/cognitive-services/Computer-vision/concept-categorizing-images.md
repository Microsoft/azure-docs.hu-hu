---
title: Képkategorizálás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megismeri a Computer Vision API képkategorizálási funkciójának fogalmait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768106"
---
# <a name="categorize-images-by-subject-matter"></a>Képek kategorizálása tárgy szerint

A címkék és a leírások mellett a Computer Vision a képeken észlelt taxonómia-alapú kategóriákat is visszaadja. A címkéktől eltérően a kategóriák szülő/gyermek öröklődő hierarchiába vannak rendezve, és ezek közül kevesebb van (86, szemben több ezer címkével). Minden kategórianév angol nyelvű. A kategorizálás önmagában vagy az újabb címkemodell mellett is használhatja.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

A számítógépes látásmód széles körben vagy pontosabban kategorizálhatja a képeket az alábbi ábrán látható 86 kategória alapján. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![Csoportosított listák a kategóriabeanúció összes kategóriájából](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Képek kategorizálásának példái

A következő JSON-válasz bemutatja, Computer Vision milyen értéket ad vissza a példakép vizuális jellemzők alapján való kategorizálásakor.

![Egy nő egy épület tetőjében](./Images/woman_roof.png)

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

Az alábbi táblázat egy tipikus képkészletet és az egyes Computer Vision által visszaadott kategóriát mutatja be.

| Kép | Kategória |
|-------|----------|
| ![Négy ember volt együtt egy családként](./Images/family_photo.png) | people_group |
| ![Egy szkent mezőben ülve](./Images/cute_dog.png) | animal_dog |
| ![Egy személy, aki egy hegykőzeten áll napnyugtakor](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy tábla kenyeres szerepkörei](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Az API használata

A kategorizálási funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t egy natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Categories` bele a **visualFeatures lekérdezési** paraméterbe. Ezután a teljes JSON-válasz után egyszerűen elemezze a szakasz tartalmának `"categories"` sztringet.

* [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Következő lépések

Megismeri a képek [címkézésének és](concept-tagging-images.md) [leírásának kapcsolódó fogalmait.](concept-describing-images.md)
