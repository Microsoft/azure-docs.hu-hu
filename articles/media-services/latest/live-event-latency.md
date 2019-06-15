---
title: Az Azure Media Servicesben videókhoz késés |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt a videókhoz késés, és bemutatja, hogyan állíthatja be a közel valós idejű.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702784"
---
# <a name="live-event-latency-in-media-services"></a>A Media Services élő esemény késés

Ez a cikk bemutatja, hogyan lehet beállítani a közel valós idejű a egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents). Emellett ismerteti a tipikus eredményeket közel valós idejű beállításainak használatával által is látható. Az eredmények függ a CDN és a hálózati késést.

Az új használandó **LowLatency** szolgáltatást, állítsa be a **StreamOptionsFlag** való **LowLatency** a a **videókhoz**. Létrehozásakor [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) HLS lejátszása, állítsa [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) 1-re. A stream működik-e, ha a [Azure Media Player](https://ampdemo.azureedge.net/) (AMP bemutató oldalon), és állítsa be a lejátszási beállítások használatához a "alacsony késés a heurisztika profil".

> [!NOTE]
> Jelenleg az Azure Media Player LowLatency HeuristicProfile lejátszásának MPEG-DASH protokollal, CSF vagy CMAF formátumban lévő adatfolyamok célja (például `format=mdp-time-csf` vagy `format=mdp-time-cmaf`). 

A következő .NET példa bemutatja, hogyan állíthatja be **LowLatency** a a **videókhoz**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Tekintse meg a teljes példát: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Élő események késés

Az alábbi táblázatok a Media Services szolgáltatásban a hozzájárulás hírcsatorna eléri a szolgáltatást, hogy amikor egy megjelenítőt látja a lejátszó a lejátszás ameddig késés (Ha engedélyezve van a LowLatency jelző) jellemző eredmény megjelenítése. Közel valós idejű optimális használatához meg kell finomhangolása lefelé 1 másodperc "Csoportot a képek" (Képcsoporttal) hossza a kódoló beállításai. Magasabb Képcsoporttal hossza használatakor, sávszélesség-használat minimalizálása érdekében, és csökkentheti a sávszélességű azonos sebessége alapján. Különösen hasznos a videókat, amelyekhez kevesebb mozgásban lévő adatoknak egyaránt.

### <a name="pass-through"></a>Továbbítás 

||2S Képcsoporttal kis késés, engedélyezve van|1s Képcsoporttal kis késés, engedélyezve van|
|---|---|---|
|VONAL-és a|10 egység|8s|
|A natív iOS-lejátszó HLS|14s|10 egység|

### <a name="live-encoding"></a>Live Encoding

||2S Képcsoporttal kis késés, engedélyezve van|1s Képcsoporttal kis késés, engedélyezve van|
|---|---|---|
|VONAL-és a|14s|10 egység|
|A natív iOS-lejátszó HLS|18s|13s|

> [!NOTE]
> A végpontok közötti késés eltérőek lehetnek attól függően, helyi hálózati körülmények vagy egy CDN gyorsítótárazási réteg bevezetésével. A pontos konfigurációk kell tesztelni.

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

