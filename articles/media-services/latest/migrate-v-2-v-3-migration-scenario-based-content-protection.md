---
title: Tartalomvédelem – áttelepítési útmutató
description: Ez a cikk a Content Protection forgatókönyv-alapú útmutatást nyújtja, amely segítséget nyújt a Azure Media Services v2-ről v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 9141fb025cb2c7976f88d894768972b10ea3a3d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729405"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Tartalomvédelem – forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk részletes információkat és útmutatást nyújt a tartalomvédelem a v2 API-ból az új Azure Media Services V3 API-ra történő áttelepítéséhez.

## <a name="protect-content-in-v3-api"></a>Tartalom biztosítása a V3 API-ban

Használja az új V3 API [többkulcsos](design-multi-drm-system-with-access-control.md) funkcióinak támogatását.

A következő témakörben talál további tudnivalókat: tartalomvédelem – fogalmak, oktatóanyagok és útmutatók.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>A v2-eszközök, a StreamingLocators és a tulajdonságok láthatósága a tartalomvédelmi forgatókönyvekhez készült V3 API-ban

A V3 API-ra való áttelepítés során meg kell találnia, hogy hozzá kell férnie a v2-eszközök egyes tulajdonságaihoz vagy tartalmi kulcsaihoz. Az egyik legfontosabb különbség, hogy a v2 API a **assetid** használja elsődleges azonosító kulcsként, és az új V3 API az entitás Azure Resource Management-nevét használja elsődleges azonosítóként.  A v2 **Asset.name** tulajdonság nem használatos egyedi azonosítóként, ezért a v3-ra való Migrálás során a v2-eszközök nevei most megjelennek az **Asset. Description** mezőben.

Ha például korábban az **"NB: CID: UUID: 8cb39104-122c-496e-9ac5-7f9e2c2547b8"** azonosítójú v2-eszközt használta, akkor a régi v2-eszközök a V3 API-n keresztül történő listázásakor a név lesz a GUID (ebben az esetben **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"**).

Lekérdezheti a v2 API-ban létrehozott eszközökhöz társított **StreamingLocators** az [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) új v3-es metódusának használatával.  Az [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) .net-ügyfél SDK-verziójára is hivatkozik

A **ListStreamingLocators** metódus eredményei megadják a lokátor **nevét** és **StreamingLocatorId** a **StreamingPolicyName** együtt.

A **StreamingLocators** használt **tartalomkulcsok** megkereséséhez hívja meg a [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) metódust.  

A v2 API-val létrehozott és közzétett összes **eszközhöz** tartozik egy [tartalmi kulcsra vonatkozó házirend](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) és egy, a V3 API-ban definiált tartalmi kulcs is, a [folyamatos átviteli házirend](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)alapértelmezett házirendjének használata helyett.

A V3 API tartalomkezelésével kapcsolatos további információkért tekintse meg a [tartalom védelme Media Services dinamikus titkosítással](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview) című cikket.

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>A v2-eszközök és a tartalomvédelem beállításainak listázása a V3 API használatával

A v2 API-ban általában az **eszközök**, a **StreamingLocators** és a **tartalomkulcsok** használatával védik a folyamatos átviteli tartalmat.
A V3 API-ra való áttelepítéskor a v2 API-eszközök, a StreamingLocators és a Tartalomkulcsok is automatikusan elérhetők a V3 API-ban, és a rajtuk lévő összes adatok hozzáférhetnek.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Frissíthetem a v2-tulajdonságokat a V3 API használatával?

Nem, a v2-es entitások összes tulajdonságát nem lehet frissíteni a StreamingLocators, StreamingPolicies, tartalmi kulcs házirendjeivel és a tartalom kulcsaival a v2-ben létrehozott V3 API használatával.
Ha frissítenie kell, módosítania vagy módosítania kell a v2 entitásokban tárolt tartalmakat, frissítenie kell a v2 API-n keresztül, vagy létre kell hoznia egy új V3 API-entitást a továbbításhoz.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Hogyan módosítja egy közzétett v2-eszközhöz használt ContentKeyPolicy, és megtartja ugyanazt a tartalmi kulcsot?

Ebben az esetben először vissza kell vonnia az eszköz közzétételét (az összes streaming-lokátor eltávolítása) az adategységen a v2 SDK-val (törölje a keresőt, válassza le a tartalmi kulcs engedélyezési házirendjét, csatolja az eszköz kézbesítési házirendjét, szüntesse meg a tartalom kulcsát, törölje a tartalmat), majd hozzon létre egy új **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** v3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) és [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept)használatával.

Meg kell adnia a **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** létrehozásakor szükséges adott tartalmi kulcs azonosítóját és kulcs értékét.

Vegye figyelembe, hogy a v2-lokátort a V3 API használatával törölheti, de ez nem távolítja el a tartalmi kulcsot vagy a tartalmi kulcshoz tartozó házirendet, amelyet a v2 API-ban hoztak létre.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>A AMSE v2 és a AMSE v3 párhuzamos használata

Ha a tartalmat a v2-ről a v3-re telepíti át, javasoljuk, hogy telepítse a [v2 Azure Media Services Explorer eszközt](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) a [v3 Azure Media Services Explorer eszközzel](https://github.com/Azure/Azure-Media-Services-Explorer) , hogy összehasonlítsa azokat az adatokat, amelyeket a v2 API-kkal létrehozott és közzétett adategységek egymás mellé mutatnak. A tulajdonságok mindegyikének láthatónak kell lennie, de kissé eltérő helyen kell lennie.  


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

## <a name="tools"></a>Eszközök

- [v3 Azure Media Services Explorer eszköz](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer eszköz](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
