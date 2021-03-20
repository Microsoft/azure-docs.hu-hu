---
title: Élő videó-elemzések a IoT EDG-terminológiában – Azure
description: Ez a cikk áttekintést nyújt az élő videók elemzéséről IoT Edge terminológiában.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88690612"
---
# <a name="terminology"></a>Terminológia

Ez a cikk áttekintést nyújt a [IoT Edge élő videó-elemzéssel](overview.md)kapcsolatos terminológiáról.

## <a name="azure-media-services"></a>Azure Media Services

A Azure Media Services egy felhőalapú média-platform, amely lehetővé teszi a multimédiás megoldások készítését. További információt a [Azure Media Services](../latest/media-services-overview.md) dokumentációjában talál.

## <a name="asset"></a>Objektum

Az [eszköz](../latest/assets-concept.md) olyan Azure Media Services entitása, amely egy Media Services-fiókhoz csatolt Azure Storage-fiókban található blob-tárolóhoz van leképezve. Az eszközhöz társított összes fájl blobként tárolódik a tárolóban, miközben Media Services tárolja a metaadatokat (például az objektum nevét, leírását, létrehozási idejét).

A IoT Edge élő videó-elemzések létrehozhatnak eszközöket és/vagy hozzáadhatnak adategységeket a meglévő eszközökhöz. Ez lehetővé teszi a folyamatos és az eseményen alapuló videók rögzítését és lejátszását (videó-rögzítéssel a peremhálózati eszközön, a Azure Media Services való rögzítést és a lejátszást a meglévő Azure Media Services streaming-képességek használatával).

## <a name="grpc"></a>gRPC

a [gRPC](https://grpc.io/docs/guides/) egy Language agnosztikus, nagy teljesítményű távoli ELJÁRÁSHÍVÁS (RPC) keretrendszer. Munkamenet-alapú strukturált sémákat használ a [3. protokollos pufferek](https://developers.google.com/protocol-buffers/docs/proto3) használatával a kommunikáció alapjául szolgáló Message Interchange formátumként.

## <a name="media-graph"></a>Médiagrafikon

A [Media Graph](media-graph-concept.md) lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Lehetővé teszi, hogy meghatározza a forrásokból, processzorokból és fogadó csomópontokból álló gráfokat, és így lehetővé teszi élő videós elemzési alkalmazások készítését. A Media Graph fogalma részletesen szerepel a Media Graph koncepciójának oldalán.

## <a name="recording"></a>Felvétel

A biztonsági kamerákhoz készült videó-felügyeleti rendszer kontextusában a videók rögzítése arra a folyamatra utal, amely a videónak a kamerákból való rögzítését és egy fájlban (vagy fájlokban) való tárolását jelenti a mobil-és böngésző-alkalmazásokkal való későbbi megtekintéshez. A videofelvételek a [folyamatos videofelvételek](continuous-video-recording-concept.md) és az [esemény-alapú videofelvételek](event-based-video-recording-concept.md)között kategorizálható. Ezeket részletesebben is ismertetjük a [videó rögzítése](video-recording-concept.md) koncepció lapján.

## <a name="rtsp"></a>RTSP

Az [RTSP](https://tools.ietf.org/html/rfc2326) Real-Time Streaming Protocol-ra hivatkozik. Ez egy alkalmazás szintű protokoll, amellyel szabályozható az adattovábbítás a valós idejű tulajdonságokkal. Az RTSP olyan bővíthető keretrendszert biztosít, amely lehetővé teszi a valós idejű adatok, például a hang és a videó vezérelt, igény szerinti kézbesítését. 

## <a name="streaming"></a>Streamelés

Ha olyan szolgáltatásokból nézte meg a videót, mint a Netflix, a YouTube és más szolgáltatások, akkor tapasztalt videóval is rendelkezik. A lejátszás a "Play" (ha elegendő sávszélességgel rendelkezik) után azonnal megkezdődik, és a videó idővonalán vissza is kérheti. A streaming esetében az a gondolat, hogy csak az éppen figyelt videó részét adja meg, és lehetővé teszi, hogy a megjelenítő elkezdje lejátszani a videót, miközben az adatok átvitele egy kiszolgálóról a lejátszási ügyfélre történik. A Azure Media Services kontextusában a [streaming](https://en.wikipedia.org/wiki/Streaming_media) arra a folyamatra utal, amely az adathordozók [Azure Media Servicesról](../azure-media-player/azure-media-player-overview.md) egy streaming-ügyfélre való továbbítását jelenti (például Azure Media Player). A Azure Media Services segítségével az iparági szabványnak megfelelő HTTP-alapú médiaadatfolyam-továbbítási protokollok [(például http Live Streaming (HLS)](https://developer.apple.com/streaming/) és [MPEG-Dash](https://dashif.org/about/)) használatával továbbíthatja a videókat az ügyfeleknek. A HLS-t a Azure Media Player, valamint a webkiszolgálók, például a [JW Player](https://www.jwplayer.com/), a [hls.js](https://github.com/video-dev/hls.js/), a [VideoJS](https://videojs.com/), a [Google](https://github.com/google/shaka-player), a Exoplayer, illetve a Mobile apps natív módon is elvégezheti az Android [](https://github.com/google/ExoPlayer) és az iOS [AV Foundation](https://developer.apple.com/av-foundation/)használatával. Az Azure Media Player támogatja az MPEG-DASH-t, és [megkeresi az ezen a lapon található ügyfelek listáját](https://dashif.org/clients/). 

Ha a [Media Graph](#media-graph)s használatával videót szeretne felvenni a Azure Media Servicesban lévő egyik eszközre, Media Services streaming-képességgel is elvégezheti a videó streamek továbbítását a HLS és a Dash-ben. Erről további információt [a videolejátszás című cikkben talál](video-playback-concept.md) .

## <a name="vms"></a>VIRTUÁLIS gépek

A [virtuális gépek](https://en.wikipedia.org/wiki/Video_management_system) egy video Management rendszerre hivatkoznak. Ilyen rendszerek a CCTV kamerák konfigurálására és vezérlésére, valamint videók rögzítésére és rögzítésére szolgálnak. Ezek a rendszerek is biztosítanak ügyfélalkalmazások számára a rögzített videó lejátszását

## <a name="next-steps"></a>Következő lépések

[Adathordozó-diagramok](media-graph-concept.md)
