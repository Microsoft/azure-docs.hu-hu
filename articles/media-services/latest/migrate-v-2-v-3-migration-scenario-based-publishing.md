---
title: Csomagolási és kézbesítési áttelepítési útmutató
description: Ez a cikk a csomagolással és a szállítással kapcsolatos forgatókönyv-alapú útmutatót nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f28a95ce6ef5e87eed6e5efcd013cc40b102fcba
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721089"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Csomagolási és kézbesítési forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk a csomagolással és a szállítással kapcsolatos forgatókönyv-alapú útmutatást nyújt, amely segítséget nyújt a Azure Media Services v2-ről a v3-re való Migrálás során.

A tartalom a V3 API-ban való közzétételének jelentős változásai. Az új közzétételi modell egyszerűsített, és kevesebb entitást használ a folyamatos átviteli lokátor létrehozásához. Az API-t csak két entitásra, a korábban szükséges négy entitásra csökkentették. A tartalmi kulcs házirendjei és a streaming-lokátorok most már lecserélik a,, és rendszerre vonatkozó igényeket `ContentKeyAuthoriationPolicy` `AssetDeliveyPolicy` `ContentKey` `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Csomagolás és kézbesítés v3-ban

1. Hozzon létre [tartalmi kulcsokra vonatkozó házirendeket](content-key-policy-concept.md).
1. [Adatfolyam-lokátorok](streaming-locators-concept.md)létrehozása.
1. A [folyamatos átviteli útvonalak](create-streaming-locator-build-url.md) beszerzése 
    1. Konfigurálja a [kötőjel](dynamic-packaging-overview.md#mpeg-dash-protocol) -vagy [HLS](dynamic-packaging-overview.md#hls-protocol) -lejátszót.

Az egyes lépésekkel kapcsolatos fogalmakat, oktatóanyagokat és útmutatókat itt találja.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Fogalmak, oktatóanyagok és útmutatók közzététele

### <a name="concepts"></a>Alapelvek

- [Dinamikus csomagolás a Media Services v3-ban](dynamic-packaging-overview.md)
- [Szűrők](filters-concept.md)
- [A jegyzékfájlok szűrése dinamikus csomagoló használatával](filters-dynamic-manifest-overview.md)
- [Streaming-végpontok (forrás) Azure Media Services](streaming-endpoint-concept.md)
- [Tartalom továbbítása CDN-integrációval](scale-streaming-cdn.md)
- [Folyamatos átviteli lokátorok](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Útmutatók

- [Adatfolyam-végpontok kezelése Media Services v3-val](manage-streaming-endpoints-howto.md)
- [CLI-példa: Objektum közzététele](cli-publish-asset.md)
- [Streamelési lokátor létrehozása és URL-címek összeállítása](create-streaming-locator-build-url.md)
- [Feladatok eredményének letöltése](download-results-howto.md)
- [Jel leíró hangsávok](signal-descriptive-audio-howto.md)
- [Az Azure Media Player teljes telepítése](../azure-media-player/azure-media-player-full-setup.md)
- [Az Video.js-lejátszó használata a Azure Media Services](how-to-video-js-player.md)
- [A Azure Media Services használatával](how-to-shaka-player.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]