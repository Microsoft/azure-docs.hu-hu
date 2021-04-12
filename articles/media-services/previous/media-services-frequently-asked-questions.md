---
title: Azure Media Services gyakori kérdések
description: Ez a cikk választ ad a Azure Media Servicesekkel kapcsolatos gyakori kérdésekre.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013380"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services v2 – gyakori kérdések

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk az Azure Media Services (AMS) felhasználói Közösség által feltett gyakori kérdéseket tárgyalja.

## <a name="general-ams-faqs"></a>Általános AMS – gyakori kérdések

K: Hogyan továbbítható az Apple iOS-eszközök?

A: add "(Format = m3u8-AAPL)" az URL "/manifest" részének elérési útját, hogy a folyamatos átviteli forrás kiszolgálója visszaadja a HLS-tartalmat az Apple iOS Native-eszközökön (részletekért lásd: [tartalom kézbesítése](media-services-deliver-content-overview.md)),

K: hogyan méretezhető az indexelés?

A: a fenntartott egységek megegyeznek a kódolási és indexelési feladatokhoz. Kövesse a [kódoláshoz fenntartott egységek méretezésének](media-services-scale-media-processing-overview.md)lépéseit. **Vegye figyelembe** , hogy az indexelő teljesítményét a fenntartott egység típusa nem érinti.

K: feltöltött, kódolt és közzétett egy videót. Mi lenne az oka, hogy a videó nem játszható le, amikor megpróbálok adatfolyamot továbbítani?

Válasz: az egyik leggyakoribb ok az, hogy nem rendelkezik a folyamatos átviteli végponttal, amelyről a **futó** állapotot próbálja lejátszani.  

K: használhatok kompozitokat élő streameken?

A: az élő streamek összeválogatása jelenleg nem érhető el Azure Media Servicesban, ezért a számítógépén előre kell összeállítani.

K: használhatok Azure CDN élő közvetítéssel?

A: a Media Services támogatja a Azure CDN integrációját (További információ: [streaming-végpontok kezelése egy Media Services-fiókban](media-services-portal-manage-streaming-endpoints.md)).  Használhatja az élő streamet a CDN használatával. A Azure Media Services Smooth Streaming, HLS és MPEG-DASH kimeneteket biztosít. Ezek a formátumok a HTTP protokoll használatával továbbítják az adatátviteli és a HTTP-gyorsítótárazás előnyeit. Az élő adatfolyamban a videó/hangadatok a töredékekre vannak osztva, és ez az egyes töredékek a CDN-ben kerülnek gyorsítótárazásra. Csak az adatfrissítést kell frissíteni a manifest-adatként. A CDN rendszeresen frissíti a jegyzékfájlokat.

K: támogatja az Azure Media Services a lemezképek tárolását?

A: Ha csak JPEG-vagy PNG-képeket szeretne tárolni, tartsa meg ezeket az Azure Blob Storageban. Ha nem szeretné, hogy a videó-vagy hangeszközeivel ne legyenek elérhetők, ne használja a Media Services fiókjában. Vagy ha lehetséges, hogy a lemezképeket átfedésként kell használni a videó kódolóban. A Media Encoder Standard támogatja a videókon felüli képek átfedését, és azt, hogy a JPEG és a PNG hogyan sorolja fel a támogatott bemeneti formátumokat. További információ: [átfedések létrehozása](media-services-advanced-encoding-with-mes.md#overlay).

K: Hogyan másolhatok eszközöket egy Media Services-fiókból egy másikba?

Válasz: ha objektumokat szeretne másolni egy Media Services-fiókból egy másikba a .NET használatával, használja a [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) bővítményi metódust a [Azure Media Services .net SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/) tárházában. További információkért tekintse meg [ezt a](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fórum szálat.

K: milyen támogatott karakterek vannak a fájlok elnevezésére az AMS használatakor?

A: Media Services a IAssetFile.Name tulajdonság értékét használja a streaming tartalom URL-címeinek létrehozásakor (például: http://{AMSAccount}. Origin. Mediaservices. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Emiatt a százalékos kódolás nem engedélyezett. A **Name (név** ) tulajdonság értéke nem lehet a következő [százalék-kódolásra fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)egyike:! * ' ();: @ &= + $,/?% # [] ". Emellett a fájlnévkiterjesztés csak egy "." lehet.

K: Hogyan csatlakozhatok a REST használatával?

A: az AMS API-hoz való kapcsolódással kapcsolatos információkért tekintse meg [a Azure Media Services API Azure ad-hitelesítéssel való elérését](media-services-use-aad-auth-to-access-ams-api.md)ismertető témakört. 

K: Hogyan lehet elforgatni egy videót a kódolási folyamat során?

A: a [Media Encoder standard](media-services-dotnet-encode-with-media-encoder-standard.md) a rotációt az 90/180/270-as szögek alapján támogatja. Az alapértelmezett viselkedés az "automatikus", amely a bejövő MP4/MOV-fájlban lévő rotációs metaadatok észlelésére és kompenzálására szolgál. Adja meg a következő **források** elemet az [itt](media-services-mes-presets-overview.md)definiált JSON-előállítók egyikéhez:

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
