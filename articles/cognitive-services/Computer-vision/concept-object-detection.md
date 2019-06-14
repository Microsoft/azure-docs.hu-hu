---
title: Objektum észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ismerje meg az észlelési szolgáltatás a Computer Vision API – a kapcsolódó fogalmak használatát és korlátait.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 012ab849c926de332da55361c79c76c5a1311169
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368041"
---
# <a name="detect-common-objects-in-images"></a>Közös objektumok észlelése képeken

Objektumfelismerés hasonlít a [címkézés](concept-tagging-images.md), azonban az API-t adja vissza a határolókeret koordinátái (képpontban) minden egyes objektum található. Például képet tartalmaz egy kutya, cat és személy, ha a hibakeresés művelet felsorolja azokat az objektumokat a a koordináták a képen. Ez a funkció használatával feldolgozni a képet az objektumok közötti kapcsolatok. Azt is lehetővé teszi, hogy vannak-e a képet ugyanazt a címkét több példányát.

A észlelése API objektumok vagy élő dolgot azonosítani a képen alapuló címkék vonatkozik. Jelenleg nincsenek a címkézési besorolás és a objektum észlelési besorolás formális kapcsolatát. Elméleti szinten a észlelése API csak megkeresi objektumok és élő dolog, amíg a címke API is használható például a "belső" környezeti feltételek, amelyeket nem a határoló be a honosított.

## <a name="object-detection-example"></a>Objektum észlelési példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha az a példában a képen objektumok észlelése.

![Egy nő egy konyha a Microsoft Surface-eszközök használata](./Images/windows-kitchen.jpg)

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

Fontos megjegyezni objektumfelismerés vonatkozó korlátozások, így elkerülheti, vagy téves negatív (kihagyott objektumok) és a korlátozott részletes enyhítésén.

* Objektumok általában rendszer nem észleli, ha kis (kevesebb mint 5 %-a képen).
* Objektumok észlelése általában nem, ha szoros együttműködésben szélesen (például a lemezek stack).
* Objektumok nem különbözteti meg a márkáról vagy termék neve (szénsavas store tároló, például a különböző típusú). Azonban információkat szerezhet a márka lemezkép használatával a [saját arculat észlelési](concept-brand-detection.md) funkció.

## <a name="use-the-api"></a>Az API használata

Az észlelési szolgáltatás részét képezi a [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-t. Ez az API REST-hívások vagy a natív SDK keresztül hívása. Például `Objects` a a **visualFeatures** lekérdezési paraméter. Ezt követően a teljes JSON-választ kap, ha egyszerűen elemezni a karakterlánc a tartalmát a `"objects"` szakaszban.

* [Rövid útmutató: Kép (.NET SDK-t) elemzése](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rövid útmutató: Kép (REST API) elemzése](./quickstarts/csharp-analyze.md)