---
title: Videók körülvágása a Media Encoder Standard használatával – Azure | Microsoft Docs
description: A vágás a videó keretén belül egy téglalap alakú ablak kiválasztásának, valamint az adott ablakon belüli képpontok kódolásának a folyamata. Ez a cikk bemutatja, hogyan vágja le a videókat Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016729"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Videók körülvágása a Media Encoder Standarddel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

A bemeneti videó kivágásához Media Encoder Standard (MES) használható. A vágás a videó keretén belül egy téglalap alakú ablak kiválasztásának, valamint az adott ablakon belüli képpontok kódolásának a folyamata. Az alábbi ábrán a folyamat szemlélteti.

![Videó körülvágása](./media/media-services-crop-video/media-services-crop-video01.png)

Tegyük fel, hogy olyan videót használ, amely 1920 × 1080 képpontos felbontással rendelkezik (16:9 oldalarányú), de a bal és a jobb oldali fekete sávokkal (oszlopokkal) rendelkezik, így csak a 4:3-es vagy a 1440x1080 képpont aktív videót tartalmaz. A MES használatával levágja vagy szerkesztheti a fekete sávokat, és kódolhatja a 1440x1080 régiót.

A MES-beli levágás egy előfeldolgozási szakasz, ezért a kódolási beállításkészletben lévő levágási paraméterek az eredeti bemeneti videóra érvényesek. A kódolás egy későbbi fázis, és a szélességi/magassági beállítások az *előre feldolgozott* videóra vonatkoznak, és nem az eredeti videóra. Az előre beállított beállítások megtervezésekor a következőket kell tennie: (a) válassza ki a vágási paramétereket az eredeti bemeneti videó alapján, és (b) a bevágott videó alapján válassza ki a kódolási beállításokat. Ha nem felel meg a bevágott videóhoz tartozó kódolási beállításoknak, a kimenet nem a várt módon fog megjelenni.

A [következő](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) témakör bemutatja, hogyan hozhat létre egy MES-kódolási feladatot, és hogyan határozhat meg egyéni beállításkészletet a kódolási feladathoz. 

## <a name="creating-a-custom-preset"></a>Egyéni beállításkészlet létrehozása
A ábrán látható példában:

1. Az eredeti bemenet 1920 × 1080
2. A bemeneti keretbe tartozó 1440x1080-kimenetre kell levágva.
3. Ez azt jelenti, hogy a (1920 – 1440)/2 = 240 X eltolása és a nulla Y eltolása
4. A körülvágási négyszög szélessége és magassága 1440 és 1080, illetve
5. A kódolás szakaszban a kérdés, hogy három réteget hoz létre, a 1440x1080, a 960x720 és a 480x360 felbontása

### <a name="json-preset"></a>JSON-készlet

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="restrictions-on-cropping"></a>A levágás korlátozásai
A levágási funkció manuális. A bemeneti videót egy megfelelő szerkesztési eszközbe kell betölteni, amely lehetővé teszi az érdekes keretek kiválasztását, a kurzort a kivágási négyszög eltolásának meghatározásához, hogy meghatározza az adott videóhoz hangolt kódolási beállításkészletet stb. Ez a funkció nem teszi lehetővé a következő műveleteket: a fekete postafiók/pillarbox szegélyének automatikus észlelése és eltávolítása a bemeneti videóban.

A következő korlátozások vonatkoznak a levágási szolgáltatásra. Ha ezek nem teljesülnek, a kódolási feladat sikertelen lehet, vagy váratlan kimenetet eredményezhet.

1. A körülvágási négyszög összehangolása és mérete a bemeneti videón belül elfér
2. A fentiekben leírtaknak megfelelően a kódolási beállításokban szereplő szélesség & magasságnak meg kell egyeznie a bevágott videóval
3. A levágás a tájkép módban rögzített videókra vonatkozik (azaz nem alkalmazható a vertikálisan vagy álló módban tárolt okostelefonokkal rögzített videókra)
4. A legjobban a szögletes képpontokkal rögzített progresszív videóval működik

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Az AMS által kínált nagyszerű funkciók megismeréséhez tekintse meg Azure Media Services képzési útvonalakat.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
