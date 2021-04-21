---
title: Színséma észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A színséma képeken való észlelésével kapcsolatos fogalmak a Computer Vision API-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b113c424851065e244c3943bf4a4816bae9bba8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778912"
---
# <a name="detect-color-schemes-in-images"></a>Színsémák észlelése képeken

Computer Vision elemzi a képek színeit, hogy három különböző attribútumot biztosítson: a domináns előtérszínt, a domináns háttérszínt és a kép egészére jellemző domináns színeket. A visszaadott színek a következő készlethez tartoznak: fekete, kék, barna, szürke, zöld, narancssárga, rózsaszín, lila, piros, teal, fehér és sárga. 

Computer Vision kinyer egy kiemelőszínt is, amely a legszínesebb színt jelöli a képen a domináns színek és a telítettség kombinációja alapján. Az ékezet színe hexadecimális HTML-színkódként lesz visszaadva. 

Computer Vision logikai értéket is visszaad, amely jelzi, hogy egy kép fekete-fehér-e.

## <a name="color-scheme-detection-examples"></a>Színséma észlelési példái

Az alábbi példa a példakép színsémája Computer Vision által visszaadott JSON-választ mutatja be. Ebben az esetben a példakép nem fekete-fehér kép, de a domináns előtér- és háttérszínek feketék, és a kép egészének domináns színei fekete-fehérek.

![A napnyugtakor a kültéri hegy, egy személy silótejával](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Domináns színek példái

Az alábbi táblázat az egyes mintaképekkel visszaadott előtér-, háttér- és képszíneket tartalmazza.

| Kép | Domináns színek |
|-------|-----------------|
|![Fehér virág zöld háttérrel](./Images/flower.png)| Előtér: Fekete<br/>Háttér: Fehér<br/>Színek: Fekete, Fehér, Zöld|
![Egy állomáson áthaladó betanítás](./Images/train_station.png) | Előtér: Fekete<br/>Háttér: Fekete<br/>Színek: Fekete |

### <a name="accent-color-examples"></a>Ékezetes színek példái

 Az alábbi táblázat a visszaadott kiemelőszínt mutatja hexadecimális HTML-színértékként minden példaképhez.

| Kép | Kiegészítő szín |
|-------|--------------|
|![Egy személy, aki egy hegykőzeten áll napnyugtakor](./Images/mountain_vista.png) | #BB6D10 |
|![Fehér virág zöld háttérrel](./Images/flower.png) | #C6A205 |
|![Egy állomáson áthaladó betanítás](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete & fehér detektálás – példák

Az alábbi táblázat Computer Vision mintaképek fekete-fehér kiértékelési eredményeit mutatja be.

| Kép | Fekete & fehér? |
|-------|----------------|
|![Az épületek fekete-fehér képe a Következőben:](./Images/bw_buildings.png) | true |
|![Egy kék ház és az elülső ház](./Images/house_yard.png) | hamis |

## <a name="use-the-api"></a>Az API használata

A színséma észlelése funkció az API [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Color` bele a **visualFeatures lekérdezési** paraméterbe. Ezután a teljes JSON-válasz után egyszerűen elemezze a szakasz tartalmának `"color"` sztringet.

* [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
