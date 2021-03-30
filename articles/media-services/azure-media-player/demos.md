---
title: Azure Media Player-bemutatók
description: Ez az oldal a Azure Media Player bemutatóinak hivatkozásait tartalmazza.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82139302"
---
# <a name="azure-media-player-demos"></a>Azure Media Player-bemutatók

Az alábbi lista a Azure Media Player bemutatóinak hivatkozásait tartalmazza. Az összes [Azure Media Player mintát](https://github.com/Azure-Samples/azure-media-player-samples) letöltheti a githubról.

## <a name="demo-listing"></a>Bemutató listája

| Minta neve | Programozás JavaScript használatával | Statikus HTML5-videó elem használatával | Description |
| ------------|----------------------------|-------------------------------------|--------------|
| Alapszintű |
| Forrás beállítása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Nem védett tartalom lejátszása.|
| Funkciók |
| VOD ad-Beszúrás – nagy méretű | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | N/A | Helyezzen be a nagy számú előre középre és post-roll-hirdetéseket egy VOD-eszközbe. |
| Lejátszás sebessége | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| N/A | Lehetővé teszi a nézők számára a videó megtekintését a következő helyen:. |
| AMP flush-felszín | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Engedélyezi az új AMP Skin használatát. **Megjegyzés:** Az AMP flush csak a 2.1.0 + AMP verziókban támogatott |
| Feliratok és feliratok | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | Lejátszás WebVTT feliratokkal.
| Élő CEA 708 feliratok | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | N/A | Lejátszás élő CEA 708 bejövő feliratokkal, a feliratokkal balra igazított feliratok. |
| Folyamatos tartalék továbbítás | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Az adaptív lejátszás alapszintű beállítása, ha a folyamatos átvitel nem támogatott a platformon. |
| Progresszív videó MP4 | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | Progresszív hang MP4 lejátszása. |
| Progresszív hang MP3 | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Progresszív hang MP3 lejátszása. |
| DD + | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | N/A | Tartalom lejátszása a DD + hangal. |
| Beállítások |
| Heurisztikus profil | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | A heurisztikus profil módosítása |
| Honosítás | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Honosítás beállítása |
| Hangsávok menü | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Az alapértelmezett felszín hangsávok menüjének megjelenítésére szolgáló beállítások. |
| Billentyűparancsok | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | Ez a minta bemutatja, hogyan konfigurálhatja, hogy mely gyorsbillentyűk engedélyezettek a lejátszóban |
| Események, naplózás és diagnosztika |
| Események regisztrálása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | N/A | Lejátszás az Event Listeners szolgáltatással. |
| Naplózás | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | A részletes naplózás bekapcsolása a konzolra. |
| Diagnosztika | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | N/A | Diagnosztikai adatbázis beolvasása. Ez a minta csak bizonyos technológiákon működik. |
| AES |
| AES nincs token | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Nem token nélküli AES-tartalom lejátszása. |
| AES-token | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | AES-tartalom lejátszása tokenrel. |
| AES HLS-proxy szimulálása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | A tokenrel rendelkező AES-tartalom lejátszása, amely egy proxyt mutat a HLS, így a token iOS-eszközökkel használható. |
| AES token Force Flash | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | AES-tartalom lejátszása tokenrel, a Flash technológia kikényszerítése. |
| DRM |
| Többplatformos DRM a PlayReady, a Widevine és a FairPlay | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | A DRM-tartalmak nem rendelkeznek tokenrel, a PlayReady, a Widevine és a FairPlay fejlécekkel. |
| PlayReady nincs token | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Token nélküli PlayReady-tartalom lejátszása. |
| PlayReady nincs jogkivonat-kényszerítési Silverlight | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | PlayReady-tartalom lejátszása token nélkül, a Silverlight technológia kényszerítése. |
| PlayReady token | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | PlayReady-tartalom lejátszása tokenrel. |
| PlayReady jogkivonat-kényszerítési Silverlight | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | PlayReady-tartalom lejátszása tokenrel, a Silverlight technológia kényszerítése. |
| Protokoll és technológia |
| TechOrder módosítása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | A technikai sorrend módosítása |
| Az MPEG-DASH csak a UrlRewriter-ben kényszeríthető | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | A nem védett tartalmak lejátszása csak a kötőjel protokoll használatával. |
| MPEG-DASH kizárása a UrlRewriter | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | A nem védett tartalmak lejátszása csak a Smooth és a HLS protokollok használatával. |
| Több kézbesítési házirend | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Statikus](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | A forrás beállítása több kézbesítési házirenddel rendelkező tartalommal Azure Media Services |
| Programozott módon kiválasztása |
| Szöveg nyomon követésének kiválasztása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | N/A | Válasszon egy WebVTT a követés listából. |
| Bitráta kiválasztása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | N/A | A bitráták listájából válassza ki a bitrátát. Ez a minta csak bizonyos technológiákon működik. |
| Hangstream kiválasztása | [Dinamikus](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | N/A | Egy hangstream kiválasztása az elérhető hangstreamek listájából. Ez a minta csak bizonyos technológiákon működik. |

## <a name="next-steps"></a>Következő lépések

<!---Some context for the following links goes here--->
- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)