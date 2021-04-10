---
title: H264 Single bitráta, alacsony minőségű SD Androidhoz | Microsoft Docs
description: A témakör áttekintést nyújt a **H264 Single bitráta alacsony minőségű SD for Android** -feladathoz.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 67d3446d-e3b8-419f-bbf8-e5149c108531
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4c32fc01b2011d49dc50a67edc98227f5b31750c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011697"
---
# <a name="h264-single-bitrate-low-quality-sd-for-android"></a>H264 Single Bitrate Low Quality SD for Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` meghatározza a kódolási feladatok létrehozásakor használható kódolási beállításkészleteket. A (z) segítségével `preset name` megadhatja, hogy melyik formátumban szeretné kódolni a médiafájlt. Vagy létrehozhat egy saját JSON-vagy XML-alapú előkészletet (UTF-8 vagy UTF-16 kódolás használatával). Ezután átadja az egyéni beállításkészletet a kódolónak. A kódoló által támogatott összes előre definiált név listájának `Media Encoder Standard` megtekintéséhez tekintse meg a [Media Encoder standard feladat-előállítók](media-services-mes-presets-overview.md)című témakört.  
  
 Ez a témakör az `H264 Single Bitrate Low Quality SD for Android` előre definiált XML-és JSON-formátumban jeleníti meg.  
  
 Ez az előre definiált, 56 Kbps sebességű és sztereó AAC hangú MP4-fájlt hoz létre. A profil, a bitráta, a mintavételezési sebesség és az előre definiált beállítások részletes ismertetését az alábbi XML-vagy JSON-kód alapján ellenőrizheti. Az ezen alapbeállításokban található egyes elemek, valamint az egyes elemek érvényes értékeinek magyarázata a [Media Encoder standard séma](media-services-mes-schema.md) témakörben található.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:05</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>56</Bitrate>  
          <Width>176</Width>  
          <Height>144</Height>  
          <FrameRate>12/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>2</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>56</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>HEAACV2</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>24</Bitrate>  
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
      "KeyFrameInterval": "00:00:05",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Baseline",  
          "Level": "2",  
          "Bitrate": 56,  
          "MaxBitrate": 56,  
          "BufferWindow": "00:00:05",  
          "Width": 176,  
          "Height": 144,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "12/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "HEAACV2",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 24,  
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
