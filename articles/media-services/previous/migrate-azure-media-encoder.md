---
title: Migrálás Azure Media Encoderról Media Encoder Standardra | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet áttelepíteni a Azure Media Encoderról a Media Encoder Standard Media Processor szolgáltatásba.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f528cea9b475c0158524ad9b46623a78df5761d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016202"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Áttelepítés Azure Media Encoderról Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a régi Azure Media Encoder (AME) adathordozó-processzorról (amely kivonásra kerül) a Media Encoder Standard Media Processor szolgáltatásba. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

A fájlok az AME-val való kódolásakor az ügyfelek általában egy megnevezett előre definiált karakterláncot használnak, például: `H264 Adaptive Bitrate MP4 Set 1080p` . A Migrálás érdekében a kódot frissíteni kell, hogy a **Media Encoder standard** adathordozó-processzort a ame helyett használja, és az egyik egyenértékű [rendszer-előállítson](media-services-mes-presets-overview.md) , például: `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Áttelepítés Media Encoder Standardre

Íme egy tipikus C#-mintakód, amely az örökölt adathordozó-processzort használja. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

A Media Encoder Standardt használó frissített verzió.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Speciális forgatókönyvek 

Ha a saját sémája alapján hozta létre saját kódolási beállításkészletét az AME-hoz, akkor a [Media Encoder standard egyenértékű sémával](media-services-mes-schema.md)rendelkezik. Ha kérdése van, hogy miként képezhető le a régebbi beállítások az új kódolóhoz, forduljon hozzánk a következőn keresztül mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Ismert különbségek 

Media Encoder Standard robusztusabb, megbízhatóbb, jobb teljesítményű, és jobb minőségű kimenetet eredményez, mint az örökölt AME-kódoló. Továbbá: 

* Media Encoder Standard különböző elnevezési konvencióval rendelkező kimeneti fájlokat hoz létre, mint az AME.
* A Media Encoder Standard összetevőket, például a [bemeneti fájl metaadatait](media-services-input-metadata-schema.md) és a [kimeneti fájl (oka) metaadatokat](media-services-output-metadata-schema.md)tartalmazó fájlokat hoz létre.

## <a name="next-steps"></a>Következő lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabás lap](https://azure.microsoft.com/pricing/details/media-services/#encoding)
