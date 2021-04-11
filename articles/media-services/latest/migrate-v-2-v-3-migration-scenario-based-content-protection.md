---
title: Tartalomvédelem – áttelepítési útmutató
description: Ez a cikk a Content Protection forgatókönyv-alapú útmutatást nyújtja, amely segítséget nyújt a Azure Media Services v2-ről v3-re való Migrálás során.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490947"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Tartalomvédelem – forgatókönyv-alapú áttelepítési útmutató

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-4.svg)

Ez a cikk részletes információkat és útmutatást nyújt a tartalomvédelem a v2 API-ból az új Azure Media Services V3 API-ra történő áttelepítéséhez.

## <a name="protect-content-in-v3-api"></a>Tartalom biztosítása a V3 API-ban

Használja az új V3 API [többkulcsos](architecture-design-multi-drm-system.md) funkcióinak támogatását.

A cikk végén tekintse meg a tartalomkezelési fogalmakat, oktatóanyagokat és útmutatókat.

> [!NOTE]
> A cikk további része azt ismerteti, hogyan telepítheti át a v2 Content Protectiont a .NET-tel.  Ha más nyelvre vagy metódusra vonatkozó utasításokra vagy mintakódra van szüksége, hozzon létre egy GitHub-problémát ezen a lapon.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>a v2-eszközök, StreamingLocators és tulajdonságok v3-beli láthatósága

A v2 API-ban, a-ben és a-ben a `Assets` `StreamingLocators` streaming- `ContentKeys` tartalom védelemére használták. A V3 API-ra való áttelepítéskor a v2 API-t, `Assets` `StreamingLocators` és `ContentKeys` mind automatikusan elérhetővé válik a V3 API-ban, és a rajtuk lévő összes adattal hozzáférhet.

A v2 entitások bármelyik tulajdonsága azonban nem *frissíthető* a v2-ben létrehozott V3 API-n keresztül.

Ha frissítenie kell, módosítania vagy módosítania kell a v2 entitásokban tárolt tartalmat, frissítse azokat a v2 API-val, vagy hozzon létre új V3 API-entitásokat a Migrálás érdekében.

## <a name="asset-identifier-differences"></a>Az eszköz azonosítójának eltérései

A Migrálás érdekében hozzá kell férnie a v2-eszközök tulajdonságaihoz vagy tartalmi kulcsaihoz.  Fontos tisztában lenni azzal, hogy a v2 API `AssetId` elsődleges azonosító kulcsot használ, de az új V3 API az entitás *Azure Resource Management-nevét* használja elsődleges azonosítóként.  (A v2 `Asset.Name` tulajdonságot nem használják egyedi azonosítóként.) A V3 API-val a v2-eszköz neve most megjelenik `Asset.Description` .

Ha például korábban már volt egy v2-eszköz a ( `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` z) azonosítójával, az azonosító a GUID végén található `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Ez akkor jelenik meg, ha a v2-eszközöket a V3 API-n keresztül listázza.

A v2 API-val létrehozott és közzétett összes eszközhöz a (z) és a (z) és a (z) rendszer is tartozik `ContentKeyPolicy` `ContentKey` a V3 API-ban, az alapértelmezett tartalmi kulcs házirendje helyett `StreamingPolicy` .

További információkért tekintse meg a [tartalmi kulcsokra vonatkozó szabályzat](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) dokumentációját és a [streaming Policy](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) dokumentációját.

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>A Azure Media Services Explorer (AMSE) v2 és a AMSE v3 eszközök használata egymás mellett

A [v2 Azure Media Services Explorer eszközzel](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) és a [v3 Azure Media Services Explorer eszközzel](https://github.com/Azure/Azure-Media-Services-Explorer) összehasonlíthatja a v2 API-k használatával létrehozott és közzétett adategységek egymás melletti oldalát. A tulajdonságok mindegyikének láthatónak kell lennie, de eltérő helyen kell lennie.

## <a name="use-the-net-content-protection-migration-sample"></a>A .NET tartalomvédelem áttelepítési mintájának használata

Megtalálhatja a Media Services ContentProtection alatti [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) az adategység-azonosítók közötti különbségek összehasonlítására szolgáló mintakód.

## <a name="list-the-streaming-locators"></a>A folyamatos átviteli lokátorok listázása

Lekérdezheti a `StreamingLocators` v2 API-ban létrehozott eszközökhöz kapcsolódó eszközöket az [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) lévő új v3 metódus használatával.  Az [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) .net-ügyfél SDK-verziójára is hivatkozik

A metódus eredményei a `ListStreamingLocators` `Name` és a keresőt is biztosítják `StreamingLocatorId` `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>A tartalmi kulcsok megkeresése

A használatával megkeresheti a `ContentKeys` `StreamingLocators` [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) metódust.  

A V3 API tartalomkezelésével kapcsolatos további információkért tekintse meg a [tartalom védelme Media Services dinamikus titkosítással](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept) című cikket.

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>A v2-ContentKeyPolicy módosítása ugyanazon ContentKey megtartásával

Először a v2 SDK használatával törölje a közzétételt (az összes streaming-lokátor eltávolítása) az eszközön. Ezt a következőképpen teheti meg:

1. Törölje a lokátort.
1. A csatolásának megszüntetése `ContentKeyAuthorizationPolicy` .
1. A csatolásának megszüntetése `AssetDeliveryPolicy` .
1. A csatolásának megszüntetése `ContentKey` .
1. Törölje a t `ContentKey` .
1. Hozzon létre egy újt `StreamingLocator` a v3-as verzióban egy v3 használatával `StreamingPolicy` , és írja be `ContentKeyPolicy` az adott tartalmi kulcs azonosítóját és a kulcs értékét.

> [!NOTE]
> A v2-lokátort a V3 API használatával törölheti, de ez nem távolítja el a tartalmi kulcsot vagy a tartalmi kulcs házirendjét, ha azokat a v2 API-ban hozták létre.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Tartalomvédelem – fogalmak, oktatóanyagok és útmutatók

### <a name="concepts"></a>Alapelvek

- [A tartalmak Media Services dinamikus titkosítással védhetők](drm-content-protection-concept.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](architecture-design-multi-drm-system.md)
- [Media Services v3 és PlayReady-licenc sablonnal](drm-playready-license-template-concept.md)
- [A Media Services v3 és a Widevine-licenc sablonjának áttekintése](drm-widevine-license-template-concept.md)
- [Apple FairPlay-licencek követelményei és konfigurálása](drm-fairplay-license-overview.md)
- [Folyamatos átviteli házirendek](stream-streaming-policy-concept.md)
- [Tartalmi kulcs házirendjei](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Oktatóanyagok

[Gyors útmutató: tartalom titkosítása a portál használatával](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Útmutatók

- [Aláírókulcs lekérése a meglévő szabályzatból](drm-get-content-key-policy-dotnet-how-to.md)
- [Offline FairPlay streaming iOS-hez Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Offline Widevine streaming az Androidhoz Media Services v3](drm-offline-widevine-for-android.md)
- [Offline PlayReady streaming a Windows 10 rendszerhez Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Példák

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- A [v2 és v3 kód is összehasonlítható a kódban szereplő mintákkal](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Eszközök

- [v3 Azure Media Services Explorer eszköz](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer eszköz](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
