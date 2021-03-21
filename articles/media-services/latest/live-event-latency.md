---
title: Kis késleltetésű beállítások LiveEvent Azure Media Services
description: Ez a témakör áttekintést nyújt az alacsony késésű LiveEvent, és bemutatja, hogyan állítható be a kis késleltetés.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f9f4476e932df9a5f4c093968b2e7c4840e7ff39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122901"
---
# <a name="live-event-low-latency-settings"></a>Élő esemény alacsony késési beállításai

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan állíthatja be a kis késleltetést egy [élő esemény](/rest/api/media/liveevents)esetében. Azt is ismerteti, hogy milyen eredményeket láthat a különböző játékosok alacsony késési beállításainak használatakor. Az eredmények a CDN és a hálózati késés alapján változnak.

Az új **LowLatency** funkció használatához állítsa be a **StreamOptionsFlag** a **LowLatency** értékre a **liveevent**. A HLS-lejátszás [LiveOutput](/rest/api/media/liveoutputs) létrehozásakor állítsa a [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) az 1 értékre. Ha a stream működik, használhatja a [Azure Media Player](https://ampdemo.azureedge.net/) (amp bemutató oldalt), és beállíthatja a lejátszási beállításokat az "alacsony késleltetésű heurisztikus profil" használatára.

> [!NOTE]
> A Azure Media Player LowLatency-HeuristicProfile jelenleg az MPEG-DASH protokollban lévő streamek lejátszására lett tervezve, amelyeknek CSF vagy CMAF formátumúnak kell lenniük (például `format=mdp-time-csf` vagy `format=mdp-time-cmaf` ). 

A következő .NET-példa bemutatja, hogyan állíthatja be a **LowLatency** a **liveevent**:

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

Tekintse meg a teljes példát: [élő esemény DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs)-sel.

## <a name="live-events-latency"></a>Élő események késése

Az alábbi táblázatok a késések jellemző eredményeit jelenítik meg (ha a LowLatency jelző engedélyezve van) Media Servicesban, amely attól az időponttól mérve, amikor a hozzájárulási csatorna eléri a szolgáltatást, amikor egy megjelenítő látja a lejátszást a lejátszón. Ha optimálisan szeretné használni az alacsony késést, állítsa be a kódoló beállításait a "Pictures" (GOP) hossz 1 másodpercre. Ha nagyobb GOP-ot használ, minimalizálhatja a sávszélesség-használatot, és az azonos képarányú bitrátát csökkentheti. Ez különösen hasznos a kevesebb mozgást biztosító videóknál.

### <a name="pass-through"></a>Továbbítás 

||2s GOP – alacsony késés engedélyezve|1s GOP – alacsony késés engedélyezve|
|---|---|---|
|**DASH in AMP**|10s|8s|
|**HLS natív iOS-lejátszón**|14s|10s|

### <a name="live-encoding"></a>Live Encoding

||2s GOP – alacsony késés engedélyezve|1s GOP – alacsony késés engedélyezve|
|---|---|---|
|**DASH in AMP**|14s|10s|
|**HLS natív iOS-lejátszón**|18s|13s|

> [!NOTE]
> A végpontok közötti késés a helyi hálózati feltételektől vagy a CDN gyorsítótárazási rétegének bevezetésével függően változhat. Tesztelje a pontos konfigurációkat.

## <a name="next-steps"></a>Következő lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)
