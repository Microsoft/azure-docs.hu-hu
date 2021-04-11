---
title: Stream Widevine Android offline
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Azure Media Services v3-fiókját a Widevine által védett tartalmak offline folyamatos átviteléhez.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 297520764272e2d6df54fe3a8ad734088163638d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069079"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streaming az Androidhoz Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az online streaming-tartalmak védelme mellett a Media Content előfizetés és a Rental Services olyan letölthető tartalmakat is kínál, amelyek akkor működnek, ha nem csatlakozik az internethez. Előfordulhat, hogy a hálózati kapcsolat megszakadása esetén a tartalmat le kell töltenie a telefonra vagy a Tablet-ra. További forgatókönyvek, amelyekben előfordulhat, hogy le szeretné tölteni a tartalmat:

- Egyes tartalomszolgáltatók nem engedélyezhetik az ország/régió szegélyén túli DRM-licencek kézbesítését. Ha a felhasználó a külföldön való utazás közben szeretné megtekinteni a tartalmat, offline letöltésre van szükség.
- Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége korlátozott. A felhasználók úgy dönthetnek, hogy letöltik a tartalmat, hogy elég nagy felbontásban tudják megtekinteni a megfelelő megjelenítési élményt.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Ez a cikk azt ismerteti, hogyan lehet az Android-eszközökön a Widevine által védett DASH-tartalmak offline módú lejátszását megvalósítani. Az offline DRM lehetővé teszi, hogy előfizetést, bérletet és vásárlási modelleket biztosítson a tartalomhoz, és lehetővé teszi a szolgáltatások ügyfelei számára, hogy az internetről való leválasztáskor könnyedén fogadják a tartalmakat.

Az androidos lejátszó alkalmazások létrehozásához három lehetőség áll rendelkezésre:

> [!div class="checklist"]
> * A ExoPlayer SDK Java API-ját használó játékosok létrehozása
> * A ExoPlayer SDK Xamarin-kötését használó lejátszó létrehozása
> * Lejátszó létrehozása titkosított Media Extension (EME) és Media Source Extension (MSE) használatával a Chrome Mobile Browser v62 vagy újabb verziójában

A cikk a Widevine által védett tartalom offline adatfolyamával kapcsolatos gyakori kérdésekre is választ ad.

> [!NOTE]
> Az offline DRM-t csak akkor számítjuk fel, ha egy licencre vonatkozó kérést készítenek a tartalom letöltésekor. A hibák számlázása nem történik meg.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt offline DRM-t implementál a Widevine Android-eszközökön, először a következőket kell tennie:

- Ismerkedjen meg az online tartalomvédelem Widevine DRM használatával bevezetett fogalmakkal. Ezt részletesen a következő dokumentumok/minták tartalmazzák:
    - [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](architecture-design-multi-drm-system.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](drm-protect-with-drm-tutorial.md)
- Klónozás https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git .

    A Widevine-konfigurációk hozzáadásához módosítania kell a kódot a [DRM-titkosítással a .NET használatával](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  
- Ismerkedjen meg az Androidhoz készült Google ExoPlayer SDK-val, amely az offline Widevine DRM-lejátszást támogató nyílt forráskódú videolejátszó SDK-t támogatja. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Tartalomvédelem konfigurálása a Azure Media Servicesban

A [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódusban a következő szükséges lépések jelennek meg:

1. Annak meghatározása, hogyan legyen engedélyezve a tartalmi kulcs kézbesítése a licenc kézbesítési szolgáltatásában: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine-licenc sablonjának konfigurálása:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions létrehozása:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Offline mód engedélyezése

A Widevine-licencek **Offline** módjának engedélyezéséhez konfigurálnia kell a [Widevine-licenc sablonját](drm-widevine-license-template-concept.md). A **policy_overrides** objektumban állítsa **igaz** értékre a **can_persist** tulajdonságot (az alapértelmezett érték a False), ahogyan az a [ConfigureWidevineLicenseTempateben](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)látható. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Az Android-lejátszó konfigurálása offline lejátszáshoz

Az Android-eszközökhöz készült natív alkalmazások fejlesztésének legegyszerűbb módja, ha a [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer)-t, egy nyílt forráskódú videolejátszó SDK-t használja. A ExoPlayer az Android natív Media Player API-ját jelenleg nem támogató funkciókat támogatja, beleértve az MPEG-DASH és a Microsoft Smooth Streaming kézbesítési protokollokat.

A ExoPlayer 2,6-es és újabb verziója számos olyan osztályt tartalmaz, amelyek támogatják az offline Widevine DRM lejátszását. A OfflineLicenseHelper osztály olyan segédprogram-funkciókat biztosít, amelyek megkönnyítik a DefaultDrmSessionManager használatát az offline licencek letöltéséhez, megújításához és felszabadításához. A "Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/" SDK mappában megadott osztályok támogatják az offline videotartalom letöltését.

Az alábbi osztályok az Androidhoz készült ExoPlayer SDK-ban az offline üzemmódot teszik lehetővé:

- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. Java  
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. Java
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. Java
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. Java 
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/Downloader. Java
- könyvtár/kötőjel/src/Main/Java/com/Google/Android/exoplayer2/Source/Dash/offline/DashDownloader. Java 

Az alkalmazások fejlesztése során a fejlesztőknek hivatkoznia kell a [ExoPlayer fejlesztői útmutatóra](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogra](https://medium.com/google-exoplayer) . A Google nem bocsátott ki teljes körűen dokumentált referenciát a ExoPlayer alkalmazáshoz, amely jelenleg a Widevine-et támogatja, így az információk a fejlesztői útmutatóra és blogra korlátozódnak. 

### <a name="working-with-older-android-devices"></a>A régebbi androidos eszközök használata

Egyes régebbi Android-eszközök esetén a következő **policy_overrides** tulajdonságok értékeit kell megadnia ( [Widevine-licenc sablonban](drm-widevine-license-template-concept.md)definiálva: **rental_duration_seconds**, **playback_duration_seconds** és **license_duration_seconds**. Azt is megteheti, hogy nullára állítja őket, ami végtelen/korlátlan időtartamot jelent.  

Az értékeket úgy kell beállítani, hogy elkerülje az egész túlcsordulási hibát. A probléma részletes ismertetését lásd: https://github.com/google/ExoPlayer/issues/3150 és https://github.com/google/ExoPlayer/issues/3112 . <br/>Ha nem adja meg explicit módon az értékeket, a  **PlaybackDurationRemaining** és a **LicenseDurationRemaining** nagyon nagy érték lesz hozzárendelve (például 9223372036854775807, amely a 64 bites egész szám maximális pozitív értéke). Ennek eredményeképpen a Widevine-licenc lejárt, ezért a visszafejtés nem fog történni. 

Ez a probléma az Android 5,0-es vagy újabb verziójában nem fordul elő, mivel az Android 5,0 az első Android-verzió, amelyet a ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) és a 64 bites platform teljes körű támogatására terveztek, míg az Android 4,4 KitKat eredetileg a ARMv7 és a 32-bites platformok támogatásához lett tervezve, más régebbi Android-verziókhoz hasonlóan.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Androidos lejátszási alkalmazás létrehozása a Xamarin használatával

A ExoPlayer Xamarin-kötéseit az alábbi hivatkozásokkal érheti el:

- [Xamarin-kötések könyvtára a Google ExoPlayer könyvtárához](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-kötések a ExoPlayer-NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Továbbá tekintse meg a következő szálat: [Xamarin-kötés](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-alkalmazások Android rendszerhez

A [Chrome for Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)verziójának megjelenése után a rendszer támogatja az állandó licencet az eme-ben. Az [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) mostantól az Androidhoz készült Chrome-ban is támogatott. Ez lehetővé teszi offline lejátszási alkalmazások létrehozását a Chrome-ban, ha a végfelhasználók a Chrome ezen (vagy újabb) verzióját használja. 

Emellett a Google létrehozta a progresszív Web App-(PWA-) mintát és a nyílt forráskódú IT-t: 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- A [Google által üzemeltetett verzió](https://biograf-155113.appspot.com/ttt/episode-2/) (csak a Chrome v 62-es és újabb verzióiban működik Android-eszközökön)

Ha Android-telefonon frissíti a Mobile Chrome böngészőt a v62 (vagy újabb verzióra), és teszteli a fent üzemeltetett minta alkalmazást, látni fogja, hogy az online streaming és az offline lejátszás is működik-e.

A fenti nyílt forráskódú PWA alkalmazás szerzője Node.js. Ha Ubuntu-kiszolgálón szeretné üzemeltetni a saját verzióját, vegye figyelembe a következő gyakori problémákat, amelyek megakadályozhatják a lejátszást:

1. CORS probléma: a minta alkalmazásban található minta videó a (z https://storage.googleapis.com/biograf-video-files/videos/ ) rendszerben található. A Google CORS állított be a Google Cloud Storage-gyűjtőben üzemeltetett összes tesztelési mintához. Ezek a CORS-fejlécekkel együtt, explicit módon határozzák meg a CORS bejegyzést: `https://biograf-155113.appspot.com` (a tartomány, amelyben a Google a mintát tárolja) megakadályozza a hozzáférést bármely más hely számára. Ha próbálkozik, a következő HTTP-hiba jelenik meg: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Tanúsítványra vonatkozó probléma: a Chrome v 58-től kezdődően a Widevine-hez szükséges, HTTPS-t igényel. Ezért a minta alkalmazást HTTPS-en keresztül kell üzemeltetni egy X509-tanúsítvánnyal. A szokásos tesztelési tanúsítvány a következő követelmények miatt nem működik: be kell szereznie egy tanúsítványt, amely megfelel a következő minimális követelményeknek:
    - A Chrome és a Firefox megköveteli SAN-Subject alternatív név beállítását a tanúsítványban
    - A tanúsítványnak megbízható HITELESÍTÉSSZOLGÁLTATÓval kell rendelkeznie, és az önaláírt fejlesztési tanúsítvány nem működik.
    - A tanúsítványnak rendelkeznie kell egy, a webkiszolgáló vagy az átjáró DNS-nevével egyező KN-névvel.

## <a name="more-information"></a>További információ

További információ: [Widevine a kérdések gyűjteményében](questions-collection.md#widevine-streaming-for-android).

A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.
