---
title: Media Services v2 – v3 áttelepítési útmutató az élő közvetítéshez
description: Ez a cikk élő közvetítési forgatókönyv-alapú útmutatást nyújt, amely segítséget nyújt az Azure Media Services v2-ről a v3-re való áttelepítéshez.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 02ae0f7e8723e9c4543a4ef681e3a1164b183a7a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927125"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Élő közvetítés forgatókönyv-alapú áttelepítési útmutatója

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

A Azure Portal mostantól támogatja az élő események beállítását és felügyeletét.  Javasoljuk, hogy próbálja ki a v2 és v3 közötti Migrálás tesztelését.

## <a name="test-the-v3-live-event-workflow"></a>A v3 élő esemény munkafolyamatának tesztelése

> [!NOTE]
> A v2-ben létrehozott csatornákat és programokat (amelyek az élő eseményekhez és a v3-as élő kimenetekhez vannak leképezve) a V3 API-val kezelhetők. Az útmutató a meglévő v2-csatorna leállítására alkalmas időpontban, a v3 élő események és az élő kimenetek átváltására használható. Jelenleg nincs támogatás a folyamatosan futó nonstop élő csatornáknak az új v3 élő eseményekre való zökkenőmentes áttelepítéséhez. Ezért a karbantartási állásidőt a célközönség és a vállalat számára legmegfelelőbb kényelmi funkciókkal kell koordinálni.

Tesztelje az élő események Media Services való továbbításának új módját, mielőtt a tartalmat a v2-ről v3-ra helyezné át. Az alábbiakban a v3-funkciók használhatók, és érdemes áttelepíteni az áttelepítést.

- Hozzon létre egy új v3 [élő eseményt](live-events-outputs-concept.md#live-events) a kódoláshoz. Engedélyezheti [az 1080p és 720p kódolású előkészleteket](live-event-types-comparison.md#system-presets).
- Az [élő kimeneti](live-events-outputs-concept.md#live-outputs) entitás használata programok helyett
- [Adatfolyam-lokátorok](streaming-locators-concept.md)létrehozása.
- Vegye figyelembe, hogy szükség van a [HLS és a Dash](dynamic-packaging-overview.md) élő közvetítésre.
- Ha az élő események gyors indítását igényli, Fedezze fel az új [készenléti üzemmód](live-events-outputs-concept.md#standby-mode) funkcióit.
- Ha a folyamatban lévő élő eseményt szeretné átírni, Fedezze fel az új [élő átírási](live-transcription.md) funkciót.
- Ha hosszabb folyamatos átviteli időtartamra van szüksége, hozzon létre nonstop élő eseményeket a v3-ben.
- Az élő események figyeléséhez használja a [Event Grid](monitor-events-portal-how-to.md) .

A konkrét lépésekhez tekintse meg az élő események fogalmait, az oktatóanyagokat és az alábbi útmutatókat.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Élő események – fogalmak, oktatóanyagok és útmutatók

### <a name="concepts"></a>Alapelvek

- [Élő közvetítés a Azure Media Services v3-val](live-streaming-overview.md)
- [Élő események és élő kimenetek Media Services](live-events-outputs-concept.md)
- [Ellenőrzött helyszíni élő adatfolyam-kódolók](recommended-on-premises-live-encoders.md)
- [Igény szerinti videolejátszás létrehozásához használja az időeltolást és az élő kimeneteket](live-event-cloud-dvr.md)
- [Élő átírás (előzetes verzió)](live-transcription.md)
- [Élő eseménytípus összehasonlítása](live-event-types-comparison.md)
- [Élő események állapotai és számlázása](live-event-states-billing.md)
- [Élő esemény alacsony késési beállításai](live-event-latency.md)
- [Media Services élő események hibakódja](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Oktatóanyagok és gyors útmutatók

- [Oktatóanyag: élő közvetítés Media Services](stream-live-tutorial-with-api.md)
- [Azure Media Services élő stream létrehozása a OBS](live-events-obs-quickstart.md)
- [Gyors útmutató: tartalom feltöltése, kódolása és továbbítása a portálon](manage-assets-quickstart.md)
- [Gyors útmutató: Azure Media Services élő stream létrehozása a Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
