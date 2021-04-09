---
title: Objektum észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API-használat és-korlátok objektum-észlelési funkciójával kapcsolatos fogalmak megismerése.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96533842"
---
# <a name="detect-common-objects-in-images"></a>Gyakori objektumok észlelése képekben

Az objektum észlelése hasonló a [címkézéshez](concept-tagging-images.md), de az API a megtalált objektumok koordinátáit (képpontban) adja vissza. Ha például egy rendszerkép kutyát, macskát és személyt tartalmaz, az észlelési művelet a képen látható koordinátákkal együtt listázza ezeket az objektumokat. Ezzel a funkcióval feldolgozhatja a képek objektumai közötti kapcsolatokat. Azt is megadhatja, hogy egy adott címkén belül több példány is van-e a képen.

Az észlelési API az objektumokon vagy a képen azonosított dolgokon alapuló címkéket alkalmaz. Jelenleg nincs formális kapcsolat a címkézési besorolás és az objektum-észlelési besorolás között. Elméleti szinten az észlelési API csak az objektumokat és az élő dolgokat észleli, míg a címke API olyan környezetfüggő kifejezéseket is tartalmazhat, mint például a "beltéri", amely nem honosítható meg a határolókeret használatával.

## <a name="object-detection-example"></a>Objektum-észlelési példa

A következő JSON-válasz azt szemlélteti, hogy milyen Computer Vision ad vissza, amikor a példában szereplő objektumokat észleli.

![Egy Microsoft Surface-eszközt használó nő egy konyhában](./Images/windows-kitchen.jpg)

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

Fontos megjegyezni az objektumok észlelésének korlátozásait, így elkerülhető vagy enyhíthető a hamis negatív (kihagyott objektumok) és a korlátozott részletesség hatása.

* Az objektumok általában nem észlelhetők, ha kicsik (a rendszerkép kevesebb, mint 5%-a).
* A rendszer általában nem észleli az objektumokat, ha azok egymással szorosan vannak rendezve (például egy halom lemez).
* Az objektumokat nem különbözteti meg a márka vagy a Terméknév (például a különböző típusú üdítőitalok egy tárolási polcon). A arculatot azonban a [márka észlelési](concept-brand-detection.md) funkciója segítségével kérheti le.

## <a name="use-the-api"></a>Az API használata

Az objektum-észlelési funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Objects` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"objects"` .

* [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
