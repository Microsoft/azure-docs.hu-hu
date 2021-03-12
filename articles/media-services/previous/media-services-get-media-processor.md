---
title: Adathordozó-feldolgozó létrehozása a .NET-hez készült Azure Media Services SDK-val | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre a médiafájlok kódolására, konvertálására, titkosítására és visszafejtésére szolgáló adathordozó-feldolgozó összetevőt Azure Media Services számára. A kód minták C# nyelven íródtak, és a .NET-hez készült Media Services SDK-t használják.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013346"
---
# <a name="how-to-get-a-media-processor-instance"></a>Útmutató: adathordozó-feldolgozó példány beszerzése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Áttekintés

Media Services a Media Processor olyan összetevő, amely egy adott feldolgozási feladatot kezel, például a kódolást, a konvertálást, a titkosítást vagy a médiatartalom visszafejtését. Általában akkor hozhat létre adathordozó-processzort, ha a tartalom kódolására, titkosítására vagy konvertálására szolgáló feladatot hoz létre.

## <a name="azure-media-processors"></a>Azure Media processors

A következő témakör az adathordozó-processzorok listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Adathordozó-feldolgozó letöltése

A következő módszer azt mutatja be, hogyan szerezhet be egy Media Processor-példányt. A kód példa feltételezi, hogy egy **_context** nevű modul szintű változót használ a kiszolgálói környezetre való hivatkozáshoz a következő témakörben ismertetett módon [: a Kapcsolódás a Media Services programhoz](media-services-use-aad-auth-to-access-ams-api.md)című részben leírtak szerint.

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan szerezhet be egy Media Processor-példányt, ugorjon az adategységek [kódolása](media-services-dotnet-encode-with-media-encoder-standard.md) témakörre, amely bemutatja, hogyan kódolhat egy adategységet a Media Encoder standard használatával.