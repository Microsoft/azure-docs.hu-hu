---
title: Márka észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk az objektumok észlelésének speciális módját tárgyalja; a márka és/vagy embléma észlelése a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 40792585fbc52aaeec8a535b6a82decfce7618f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533689"
---
# <a name="detect-popular-brands-in-images"></a>Népszerű márkák felismerése képeken

A márka észlelése az [objektumok észlelésének](concept-object-detection.md) speciális módja, amely több ezer globális emblémát tartalmazó adatbázist használ a képekben vagy videókban található kereskedelmi márkák azonosítására. Ezt a funkciót használhatja például arra, hogy felderítse, mely márkák a legnépszerűbbek a közösségi médiában, vagy a média termékeinek legelterjedtebb eleme.

A Computer Vision szolgáltatás észleli, hogy van-e olyan márka emblémája egy adott képben; Ha igen, a rendszer visszaadja a márka nevét, a megbízhatósági pontszámot és az embléma körüli határoló mező koordinátáit.

A beépített embléma-adatbázis a népszerű márkákat tartalmazza a fogyasztói elektronika, a ruházati szolgáltatások és egyebek terén. Ha azt tapasztalja, hogy a keresett márkát nem észleli a Computer Vision szolgáltatás, akkor lehet, hogy jobban kiszolgálhatja a saját emblémás detektor létrehozását és betanítását a [Custom Vision](../custom-vision-service/index.yml) szolgáltatás használatával.

## <a name="brand-detection-example"></a>Példa a márka észlelésére

A következő JSON-válaszok azt szemléltetik, hogy milyen Computer Vision ad vissza, amikor a képeken a márkákat észleli.

![Egy piros póló egy Microsoft-címkével és-emblémával](./Images/red-shirt-logo.jpg)

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

Bizonyos esetekben a Brand detektor az embléma képét és a stilizált márkanevet is két külön emblémaként fogja felvenni.

![Egy szürke pulóver egy Microsoft-címkével és-emblémával](./Images/gray-shirt-logo.jpg)

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

A márka észlelése funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Brands` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"brands"` .

* [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)