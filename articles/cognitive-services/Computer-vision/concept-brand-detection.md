---
title: Márkaészlelés – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk az objektumészlelés speciális módját ismerteti; márka és/vagy embléma észlelése a Computer Vision API-val.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 5892fc0bbbd07690ff010e8e1212a914733cbb18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778984"
---
# <a name="detect-popular-brands-in-images"></a>Népszerű márkák észlelése képeken

A márkaészlelés [](concept-object-detection.md) az objektumészlelés egy speciális módja, amely globális emblémák ezreit használó adatbázissal azonosítja a kereskedelmi márkákat képeken vagy videókban. Ezzel a funkcióval például felfedezheti, hogy mely márkák a legnépszerűbbek a közösségi médiában, vagy melyek a legnépszerűbbek a médiatermékelhelyezésben.

A Computer Vision szolgáltatás észleli, hogy vannak-e márkaemblémák egy adott képen; Ha igen, visszaadja az embléma körüli határolókeret márkanevét, megbízhatósági pontszámát és koordinátáit.

A beépített embléma-adatbázis olyan népszerű márkákat fed le, mint a fogyasztói elektronikai cikkek, a ruházat és más termékek. Ha úgy találja, hogy a Computer Vision szolgáltatás nem észleli a keresett márkát, jobb, ha saját emblémaérzékelőt hoz létre és betanít az [Custom Vision](../custom-vision-service/index.yml) szolgáltatással.

## <a name="brand-detection-example"></a>Márkaészlelési példa

Az alábbi JSON-válaszok bemutatják, Computer Vision mit ad vissza, amikor márkaeket észlel a példaképeken.

![Piros ing Microsoft-címkével és emblémával](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

Bizonyos esetekben a márkaérzékelő két külön emblémaként fogja felvenni az embléma képét és a stilizált márkanevet.

![Szürke szürke árnyalat, rajta egy Microsoft-címkével és emblémával](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Az API használata

A márkaészlelési funkció a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Foglalja `Brands` bele a **visualFeatures lekérdezési** paraméterbe. Ezután a teljes JSON-válasz után egyszerűen elemezze a szakasz tartalmának `"brands"` sztringet.

* [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)