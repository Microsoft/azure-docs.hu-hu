---
title: H264 Single bitráta 4x3 SD hang 5,1 | Microsoft Docs
description: A témakör áttekintést nyújt a **H264 Single bitráta 4X3 SD Audio 5,1** feladatról.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: e55dd302-2f42-46b5-ae17-bd3c72329c03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 18e905078e527ead1f67f4d868cd356688e58d55
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261802"
---
# <a name="h264-single-bitrate-4x3-sd-audio-51"></a>H264 Single Bitrate 4x3 SD Audio 5.1

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` meghatározza a kódolási feladatok létrehozásakor használható kódolási beállításkészleteket. A (z) segítségével `preset name` megadhatja, hogy melyik formátumban szeretné kódolni a médiafájlt. Vagy létrehozhat egy saját JSON-vagy XML-alapú előkészletet (UTF-8 vagy UTF-16 kódolás használatával). Ezután átadja az egyéni beállításkészletet a kódolónak. A kódoló által támogatott összes előre definiált név listájának `Media Encoder Standard` megtekintéséhez tekintse meg a [Media Encoder standard feladat-előállítók](media-services-mes-presets-overview.md)című témakört.  
  
 Ez a témakör az `H264 Single Bitrate 4x3 SD Audio 5.1` előre definiált XML-és JSON-formátumban jeleníti meg.  
  
 Ez az előre definiált egy MP4-fájlt hoz létre, amely 1800 kbps sebességű és AAC 5,1 hang. A profil, a bitráta, a mintavételezési sebesség és az előre definiált beállítások részletes ismertetését az alábbi XML-vagy JSON-kód alapján ellenőrizheti. Az egyes elemek leírását és az egyes elemek érvényes értékeit a [Media Encoder standard sémában](media-services-mes-schema.md)találja.  
  
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
          <Bitrate>1800</Bitrate>  
          <Width>640</Width>  
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
          <MaxBitrate>1800</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
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
          "Bitrate": 1800,  
          "MaxBitrate": 1800,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
