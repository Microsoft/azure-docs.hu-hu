---
title: Videolejátszás – Azure
description: Helyőrző
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87042963"
---
# <a name="video-playback"></a>Videó lejátszása 

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás 

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph koncepciója](media-graph-concept.md)

## <a name="overview"></a>Áttekintés  

A [Media graphs](media-graph-concept.md) használatával videót rögzíthet egy Azure Media Services [eszközre](terminology.md#asset). Ebből a dokumentumból megtudhatja, milyen lépésekre van szükség ahhoz, hogy az eszközöket a Azure Media Services meglévő folyamatos átviteli képességeinek használatával lehessen lejátszani.

## <a name="streaming-endpoint"></a>Streamvégpont 

Az adategységeket az iparági szabványoknak megfelelő, HTTP-alapú médiaadatfolyam-továbbítási protokollok, például a HTTP Live Streaming (HLS) és az MPEG-DASH használatával [továbbíthatja](terminology.md#streaming) a Azure Media Services. Az adathordozónak a rögzített tartalomból a streaming formátumokba való átalakítását egy [streaming-végpont](../latest/streaming-endpoint-concept.md)kezeli, amely egy olyan erőforrás, amelyet az Azure Media Service-fiókban kell kiépíteni.

## <a name="streaming-policy"></a>Folyamatos átviteli szabályzat 

A Azure Media Services különböző módszerekkel biztosíthatja a videó streamek védelmét, ahogy azt a [tartalom védelme Media Services dinamikus titkosítással](../latest/content-protection-overview.md) című cikkben tárgyaljuk. A tartalomvédelem magas szintű lehetőségei a következők:

* **In-the-Clear streaming – a** folyamatos átvitel során a rendszer nem alkalmazza a titkosítást.
* **Használjon Advanced Encryption Standard (AES-128)** – és implementálja a kulcsokat a videó visszafejtéséhez csak a hitelesített megjelenítők számára.
* A **digitális Rights Management (DRM) rendszerek használatával** szabályozhatja a videók használatát, módosítását és továbbítását az ezeket a házirendeket érvényesítő eszközökön.

A tartalomvédelem eléréséhez megadhatja és létrehozhatja a [streaming-szabályzatot](../latest/streaming-policy-concept.md) a Media Service-fiókban, és használhatja az összes eszköz folyamatos átviteléhez (feltéve, hogy minden adatfolyam ugyanazokkal a biztonsági követelményekkel rendelkezik). Használhatja az előre definiált házirendeket is (például Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Folyamatos átviteli lokátor  

Miután elindította a streaming-végpontot a Media Service-fiókban, és meghatározta a folyamatos átviteli szabályzatot, folytathatja a rögzített adathordozók adatfolyamként való továbbítását egy HLS vagy DASH protokollon keresztül. A web-Players és a Mobile apps alkalmazásnak szüksége van egy URL-címre, amely a HLS vagy a DASH streamre mutat. Ezt az URL-címet a [streaming Locator](../latest/streaming-locators-concept.md)használatával hozhatja létre. Ahogy az a cikkben is látható, és a streaming- [lokátor létrehozása és az URL-címek összeállítása](../latest/create-streaming-locator-build-url.md) minta jelenik meg, a folyamatos átviteli URL-cím a folyamatos átviteli végpontból, a folyamatos átviteli házirendből és a folyamatos átviteli lokátorból tevődik össze.

## <a name="content-recorded-using-file-sink"></a>A fájl elfogadóval rögzített tartalma  

A [Media Graph-fájlok](media-graph-concept.md#file-sink)elfogadója című témakörben leírtak szerint a Media Graph használatával videókat rögzíthet a peremhálózati eszköz helyi fájlrendszeréhez a Media Graph-ban. A file mosogató [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) -fájlokat hoz létre, és a HTML5 [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) elem használatával is lejátszhatja az ilyen tartalmakat. 

## <a name="next-steps"></a>Következő lépések

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
