---
title: Media Services v3-minták
description: Ez a cikk a Media Services v3 által a metódus és az SDK által szervezett minták számára elérhető összes minta listáját tartalmazza.  A minták közé tartozik a .NET, a Node.JS, a Python és a Java, valamint a Poster.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: bfe39020da0be245f47d0c954de7598914d6534d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105051336"
---
# <a name="media-services-v3-samples"></a>Media Services v3-minták

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk a metódus és az SDK által szervezett Media Services számára elérhető összes minta listáját tartalmazza.  A minták közé tartozik a .NET, a Node.JS, a Python és a Java, valamint a Poster.

## <a name="rest-postman-collection"></a>REST Poster-gyűjtemény

A [Rest Poster](https://github.com/Azure-Samples/media-services-v3-rest-postman) -minták tartalmaz egy Poster-környezetet és egy gyűjteményt, amelyet importálhat a Poster-ügyfélbe.

## <a name="samples-by-sdk"></a>Minták SDK szerint

Az egyes lapokon található minták leírását és hivatkozásait itt találja.

## <a name="net"></a>[.NET](#tab/net/)

| Mappa | Leírás |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|A feladatok beküldése egy beépített beállításkészlet és egy HTTP URL-cím használatával, a folyamatos átvitel kimeneti eszközének közzététele, valamint az ellenőrzés eredményeinek letöltése.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Feladatok elküldése egyéni H. 264 kódolási beállításkészlettel és HTTP URL-cím beírásával, kimeneti eszköz közzététele a folyamatos átvitelhez, valamint az ellenőrzés eredményeinek letöltése.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Feladatok elküldése egyéni HEVC-kódolással és egy HTTP URL-cím bemenettel, kimeneti eszköz közzététele a folyamatos átvitelhez, valamint az ellenőrzés eredményeinek letöltése.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Feladatok elküldése a JobInputSequence használatával a két vagy több olyan eszköz összefűzéséhez, amely az indítási vagy a befejezési időponttal együtt lehet levágva. Az eredményül kapott kódolt fájl egyetlen videó, amelyben az összes eszköz össze van fűzve.  A minta a kimeneti adategységet is közzéteszi a folyamatos átvitelhez és a letöltési eredmények ellenőrzéséhez.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Feladatok elküldése egy miniatűr sprite-val rendelkező egyéni beállításkészlettel és egy HTTP URL-cím beírásával, kimeneti eszköz közzététele a streaming számára, valamint az ellenőrzés eredményeinek letöltése.|
| [Élő/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Teljes archívumot tartalmazó LiveEvent létrehozása akár 25 óráig, valamint szűrő az objektumon 5 perces DVR-ablak használatával. Hogyan lehet szűrőt használni a folyamatos átviteli lokátor létrehozásához.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Video Analyzer-átalakító létrehozása, videofájl feltöltése egy bemeneti adategységbe, a feladatok elküldése az átalakítással és az eredmények letöltése ellenőrzés céljából.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Hogyan hozhat létre egy hangelemző átalakítót, tölthet fel egy médiafájlt egy bemeneti eszközre, elküldheti a feladatot az átalakítással, és letöltheti az eredményeket az ellenőrzéshez.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Beépített AdaptiveStreaming rendelkező átalakító létrehozása, feladatok elküldése, ContentKeyPolicy létrehozása titkos kulccsal, a ContentKeyPolicy hozzárendelése a StreamingLocator-hez, token beszerzése és URL-cím nyomtatása Azure Media Playerban való lejátszáshoz. Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat az AES-128-mel, és a Azure Media Player a tokent használja a visszafejtéshez.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Beépített AdaptiveStreaming készlettel rendelkező átalakítás létrehozása, feladatok elküldése, Widevine-konfigurációval rendelkező ContentKeyPolicy létrehozása titkos kulccsal, a ContentKeyPolicy hozzárendelése a StreamingLocator-hez, token beszerzése és URL-cím nyomtatása Widevine-lejátszón való lejátszáshoz. Amikor egy felhasználó Widevine-védelemmel ellátott tartalmat kér, a Player alkalmazás a Media Services licencelési szolgáltatástól kér licencet. Ha a lejátszóalkalmazás hitelesítve van, a Media Services licencelési szolgáltatás kiadja a licencet. A Widevine-licenc tartalmazza azt a visszafejtési kulcsot, amelyet az ügyfél a tartalom visszafejtésére és továbbítására használhat.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Beépített AdaptiveStreaming készlettel rendelkező átalakítás létrehozása, feladatok elküldése, PlayReady-konfigurációval rendelkező ContentKeyPolicy létrehozása titkos kulccsal, a ContentKeyPolicy hozzárendelése a StreamingLocator-hez, token beszerzése és URL-cím nyomtatása egy Azure Media Player. Amikor egy felhasználó PlayReady-védelemmel ellátott tartalmat kér, a Player alkalmazás a Media Services licencelési szolgáltatástól kér licencet. Ha a lejátszóalkalmazás hitelesítve van, a Media Services licencelési szolgáltatás kiadja a licencet. A PlayReady-licenc tartalmazza azt a visszafejtési kulcsot, amelyet az ügyfél a tartalom visszafejtésére és továbbítására használhat.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|A tartalom dinamikus titkosítása a PlayReady és a Widevine DRM-mel, valamint a tartalom lejátszása anélkül, hogy licencet kérne a licencelési szolgáltatástól. Bemutatja, hogyan hozhat létre átalakítót beépített AdaptiveStreaming-készlettel, hogyan küldhet el egy feladatot, létrehozhat egy ContentKeyPolicy a nyitott korlátozásokkal és a PlayReady/Widevine állandó konfigurációval, társíthatja a ContentKeyPolicy egy StreamingLocator, és kinyomtathatja a lejátszáshoz használandó URL-címet.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Beépített AdaptiveStreaming rendelkező átalakító létrehozása, feladatok elküldése, eszköz-szűrő és fiók-szűrő létrehozása, a szűrők hozzárendelése a streaming-lokátorokhoz és a lejátszási URL-címek nyomtatása.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Beépített AdaptiveStreaming készlettel rendelkező átalakítás létrehozása, feladatok elküldése, kimeneti eszköz közzététele a HLS és a DASH streaming számára.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Az igény szerinti kódolást vagy elemzést használó éles rendszerekre vonatkozó útmutatók és ajánlott eljárások. Az olvasóknak az [Media Services és a VOD-val való magas rendelkezésre állással](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding)kell kezdődnie. A [HighAvailabilityEncodingStreaming](/HighAvailabilityEncodingStreaming/Readme.md) -minta számára külön megoldási fájl van megadva. |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Mappa|Leírás|
|---|---|
|[HelloWorld – ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Eszközök összekapcsolásának és listázásának módja |
|[Élő](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Alapszintű élő közvetítés. **Figyelmeztetés**: Ellenőrizze, hogy az összes erőforrás törlődik-e, és a továbbiakban nem történik meg a számlázás a portálon az élő használatakor.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Helyi fájl vagy kódolás feltöltése a forrás URL-címről, hogyan tölthetők le a Storage SDK a tartalom letöltéséhez, és hogyan lehet streamet használni a lejátszóba. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Kódolás és továbbítás a Widevine és a PlayReady DRM használatával |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| A video-és hangelemző-készletek használata metaadatok és elemzések készítéséhez videó-vagy hangfájlból. |

## <a name="python"></a>[Python](#tab/python)

Jelenleg egy Python-minta van, [amely alapszintű kódolással rendelkezik a Pythonban](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Mappa|Leírás|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Hang elemzése médiafájlban. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|A tartalom dinamikus titkosítása AES-128-mel.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Tartalom dinamikus titkosítása PlayReady DRM-mel.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Tartalom dinamikus titkosítása Widevine DRM-mel.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|A tartalom dinamikus titkosítása FairPlay DRM-mel és a tartalom lejátszása anélkül, hogy licencet kérne a licencelési szolgáltatástól.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|A tartalom dinamikus titkosítása a PlayReady és a Widevine DRM-mel, valamint a tartalom lejátszása anélkül, hogy licencet kérne a licencelési szolgáltatástól.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Tartalom szűrése az eszközök és a fiókok szűrőinek használatával.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Hogyan lehet dinamikusan csomagokat közvetíteni a VOD-tartalmak HLS/DASH-be történő átviteléhez.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Élő esemény létrehozása teljes archiválással akár 25 óráig, valamint egy szűrő az adategységben 5 perces DVR-ablak használatával, valamint egy lokátor létrehozása a folyamatos átvitelhez, valamint a szűrő használata.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Élő esemény létrehozása teljes archiválással akár 25 óráig, valamint egy szűrő az adategységben 5 perces DVR-ablak használatával, valamint egy lokátor létrehozása a folyamatos átvitelhez, valamint a szűrő használata.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Egyéni kódolási átalakítás létrehozása a StandardEncoderPreset-beállítások használatával.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|A feladatok beküldése egy beépített beállításkészlet és egy HTTP URL-cím használatával, a folyamatos átvitel kimeneti eszközének közzététele, valamint az ellenőrzés eredményeinek letöltése.|

---
