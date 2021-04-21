---
title: Objektumészlelés – Computer Vision
titleSuffix: Azure Cognitive Services
description: Megismeri a Computer Vision API objektumészlelési funkciójának – használat és korlátok – fogalmait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a705a4134ec22d1cb14406cab4491f2af9177b48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767996"
---
# <a name="detect-common-objects-in-images"></a>Képek gyakori objektumának észlelése

Az objektumészlelés hasonló a [címkézéséhez,](concept-tagging-images.md)de az API visszaadja a határolókeret koordinátáit (képpontban) minden egyes megtalált objektumhoz. Ha például egy képen kutya, macska és személy látható, a Detect művelet ezeket az objektumokat a koordinátáival együtt sorolja fel a képen. Ezzel a funkcióval feldolgozhatja a képek objektumai közötti kapcsolatokat. Azt is meghatározhatja, hogy egy címkének több példánya is van-e egy képen.

A Detect API címkéket alkalmaz a képen azonosított objektumok vagy élőlények alapján. Jelenleg nincs formális kapcsolat a címkézési és az objektumészlelési taxonómia között. Elméleti szinten a Detect API csak objektumokat és élő dolgokat keres, a Tag API pedig olyan környezeti kifejezéseket is tartalmazhat, mint a "beltéri", amelyek határoló keretekkel nem honosítottak.

## <a name="object-detection-example"></a>Objektumészlelési példa

A következő JSON-válasz bemutatja, Computer Vision mit ad vissza, amikor objektumokat észlel a példaképen.

![Egy nő, aki Egy Microsoft Surface-eszközt használ egy konyha](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Korlátozások

Fontos megjegyezni az objektumészlelés korlátait, hogy elkerülheti vagy enyhítheti a téves negatívok (kihagyott objektumok) és a korlátozott részletesség hatásait.

* A rendszer általában nem észleli az objektumokat, ha kicsik (a kép kevesebb mint 5%-a).
* A rendszer általában nem észleli az objektumokat, ha szorosan egymásba vannak rendezve (például egy lapkát).
* Az objektumokat nem különböztetjük meg márka- vagy terméknevek alapján (például az áruházi polcokon különböző típusú fogasok). A márkainformációk azonban lekért képekről a Márkaészlelési [funkcióval.](concept-brand-detection.md)

## <a name="use-the-api"></a>Az API használata

Az objektumészlelési funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Objects` bele a **visualFeatures lekérdezési** paraméterbe. Ezután, amikor a teljes JSON-választ kap, egyszerűen elemezze a szakasz tartalmának `"objects"` sztringet.

* [Rövid útmutató: Computer Vision REST API ügyfélkódtárak létrehozása](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
