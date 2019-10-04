---
title: Widevine-licencek továbbítása a castLabs használatával a Azure Media Services | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Media Services (AMS) egy olyan stream továbbítására, amelyet az AMS dinamikusan titkosít az PlayReady és a Widevine DRMs. A PlayReady-licenc a Media Services PlayReady-licenckiszolgálóról származik, és a Widevine-licencet a castLabs-licenckiszolgáló továbbítja.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 9c61fad333037074f392b019ae61c161673e4008
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016689"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>A castLabs használata a Widevine-licencek közvetítéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használható a Azure Media Services (AMS) egy olyan stream továbbítására, amelyet az AMS dinamikusan titkosít az PlayReady és a Widevine DRMs. A PlayReady-licenc a Media Services PlayReady-licenckiszolgálóról származik, és a Widevine-licencet a **castLabs** -licenckiszolgáló továbbítja.

A CENC (PlayReady és/vagy Widevine) által védett streaming tartalom lejátszásához használhatja a [Azure Media Player](https://aka.ms/azuremediaplayer). Részletekért lásd az [amp](https://amp.azure.net/libs/amp/latest/docs/) -dokumentációt.

A következő ábra egy magas szintű Azure Media Services és castLabs integrációs architektúrát mutat be.

![integrációs](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Tipikus Rendszerbeállítás

* A médiafájlok AMS-ben tárolódnak.
* A castLabs és az AMS egyaránt tárolja a tartalmi kulcsok kulcs-azonosítóit.
* a castLabs és az AMS is beépített jogkivonat-hitelesítést tartalmaz. A következő fejezetek a hitelesítési jogkivonatokat tárgyalják. 
* Amikor egy ügyfél a videó továbbítására kéri, a tartalmat dinamikusan titkosítja **Common encryption** (CENC), és az AMS által Smooth streaming és kötőjelre dinamikusan csomagolja. A HLS Streaming Protocol PlayReady M2TS-beli, alapszintű stream-titkosítást is biztosítunk.
* A rendszer beolvassa a PlayReady-licencet az AMS-licenckiszolgálóról, és a Widevine-licencet a castLabs-licenckiszolgálóról kéri le. 
* Media Player automatikusan eldönti, hogy melyik licencet kell lekérnie az ügyféloldali platform képesség alapján. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Hitelesítési jogkivonat létrehozása a licenc beszerzéséhez

A castLabs és az AMS támogatja a JWT (JSON Web Token) jogkivonat-formátumot, amely a licencek engedélyezésére szolgál. 

### <a name="jwt-token-in-ams"></a>JWT token az AMS-ben

Az alábbi táblázat az AMS JWT tokenjét ismerteti. 

| Kiállító | Kiállítói sztring a kiválasztott biztonságos jogkivonat-szolgáltatásból (STS) |
| --- | --- |
| Célközönség |Célközönség karakterlánca a használt STS-ből |
| Igénylések |Jogcímek készlete |
| NotBefore |A jogkivonat érvényességének kezdete |
| Elévül |A jogkivonat érvényességének vége |
| SigningCredentials |A PlayReady-licenckiszolgáló, a castLabs és az STS között megosztott kulcs lehet szimmetrikus vagy aszimmetrikus kulcs. |

### <a name="jwt-token-in-castlabs"></a>JWT token a castLabs

A következő táblázat a castLabs JWT tokenjét ismerteti. 

| Name (Név) | Leírás |
| --- | --- |
| optData |Egy JSON-karakterlánc, amely az Ön adatait tartalmazza. |
| CRT |Egy olyan JSON-karakterlánc, amely az eszközre, a licencre és a lejátszási jogosultságokra vonatkozó információkat tartalmaz. |
| iat |Az aktuális dátum és idő. |
| JTI |A jogkivonatra vonatkozó egyedi azonosító (minden token csak egyszer használható a castLabs rendszeren). |

## <a name="sample-solution-setup"></a>Példa a megoldás telepítésére

A [minta megoldás](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) két projektből áll:

* Egy olyan konzolos alkalmazás, amellyel a DRM-korlátozásokat beállíthatja egy már betöltött eszközön, mind a PlayReady, mind a Widevine.
* Egy webalkalmazás, amely kihasználja a jogkivonatokat, amelyek az STS nagyon egyszerűsített verziójának tekinthetők.

A konzol alkalmazás használata:

1. Módosítsa az app. config fájlt, és állítsa be az AMS hitelesítő adatait, a castLabs hitelesítő adatait, az STS-konfigurációt és a megosztott kulcsot.
2. Eszköz feltöltése AMS-be.
3. Kérje le az UUID-t a feltöltött objektumból, és változtassa meg a 32-as sort a Program.cs fájlban:
   
      var objIAsset = _context. Eszközök. where (x = > x.Id = = "NB: CID: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. Használjon egy AssetId az eszköz castLabs rendszerbeli elnevezéséhez (a Program.cs-fájlban található 44. sor).
   
   Be kell állítania a **CastLabs**AssetID. egyedi alfanumerikus karakterláncnak kell lennie.
5. Futtassa a programot.

A webalkalmazás (STS) használata:

1. Módosítsa a web. config fájlt a castlabs kereskedelmi AZONOSÍTÓjának, az STS-konfigurációnak és a megosztott kulcsnak a beállításához.
2. Üzembe helyezés az Azure-webhelyeken.
3. Navigáljon a webhelyre.

## <a name="playing-back-a-video"></a>Videó lejátszása

A Common encryption (PlayReady és/vagy Widevine) titkosítással titkosított videók lejátszásához használhatja a [Azure Media Player](https://aka.ms/azuremediaplayer). A konzol alkalmazás futtatásakor a rendszer a tartalmi kulcs AZONOSÍTÓját és a jegyzékfájl URL-címét is megismétli.

1. Nyisson meg egy új fület, és indítsa el az STS-t: http://[yourStsName]. azurewebsites. NET/API/Token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Lépjen [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Illessze be a streaming URL-címet.
4. Kattintson a **Speciális beállítások** jelölőnégyzetre.
5. A **védelem** legördülő menüben válassza a PlayReady és/vagy a Widevine lehetőséget.
6. Illessze be az STS-ből kapott tokent a jogkivonat szövegmezőbe. 
   
   A castLab-kiszolgálónak nincs szüksége a "Bearer =" előtagra a jogkivonat előtt. Ezért távolítsa el a token elküldése előtt.
7. Frissítse a lejátszót.
8. A videónak játszania kell.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

