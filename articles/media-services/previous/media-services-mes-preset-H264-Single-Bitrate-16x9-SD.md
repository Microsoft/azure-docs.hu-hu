---
title: H264 Single bitráta 16x9 SD Media Encoder Standard PRESET – Azure | Microsoft Docs
description: A témakör áttekintést nyújt a **H264 Single bitráta 16X9 SD** -feladatról.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ce0efc07-3461-44f6-a7bc-c4877bc09529
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 03ba7fe9785119050759da501d55c2decd693428
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015607"
---
# <a name="h264-single-bitrate-16x9-sd"></a>H264 Single Bitrate 16x9 SD

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` meghatározza a kódolási feladatok létrehozásakor használható kódolási beállításkészleteket. A (z) segítségével `preset name` megadhatja, hogy melyik formátumban szeretné kódolni a médiafájlt. Vagy létrehozhat egy saját JSON-vagy XML-alapú előkészletet (UTF-8 vagy UTF-16 kódolás használatával). Ezután átadja az egyéni beállításkészletet a kódolónak. A kódoló által támogatott összes előre definiált név listájának `Media Encoder Standard` megtekintéséhez tekintse meg a [Media Encoder standard feladat-előállítók](media-services-mes-presets-overview.md)című témakört.  
  
 Ez a témakör az `H264 Single Bitrate 16x9 SD` előre definiált XML-és JSON-formátumban jeleníti meg.  
  
 Ez az előre definiált, 2200 kbps sebességű és sztereó AAC hangú MP4-fájlt hoz létre. A profil, a bitráta, a mintavételezési sebesség és az előre definiált beállítások részletes ismertetését az alábbi XML-vagy JSON-kód alapján ellenőrizheti. Az ezen alapbeállításokban található egyes elemek, valamint az egyes elemek érvényes értékeinek magyarázata a [Media Encoder standard séma](media-services-mes-schema.md) témakörben található.  
  
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
          <Bitrate>2200</Bitrate>  
          <Width>848</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>2200</MaxBitrate>  
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
          "Bitrate": 2200,  
          "MaxBitrate": 2200,  
          "BufferWindow": "00:00:05",  
          "Width": 848,  
          "Height": 480,  
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
