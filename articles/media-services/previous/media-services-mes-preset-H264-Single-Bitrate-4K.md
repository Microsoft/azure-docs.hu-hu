---
title: H264 Single bitráta 4K Media Encoder Standard PRESET – Azure | Microsoft Docs
description: A cikk áttekintést nyújt a Media Encoder Standard H264 Single bitrate 4K-feladathoz.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 62984c938a3db67550b36c731e7a5a27de5df7ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012870"
---
# <a name="h264-single-bitrate-4k"></a>H264 Single Bitrate 4K

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` meghatározza a kódolási feladatok létrehozásakor használható kódolási beállításkészleteket. A (z) segítségével `preset name` megadhatja, hogy melyik formátumban szeretné kódolni a médiafájlt. Vagy létrehozhat egy saját JSON-vagy XML-alapú előkészletet (UTF-8 vagy UTF-16 kódolás használatával). Ezután átadja az egyéni beállításkészletet a kódolónak. A kódoló által támogatott összes előre definiált név listájának `Media Encoder Standard` megtekintéséhez tekintse meg a [Media Encoder standard feladat-előállítók](media-services-mes-presets-overview.md)című témakört.  
  
 Ez a témakör az `H264 Single Bitrate 4K` előre definiált XML-és JSON-formátumban jeleníti meg.  
  
 Ez az előre definiált, 18000 kbps sebességű és sztereó AAC hangú MP4-fájlt hoz létre. A profil, a bitráta, a mintavételezési sebesség és az előre definiált beállítások részletes ismertetését az alábbi XML-vagy JSON-kód alapján ellenőrizheti. Az ezen alapbeállításokban található egyes elemek, valamint az egyes elemek érvényes értékeinek magyarázata a [Media Encoder standard séma](media-services-mes-schema.md) témakörben található.  
  
> [!NOTE]
>  Szerezze be a prémium szintű fenntartott egységet 4K-kódolással. További információ: [a kódolás skálázása](./media-services-scale-media-processing-overview.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
