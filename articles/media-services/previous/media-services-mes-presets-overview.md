---
title: Media Encoder Standard (MES) feladatának előbeállításai | Microsoft Docs
description: A témakör áttekintést nyújt a Media Encoder Standard (MES) szolgáltatás által definiált minta-előkészletekről.
author: IngridAtMicrosoft
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011646"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Minta-előállítók a Media Encoder Standardhoz (MES)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder standard** a kódolási feladatok létrehozásakor használható előre definiált rendszerkódolási beállításkészletek készletét határozza meg. Javasoljuk, hogy az "adaptív streaming" készletet használja, ha egy videót kíván kódolni Media Services használatával történő folyamatos átvitelhez. Ha megadja ezt az előre beállított értéket, Media Encoder Standard automatikusan [létrehoz egy bitráta-létrát](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Egyéni beállításkészletek létrehozása mintákból
Media Services teljes mértékben támogatja az előre beállított összes érték testreszabását az adott kódolási igények és követelmények kielégítése érdekében. Ha testre kell szabnia egy kódolási beállításkészletet, az ebben a szakaszban ismertetett Rendszerkészletek egyikével kell kezdenie az egyéni konfiguráció sablonját. Az ezen alapbeállításokban található egyes elemek, valamint az egyes elemek érvényes értékeinek magyarázata a [Media Encoder standard séma](media-services-mes-schema.md) témakörben található.  
  
> [!NOTE]
>  Ha 4k-kódoláshoz készletet használ, szerezze `S3` be a fenntartott egység típusát. További információ: [a kódolás skálázása](./media-services-scale-media-processing-overview.md).  

#### <a name="video-rotation-default-setting-in-presets"></a>A videó elforgatásának alapértelmezett beállítása az alapbeállításokban:
Media Encoder Standard használatakor a videó forgatása alapértelmezés szerint engedélyezve van. Ha a videó rögzített módban van rögzítve egy mobileszközön, akkor ezek a készletek a kódolás előtt elforgatják a tájkép módba.
 
## <a name="available-presets"></a>Elérhető beállításkészletek: 

 A [H264 több bitráta 1080p hang5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) 8 GOP-ra igazított MP4-fájlt állít elő, amely 6000 kbps-ról 400 kbps-ra, valamint AAC 5,1 hangra mutat.  
  
 A [H264 több bitráta 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) 8 GOP-ra igazított MP4-fájlokat hoz létre, amelyek 6000 kbps-ról 400 kbps-re és sztereó AAC-hangra állnak.  
  
 [Az iOS-hez készült több bitráta-16x9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) 8 GOP-ra igazított MP4-fájlt állít elő, amely 8500 kbps-ról 200 kbps-re, valamint sztereó AAC hangra mutat.  
  
 [H264 több bitráta 16X9 SD hang 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) 5 GOP-ra igazított MP4-fájlból áll, amely a 1900 kbps-ról 400 kbps-ra, valamint az AAC 5,1 hangra van állítva.  
  
 [H264 több bitrátát 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) létrehoz egy 5 GOP-ra igazított MP4-fájlt, 1900 kbps-ról 400 kbps-ra és sztereó AAC hangra.  
  
 A [H264 több bitráta 4k](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) -as 5,1-es számú 12 GOP-ra igazított MP4-fájlt állít elő, amely 20000 kbps-ról 1000 kbps-re, valamint AAC 5,1 hangra mutat.  
  
 A [H264 több bitráta 4k](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) 12 GOP-ra igazított MP4-fájlt hoz létre, 20000 kbps-ról 1000 kbps-ra, valamint sztereó AAC hangra.  
  
 [Az iOS-hez készült több bitráta-4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) 8 GOP-ra igazított MP4-fájlt állít elő, amely 8500 kbps-ról 200 kbps-re, valamint sztereó AAC hangra mutat.  
  
 [H264 több bitráta 4X3 SD hang 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) 5 GOP-ra igazított MP4-fájlból áll, amely a 1600 kbps-ról 400 kbps-ra, valamint az AAC 5,1 hangra van állítva.  
  
 [H264 több bitrátát 4X3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) létrehoz egy 5 GOP-ra igazított MP4-fájlt, 1600 kbps-ról 400 kbps-ra és sztereó AAC hangra.  
  
 A [H264 több bitráta 720P hang 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) 6 GOP-ra igazított MP4-fájlból áll, amely 3400 kbps-ról 400 kbps-ra, valamint AAC 5,1 hangra mutat.  
  
 [H264 több bitráta 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 6 GOP-os MP4-fájlból áll, amely 3400 kbps-ról 400 kbps-ra, valamint sztereó AAC hangra van állítva.  
  
 A [H264 Single bitrate 1080P Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) egy MP4-fájlt hoz létre, amely a 6750 kbps és az AAC 5,1 hang.  
  
 A [H264 Single bitráta 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) egyetlen MP4-fájlt hoz létre, amelynek bitrátája 6750 kbps, a sztereó AAC hang pedig.  
  
 A [H264 Single bitrate 4K Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) egy MP4-fájlt hoz létre, amelynek a sávszélessége 18000 kbps, és az AAC 5,1 hang.  
  
 A [H264 Single bitráta 4k](media-services-mes-preset-H264-Single-Bitrate-4K.md) egyetlen MP4-fájlt hoz létre, amelynek bitrátája 18000 kbps és sztereó AAC hang.  
  
 A [H264 Single bitráta 4X3 SD Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) egy MP4-fájlt hoz létre, amelynek a bitrátája 1800 kbps, és az AAC 5,1 hang.  
  
 A [H264 Single bitráta 4X3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) egyetlen MP4-fájlt hoz létre, amelynek bitrátája 1800 kbps, a sztereó AAC hang pedig.  
  
 A [H264 Single bitráta 16X9 SD Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) egy MP4-fájlt hoz létre, amelynek a bitrátája 2200 kbps, és az AAC 5,1 hang.  
  
 A [H264 Single bitráta 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) egyetlen MP4-fájlt hoz létre, amelynek bitrátája 2200 kbps, a sztereó AAC hang pedig.  
  
 A [H264 Single bitráta 720P hang 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) egy MP4-fájlt hoz létre, amelynek a bitrátája 4500 kbps, és az AAC 5,1 hang.  
  
 A [H264 Single bitráta 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) előre definiált egy MP4-fájlt, amelynek bitrátája 2000 kbps és sztereó AAC.  
  
 A [H264 Single bitráta 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) egyetlen MP4-fájlt hoz létre, amelynek bitrátája 4500 kbps, a sztereó AAC hang pedig.  
  
 A [H264 Single bitráta kiváló minőségű SD-je androidos](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) , 500 kbps sebességű, valamint sztereó AAC-HANGGAL rendelkező MP4-fájlt hoz létre.  
  
 A [H264 Single bitráta alacsony minőségű SD-je androidos](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) , 56 kbps-os és sztereó AAC-hanggal rendelkező, egyetlen MP4-fájlt hoz létre.  
  
 Media Services kódolókkal kapcsolatos további információkért lásd: [igény szerinti kódolás Azure Media Services](./media-services-encode-asset.md).
