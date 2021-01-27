---
title: Tartalomvédelem – forgatókönyv-alapú áttelepítési útmutató
description: Ez a cikk olyan tartalomvédelem-alapú útmutatást nyújt, amely a Azure Media Services v2-ről a v3-re való áttelepítést segíti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: d1e8c8d1f7f710acff75fa1d78aab68df03c954d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898290"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Tartalomvédelem – forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk olyan tartalomvédelem-alapú útmutatást nyújt, amely a Azure Media Services v2-ről a v3-re való áttelepítést segíti.

## <a name="protect-content-in-v3-api"></a>Tartalom biztosítása a V3 API-ban

Használja az új V3 API [többkulcsos](design-multi-drm-system-with-access-control.md) funkcióinak támogatását.

A következő témakörben talál további tudnivalókat: tartalomvédelem – fogalmak, oktatóanyagok és útmutatók.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Tartalomvédelem – fogalmak, oktatóanyagok és útmutatók

### <a name="concepts"></a>Alapelvek

- [A tartalmak Media Services dinamikus titkosítással védhetők](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)
- [Media Services v3 és PlayReady-licenc sablonnal](playready-license-template-overview.md)
- [A Media Services v3 és a Widevine-licenc sablonjának áttekintése](widevine-license-template-overview.md)
- [Apple FairPlay-licencek követelményei és konfigurálása](fairplay-license-overview.md)
- [Folyamatos átviteli házirendek](streaming-policy-concept.md)
- [Tartalmi kulcs házirendjei](content-key-policy-concept.md)

### <a name="tutorials"></a>Oktatóanyagok

[Gyors útmutató: tartalom titkosítása a portál használatával](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Útmutatók

- [Aláírókulcs lekérése a meglévő szabályzatból](get-content-key-policy-dotnet-howto.md)
- [Offline FairPlay streaming iOS-hez Media Services v3](offline-fairplay-for-ios.md)
- [Offline Widevine streaming az Androidhoz Media Services v3](offline-widevine-for-android.md)
- [Offline PlayReady streaming a Windows 10 rendszerhez Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Példák

A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]