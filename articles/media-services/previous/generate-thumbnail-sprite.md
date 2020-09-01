---
title: Miniatűr sprite létrehozása a Azure Media Serviceskal | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan hozhatja Azure Media Services a miniatűr Sprite-ot.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: 1d05e73bc9aa51bf20121790de94b1b6d0e26615
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268570"
---
# <a name="generate-a-thumbnail-sprite"></a>Miniatűr sprite létrehozása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Media Encoder Standard használatával létrehozhat egy miniatűr sprite-t, amely egy JPEG-fájl, amely több kis felbontású miniatűrt tartalmaz, és egyetlen (nagy) képként összefűzve, egy VTT-fájllal együtt. Ez a VTT-fájl megadja az időtartományt az egyes bélyegképek által reprezentált bemeneti videóban, valamint a miniatűr méretét és koordinátáit a nagyméretű JPEG-fájlon belül. A videolejátszó a VTT fájlt és a sprite-rendszerképet használja a "vizualizáció" seekbar megjelenítéséhez, és vizuális visszajelzést nyújtó megjelenítőt biztosít, amikor visszakerül a videó idővonalára, és továbbítja azokat.

Ahhoz, hogy a Media Encoder Standardt a miniatűr sprite létrehozásához használni, az előre beállított:

1. A JPG miniatűr képformátumot kell használni
2. Meg kell adni a kezdő/a érték/tartomány értékeket időbélyegként, vagy%-os értéket (és nem a keretek számát). 
    
    1. Az időbélyegek és a (z)% Values együttes használata rendben van

3. A SpriteColumn értéknek kell lennie, nem negatív számnak, vagy egyenlőnek kell lennie 1-nél

    1. Ha a SpriteColumn értéke M >= 1, a kimeneti rendszerkép egy M oszlopot tartalmazó négyszög. Ha a #2on keresztül generált bélyegképek száma nem az M pontos többszöröse, akkor az utolsó sor hiányos lesz, és fekete képponttal marad.  

Alább bemutatunk egy példát:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Ismert problémák

1.  A sprite-lemezképek egyetlen sornyi képpel nem hozhatók létre (a SpriteColumn = 1 eredménye egyetlen oszlopból álló kép).
2.  A sprite-lemezképek közepes méretű JPEG-képekre való darabolása még nem támogatott. Ezért ügyelni kell a bélyegképek számának és méretének korlátozására, hogy az eredményül kapott összefűzött miniatűr sprite a 8P képpont vagy annál kisebb legyen.
3.  A Azure Media Player támogatja a sprite-ket a Microsoft Edge, a Chrome és a Firefox böngészőkben. A IE11 nem támogatja a VTT-elemzést.

## <a name="next-steps"></a>További lépések

[Tartalom kódolása](media-services-encode-asset.md)
