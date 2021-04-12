---
title: Csomagolási és kézbesítési áttelepítési útmutató
description: Ez a cikk a csomagolással és a szállítással kapcsolatos forgatókönyv-alapú útmutatót nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279017"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Csomagolási és kézbesítési forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk a csomagolással és a szállítással kapcsolatos forgatókönyv-alapú útmutatást nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.

A tartalom a V3 API-ban való közzétételének jelentős változásai. Az új közzétételi modell egyszerűsített, és kevesebb entitást használ a folyamatos átviteli lokátor létrehozásához. Az API-t csak két entitásra, a korábban szükséges négy entitásra csökkentették. A tartalmi kulcs házirendjei és a streaming-lokátorok most már lecserélik a,, és rendszerre vonatkozó igényeket `ContentKeyAuthoriationPolicy` `AssetDeliveyPolicy` `ContentKey` `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Csomagolás és kézbesítés v3-ban

1. Hozzon létre [tartalmi kulcsokra vonatkozó házirendeket](drm-content-key-policy-concept.md).
1. [Adatfolyam-lokátorok](stream-streaming-locators-concept.md)létrehozása.
1. A [folyamatos átviteli útvonalak](create-streaming-locator-build-url.md) beszerzése 
    1. Konfigurálja a [kötőjel](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) -vagy [HLS](encode-dynamic-packaging-concept.md#hls-protocol) -lejátszót.

Az egyes lépésekkel kapcsolatos fogalmakat, oktatóanyagokat és útmutatókat itt találja.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Fogalmak, oktatóanyagok és útmutatók közzététele

### <a name="concepts"></a>Alapelvek

- [Dinamikus csomagolás a Media Services v3-ban](encode-dynamic-packaging-concept.md)
- [Szűrők](filters-concept.md)
- [A jegyzékfájlok szűrése dinamikus csomagoló használatával](filters-dynamic-manifest-concept.md)
- [Streaming-végpontok (forrás) Azure Media Services](stream-streaming-endpoint-concept.md)
- [Tartalom továbbítása CDN-integrációval](stream-scale-streaming-cdn-concept.md)
- [Folyamatos átviteli lokátorok](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Útmutatók

- [Adatfolyam-végpontok kezelése Media Services v3-val](stream-manage-streaming-endpoints-how-to.md)
- [CLI-példa: Objektum közzététele](cli-publish-asset.md)
- [Streamelési lokátor létrehozása és URL-címek összeállítása](create-streaming-locator-build-url.md)
- [Feladatok eredményének letöltése](job-download-results-how-to.md)
- [Jel leíró hangsávok](signal-descriptive-audio-howto.md)
- [Az Azure Media Player teljes telepítése](../azure-media-player/azure-media-player-full-setup.md)
- [Az Video.js-lejátszó használata a Azure Media Services](player-how-to-video-js-player.md)
- [A Azure Media Services használatával](player-shaka-player-how-to.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).
