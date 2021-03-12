---
title: Fiók konfigurálása a Widevine által védett tartalom offline folyamatos átviteléhez – Azure
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Azure Media Services-fiókját a Widevine által védett tartalmak offline folyamatos átviteléhez.
services: media-services
keywords: DASH, DRM, Widevine offline üzemmód, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: b13feeda162f9babe1958402a694b728312ea4e5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015403"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine-streamelés Androidhoz

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [3\. verzió](../latest/offline-widevine-for-android.md)
> * [2-es verzió](offline-widevine-for-android.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Az online streaming-tartalmak védelme mellett a Media Content előfizetés és a Rental Services olyan letölthető tartalmakat is kínál, amelyek akkor működnek, ha nem csatlakozik az internethez. Előfordulhat, hogy a hálózati kapcsolat megszakadása esetén a tartalmat le kell töltenie a telefonra vagy a Tablet-ra. További forgatókönyvek, amelyekben előfordulhat, hogy le szeretné tölteni a tartalmat:

- Egyes tartalomszolgáltatók nem engedélyezhetik az ország/régió szegélyén túli DRM-licencek kézbesítését. Ha a felhasználó a külföldön való utazás közben szeretné megtekinteni a tartalmat, offline letöltésre van szükség.
- Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége korlátozott. A felhasználók úgy dönthetnek, hogy letöltik a tartalmat, hogy elég nagy felbontásban tudják megtekinteni a megfelelő megjelenítési élményt.

Ez a cikk azt ismerteti, hogyan lehet az Android-eszközökön a Widevine által védett DASH-tartalmak offline módú lejátszását megvalósítani. Az offline DRM lehetővé teszi, hogy előfizetést, bérletet és vásárlási modelleket biztosítson a tartalomhoz, és lehetővé teszi a szolgáltatások ügyfelei számára, hogy az internetről való leválasztáskor könnyedén fogadják a tartalmakat.

Az androidos lejátszó alkalmazások létrehozásához három lehetőség áll rendelkezésre:

> [!div class="checklist"]
> * A ExoPlayer SDK Java API-ját használó játékosok létrehozása
> * A ExoPlayer SDK Xamarin-kötését használó lejátszó létrehozása
> * Lejátszó létrehozása titkosított Media Extension (EME) és Media Source Extension (MSE) használatával a Chrome Mobile Browser v62 vagy újabb verziójában

A cikk a Widevine által védett tartalom offline adatfolyamával kapcsolatos gyakori kérdésekre is választ ad.

## <a name="requirements"></a>Követelmények 

Mielőtt offline DRM-t implementál a Widevine Android-eszközökön, először a következőket kell tennie:

- Ismerkedjen meg az online tartalomvédelem Widevine DRM használatával bevezetett fogalmakkal. Ezt részletesen a következő dokumentumok/minták tartalmazzák:
    - [DRM-licencek vagy AES-kulcsok továbbítása Azure Media Services használatával](media-services-deliver-keys-and-licenses.md)
    - [CENC többplatformos DRM és hozzáférés-vezérlés használatával: Egy referenciaterv és megvalósítás az Azure-on és az Azure Media Services szolgáltatásban](media-services-cenc-with-multidrm-access-control.md)
    - [A PlayReady és/vagy Widevine dinamikus Common Encryption használata a .NET-tel](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [A PlayReady és/vagy Widevine licencek továbbítása a .NET-mel a Azure Media Services használatával](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Ismerkedjen meg az Androidhoz készült Google ExoPlayer SDK-val, amely az offline Widevine DRM-lejátszást támogató nyílt forráskódú videolejátszó SDK-t támogatja. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Tartalomvédelem konfigurációja Azure Media Services

Ha egy eszköz Widevine-védelmét konfigurálja Media Servicesban, létre kell hoznia egy ContentKeyAuthorizationPolicyOption, amely a következő három dolgot adja meg:

1. DRM-rendszerek (Widevine)
2. ContentKeyAuthorizationPolicyRestriction, amely meghatározza, hogy a rendszer hogyan engedélyezte a Content Key Delivery szolgáltatást a licencelési szolgáltatásban (nyitott vagy jogkivonat-hitelesítés)
3. DRM-(Widevine-) licenc sablonja

A Widevine-licencek **Offline** módjának engedélyezéséhez konfigurálnia kell a [Widevine-licenc sablonját](media-services-widevine-license-template-overview.md). A **policy_overrides** objektumban állítsa **igaz** értékre a **can_persist** tulajdonságot (az alapértelmezett érték a False). 

A következő mintakód a .NET-et használja a Widevine-licencek **Offline** üzemmódjának engedélyezéséhez. A kód az [ PlayReady és/vagy Widevine dinamikus Common encryption .net-mintával való használatával](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) történik. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Az Android-lejátszó konfigurálása offline lejátszáshoz

Az Android-eszközökhöz készült natív alkalmazások fejlesztésének legegyszerűbb módja, ha a [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer)-t, egy nyílt forráskódú videolejátszó SDK-t használja. A ExoPlayer az Android natív Media Player API-ját jelenleg nem támogató funkciókat támogatja, beleértve az MPEG-DASH és a Microsoft Smooth Streaming kézbesítési protokollokat.

A ExoPlayer 2,6-es és újabb verziója számos olyan osztályt tartalmaz, amelyek támogatják az offline Widevine DRM lejátszását. A OfflineLicenseHelper osztály olyan segédprogram-funkciókat biztosít, amelyek megkönnyítik a DefaultDrmSessionManager használatát az offline licencek letöltéséhez, megújításához és felszabadításához. A "Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/" SDK mappában megadott osztályok támogatják az offline videotartalom letöltését.

A következő osztályok listája az Androidhoz készült ExoPlayer SDK-ban könnyíti meg a kapcsolat nélküli üzemmódot:

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

Az alkalmazások fejlesztése során a fejlesztőknek hivatkoznia kell a [ExoPlayer fejlesztői útmutatóra](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogra](https://medium.com/google-exoplayer) . A Google nem bocsátott ki teljes körűen dokumentált hivatkozási implementációt vagy mintakód-kódot a ExoPlayer-alkalmazáshoz, amely jelenleg nem támogatja a Widevine-t, ezért az információk a fejlesztői útmutatóra és blogra korlátozódnak. 

### <a name="working-with-older-android-devices"></a>A régebbi androidos eszközök használata

Egyes régebbi Android-eszközök esetén a következő **policy_overrides** tulajdonságok értékeit kell megadnia ( [Widevine-licenc sablonban](media-services-widevine-license-template-overview.md)definiálva: **rental_duration_seconds**, **playback_duration_seconds** és **license_duration_seconds**. Azt is megteheti, hogy nullára állítja őket, ami végtelen/korlátlan időtartamot jelent.  

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

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan biztosíthatok állandó licenceket (kapcsolat nélküli üzemmódban) egyes ügyfelekhez/felhasználókhoz és nem állandó licencekhez (offline – letiltva) mások számára? Duplikálni kell a tartalmat, és külön tartalmi kulcsot kell használnia?

### <a name="answer"></a>Válasz
Nem kell megdupláznia a tartalmat. Egyszerűen csak egyetlen példányban használhatja a tartalmat és egyetlen ContentKeyAuthorizationPolicy, de két különálló ContentKeyAuthorizationPolicyOption is:

1. 1. IContentKeyAuthorizationPolicyOption: állandó licencet használ, és ContentKeyAuthorizationPolicyRestriction 1, amely tartalmaz egy jogcímet, például license_type = "Perzisztens"
2. IContentKeyAuthorizationPolicyOption 2: nem állandó licencet használ, és 2. ContentKeyAuthorizationPolicyRestriction, amely tartalmaz egy jogcímet, például license_type = "nem állandó"

Ily módon, amikor egy licencelési kérelem érkezik az ügyfélalkalmazás alkalmazásból, a licencelési kérelemből nincs különbség. Azonban a különböző végfelhasználók/eszközök esetében az STS-nek üzleti logikával kell rendelkeznie a különböző jogcímeket tartalmazó különböző JWT-tokenek kikibocsátásához (a fenti két license_type egyike). A JWT jogkivonatban szereplő jogcím-érték a licencelési szolgáltatás alapján dönti el, hogy milyen típusú licencet állít ki: állandó vagy nem állandó.

Ez azt jelenti, hogy a biztonságos jogkivonat szolgáltatásnak (STS) rendelkeznie kell az üzleti logikával, valamint az ügyfél/eszköz információval ahhoz, hogy hozzá lehessen adni a megfelelő jogcím-értéket egy tokenhez.

### <a name="question"></a>Kérdés

A Widevine biztonsági szintjeinek a Google [WIDEVINE DRM-architektúra áttekintés](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentációjában három különböző biztonsági szintet határoz meg. A Widevine- [licenc sablonjának Azure Media Services dokumentációjában](./media-services-widevine-license-template-overview.md)azonban öt különböző biztonsági szint látható. Mi a kapcsolat vagy a leképezés két különböző biztonsági szint között?

### <a name="answer"></a>Válasz

A Google [WIDEVINE DRM-architektúrájának áttekintése](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)a következő három biztonsági szintet határozza meg:

1.  1. biztonsági szint: a rendszer az összes tartalom feldolgozását, titkosítását és ellenőrzését a megbízható végrehajtási környezetben (TEE) hajtja végre. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipeken végezhető el.
2.  2. biztonsági szint: titkosítás (de nem a videó feldolgozása) végrehajtása a PÓLÓn belül: a visszafejtett pufferek visszakerülnek az alkalmazás-tartományba, és külön videó hardveren vagy szoftveren keresztül dolgozzák fel azokat. A 2. szinten azonban a titkosítási adatok feldolgozása még csak a PÓLÓn belül történik.
3.  A 3. biztonsági szint nem rendelkezik PÓLÓval az eszközön. A titkosítási információk és a visszafejtett tartalmak a gazdagép operációs rendszerén való védetté tételéhez megfelelő intézkedéseket lehet tenni. A 3. szintű implementációk hardveres titkosítási motort is tartalmazhatnak, de ez csak a teljesítményt javítja, nem pedig a biztonságot.

Ugyanakkor a [Widevine-licenc sablonjának Azure Media Services dokumentációjában](./media-services-widevine-license-template-overview.md)a content_key_specs security_level tulajdonsága a következő öt különböző értékkel rendelkezhet (a lejátszáshoz szükséges ügyfél-megbízhatósági követelmények):

1.  A szoftveres alapú Whitebox-titkosítás szükséges.
2.  A szoftveres kriptográfia és a megzavarodott dekóder szükséges.
3.  A kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres biztonsági másolaton belül kell végrehajtani.
4.  A tartalom titkosítását és dekódolását egy hardveres PÓLÓn belül kell végrehajtani.
5.  A titkosítást, a dekódolást és az adathordozó összes kezelését (tömörítve és tömörítve) egy hardveres biztonsági másolaton belül kell kezelni.

A Google Widevine mindkét biztonsági szintet meghatározza. A különbség a használati szintjén van: architektúra vagy API-szint. Az öt biztonsági szint a Widevine API-ban használatos. A security_levelt tartalmazó content_key_specs objektum deszerializált, és a Widevine globális kézbesítési szolgáltatásnak továbbítja a Azure Media Services Widevine License Service. Az alábbi táblázat a két biztonsági szint közötti leképezést mutatja be.

| **A Widevine architektúrában definiált biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint**: a rendszer az összes tartalom feldolgozását, titkosítását és ellenőrzését a megbízható végrehajtási környezetben (Tee) hajtja végre. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipeken végezhető el.|**security_level = 5**: a titkosítást, a dekódolást és az adathordozó összes kezelését (tömörítve és tömörítve) egy HARDVERes pólón belül kell kezelni.<br/><br/>**security_level = 4**: a tartalom titkosítását és dekódolását egy HARDVERes pólón belül kell végrehajtani.|
**2. biztonsági szint**: titkosítás (de nem a videó feldolgozása) végrehajtása a pólón belül: a visszafejtett pufferek visszakerülnek az alkalmazás-tartományba, és külön videó hardveren vagy szoftveren keresztül dolgozzák fel azokat. A 2. szinten azonban a titkosítási adatok feldolgozása még csak a PÓLÓn belül történik.| **security_level = 3**: a kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres biztonsági másolaton belül kell végrehajtani. |
| **3. biztonsági szint**: nem tartozik póló az eszközön. A titkosítási információk és a visszafejtett tartalmak a gazdagép operációs rendszerén való védetté tételéhez megfelelő intézkedéseket lehet tenni. A 3. szintű implementációk hardveres titkosítási motort is tartalmazhatnak, de ez csak a teljesítményt javítja, nem pedig a biztonságot. | **security_level = 2**: a szoftveres titkosítás és a megzavarodott dekóder szükséges.<br/><br/>**security_level = 1**: a szoftveres Whitebox-alapú titkosítás szükséges.|

### <a name="question"></a>Kérdés

Miért tart ilyen sokáig a tartalom letöltése?

### <a name="answer"></a>Válasz

A letöltési sebesség javítása kétféleképpen lehetséges:

1.  Engedélyezze a CDN-t, hogy a végfelhasználók a tartalom letöltéséhez a forrás/streaming végpont helyett nagyobb eséllyel megkeressék a CDN-t. Ha a felhasználó eléri a folyamatos átviteli végpontot, a rendszer minden egyes HLS-szegmenst vagy DASH-töredéket dinamikusan csomagol és titkosít. Annak ellenére, hogy ez a késés az egyes szegmensek/töredékek esetében ezredmásodperces skálán van, ha egy órás videóval rendelkezik, a felhalmozott késés nagy valószínűséggel hosszabb időt is igénybe vehet.
2.  Adja meg a végfelhasználók számára a minőségi rétegek és hangsávok szelektív letöltését az összes tartalom helyett. Offline módban nem lehet letölteni az összes minőségi réteget. A következő két módon valósítható meg:
    1.  Ügyfél által vezérelt: vagy a Player alkalmazás automatikus kiválasztása vagy a felhasználó kiválasztja a videó minőségi réteget és a hangsávokat a letöltéshez.
    2.  Szolgáltatás által vezérelt: a (z) Azure Media Services dinamikus jegyzékfájl funkciójának használatával létrehozhat egy (globális) szűrőt, amely korlátozza a HLS lejátszási listákat vagy a DASH MPD-t egyetlen videó minőségi rétegre és kiválasztott hangsávokra. Ezután a végfelhasználók számára megjelenített letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="summary"></a>Összefoglalás

Ez a cikk azt ismerteti, hogyan lehet az Android-eszközökön a Widevine által védett DASH-tartalmak offline módú lejátszását megvalósítani.  Emellett a Widevine által védett tartalom offline adatfolyamával kapcsolatos gyakori kérdésekre is válaszolt.
