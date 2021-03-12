---
title: Örökölt összetevők Azure Media Services | Microsoft Docs
description: Ez a témakör Azure Media Services örökölt összetevőket ismerteti.
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008739"
---
# <a name="azure-media-services-legacy-components"></a>Örökölt összetevők Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Az idő múlásával növeljük a Media Service-összetevőket, és kivonják a régi összetevőket. Ebből a cikkből megtudhatja, hogyan telepítheti át alkalmazását egy örökölt összetevőből egy aktuális összetevőbe.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>A régi összetevők és az áttelepítési útmutató nyugdíjazási tervei

A *Windows Azure Media Encoder* (Tamás) és a *Azure Media Encoder* (ame) adathordozó-processzor elavult.

* [Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre](migrate-windows-azure-media-encoder.md)
* [Áttelepítés Azure Media Encoderról Media Encoder Standard](migrate-azure-media-encoder.md)

A következő Media Analytics adathordozó-processzorok elavultak, vagy hamarosan elavulttá válnak:

  
 
| **Médiafeldolgozó neve** | **Nyugdíjazás dátuma** | **További megjegyzések** |
| --- | --- | ---|
| Azure Media Indexer 2 | 2020. január 1. | Ezt a médialejátszót a [Media Services v3 AudioAnalyzerPreset alapszintű üzemmód](../latest/analyzing-video-audio-files-concept.md)váltja fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 2023. március 1. | Ezt a médialejátszót a [Media Services v3 AudioAnalyzerPreset alapszintű üzemmód](../latest/analyzing-video-audio-files-concept.md)váltja fel. További információ: [áttelepítés Azure Media Indexer 2 rendszerről Azure Media Services video Indexerre](migrate-indexer-v1-v2.md). |
| Mozgás észlelése | 2020. június 1.|Jelenleg nincsenek helyettesítési csomagok. |
| Videók összegzése |2020. június 1.|Jelenleg nincsenek helyettesítési csomagok.|
| Videós optikai karakterfelismerés | 2020. június 1. |Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Arcérzékelő | 2020. június 1. | Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 2020. június 1. |Ezt a médialejátszó-processzort [Azure Media Services video Indexer](../video-indexer/index.yml)váltotta fel. Emellett érdemes lehet [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)-t használni. <br/>Lásd: [Azure Media Services v3-es előállítók és video Indexer összehasonlítása](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Következő lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](../latest/migrate-v-2-v-3-migration-introduction.md)
