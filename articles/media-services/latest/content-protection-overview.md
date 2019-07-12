---
title: A tartalmat, a Media Services dinamikus titkosítás használata – Azure |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt a dinamikus titkosítás segítségével a content protection adott. Azt is ismerteti a streamelési protokollok és a titkosítási típusok.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ee65aa5753d9967022ca91a66433b11761b73a32
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835796"
---
# <a name="content-protection-with-dynamic-encryption"></a>A Content protection, a dinamikus titkosítás segítségével

Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.svg)

&#42;*a dinamikus titkosítás AES-128 "tiszta kulcsot" CBCS és CENC támogatja. További részletekért lásd: a támogatási mátrix [Itt](#streaming-protocols-and-encryption-types).*

Ez a cikk ismerteti az alapelvek és fogalmak ismertetése a Media Services content protection kapcsolódik.

## <a name="main-components-of-a-content-protection-system"></a>A rendszer a content protection fő összetevőinek

Fejezze be a "content protection" rendszer vagy alkalmazás-tervezés, teljes ismertetése az részéről az erőfeszítés hatókörének kell. Az alábbi lista áttekintést ad végrehajtására kellene három részből áll. 

1. Az Azure Media Services-kódot
  
   A [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) minta bemutatja, hogyan multi-DRM-rendszer a Media Services v3 .NET használatával. Azt is bemutatja, hogyan használja a Media Services licenckulcs/kézbesítési szolgáltatás. Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni.
  
   A példa bemutatja, hogyan:

   1. Létrehozhat és konfigurálhat egy [Tartalomszabályzat kulcs](content-key-policy-concept.md). Létrehoz egy **tartalom kulcs házirend** konfigurálása a tartalomkulcs (amelyek biztonságos hozzáférést biztosít az eszközök) a rendszer hogyan továbbítja az ügyfelek közötti.    

      * Adja meg a licenc kézbesítési engedélyezési, adja meg a JWT jogcímszolgáltatói alapján jogosultsági ellenőrzés logikáját.
      * Konfigurálása [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), és/vagy [FairPlay](fairplay-license-overview.md) licenceket. A sablonok segítségével beállíthatja, hogy jogosultságai és engedélyei a használt DRMs mindegyikéhez.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Hozzon létre egy [Streamelési lokátor](streaming-locators-concept.md) megfelelően van konfigurálva, a titkosított adategység továbbításához. 
  
      A **Streamelési lokátor** társítva van egy [Streamelési házirend](streaming-policy-concept.md). A példában a StreamingLocator.StreamingPolicyName állítjuk a "Predefined_MultiDrmCencStreaming" szabályzat. A PlayReady és Widevine titkosítások használatára vonatkoznak, a rendszer továbbítja a kulcsot a lejátszás ügyfél a beállított DRM-licencek alapján. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.
      
      A Streamelési lokátor is társítva van a **tartalom kulcs házirend** , amelyek definiálva lett.
    
   3. A test-token létrehozásához.

      A **GetTokenAsync** módszer bemutatja, hogyan hozzon létre egy tesztet token.
   4. A streamelési URL-cím összeállítását.

      A **GetDASHStreamingUrlAsync** metódus azt mutatja be, hogyan hozhat létre a streamelési URL-CÍMÉT. Ebben az esetben az URL-cím Streamek a **DASH** tartalmat.

2. A Player AES vagy DRM-ügyféllel. Egy videolejátszó alkalmazást, a lejátszó SDK-t (natív vagy böngészőalapú) alapján kell az alábbi követelményeknek:
   * A lejátszó SDK támogatja a szükséges DRM-ügyfelek
   * A lejátszó SDK-t a szükséges streamelési protokollokat támogatja: A Smooth, DASH és/vagy a HLS
   * A lejátszó SDK képesnek kell lennie a JWT jogkivonat passing licenc beszerzési kérés kezelésére
  
     A player használatával hozhat létre a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Használja a [az Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) , adja meg, melyik DRM-technológiával használandó DRM különböző platformokon.

     A tesztelési AES vagy CENC (Widevine és/vagy a PlayReady) titkosított tartalmat használhatja [Azure Media Player](http://aka.ms/azuremediaplayer). Győződjön meg arról, kattintson a "Speciális beállítások", és ellenőrizze a titkosítási beállításokat.

     Ha titkosított FairPlay teszttartalmat szeretne, használja a [ezen teszt player](https://aka.ms/amtest). A Windows Media player támogatja, a PlayReady, Widevine és FairPlay DRMs, valamint az AES-128 titkosítatlan kulcs titkosítás. 
    
     Válassza ki a megfelelő böngészőt másik DRMs teszteléséhez szüksége: Chrome/Opera/Firefox Widevine, a Microsoft Edge/IE11 a PlayReady, a Safari a fairplay rendszerhez macOS rendszeren.

3. A biztonságos jogkivonat-szolgáltatás (STS), amely JSON webes jogkivonat (JWT), a hozzáférési jogkivonatot az háttérbeli erőforrások eléréséhez. Az AMS licenctovábbítási szolgáltatások a háttérerőforrásra is használhatja. Az STS szolgáltatással rendelkezik, az alábbiak megadásához:

   * Kibocsátó és a célközönség (vagy hatókör)
   * A jogcímek, ami függ a content protection az üzleti követelmények
   * A szimmetrikus vagy aszimmetrikus ellenőrzési aláírás-ellenőrzés
   * Kulcsváltás támogatás (ha szükséges)

     Használhat [az STS eszköz](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) tesztelési STS, amely támogatja az ellenőrzőkulcs 3 típusú: szimmetrikus, aszimmetrikus vagy az Azure AD-kulcshoz kapcsolódó kulcsváltás. 

> [!NOTE]
> Azt javasoljuk hogy teljes körű tesztelését az egyes részek (lásd fent), és mielőtt a következő részére. A "content protection" rendszer teszteléséhez használja a fenti listában megadott eszközök.  

## <a name="streaming-protocols-and-encryption-types"></a>Adatfolyam-továbbítási protokollok és a titkosítási típusok

A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg hogy titkosítsa a HTTP Live Streaming (HLS), MPEG DASH és Smooth Streaming formátumban. Minden egyes protokollhoz a következő titkosítási módszereket támogatja:

### <a name="hls"></a>HLS

A HLS-protokoll támogatja a következő tároló formátumok és a titkosítási sémát.

|Tároló-formátum|Titkosítási sémával|Példa URL-címe|
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 – TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 – TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

Fairplay HLS/CMAF (beleértve a HEVC / H.265) a következő eszközökön támogatott:

* iOS 11-es verziójú vagy újabb 
* iPhone 8 vagy újabb
* Az Intel a MacOS high Sierra 7. általános CPU

### <a name="mpeg-dash"></a>MPEG-DASH

Az MPEG-DASH-protokoll támogatja a következő tároló formátumok és a titkosítási sémát.

|Tároló-formátum|Titkosítási sémával|URL-cím-példák
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

A Smooth Streaming protokoll támogatja a következő tároló formátumok és a titkosítási sémát.

|Protocol|Tároló-formátum|Titkosítási sémával|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Böngészők

Közös böngésző támogatja a következő DRM-ügyfelek:

|Browser|Encryption|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-kulcsok vs. DRM

Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. Az a két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalomkulcs az ügyfél a hálózaton keresztül továbbított TLS, hogy a kulcs titkosítva van az átvitel során, de minden további titkosítás nélkül ("a titkosítatlan"). Ennek eredményeképpen a kulcs használatával fejti vissza a tartalom elérhető ügyfél Windows Media player számára, és tekintheti meg a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128-kulcsok titkosítási ideális használati esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók megtekintését a az alkalmazottak által a vállalaton belül terjesztett).

DRM-rendszerek, például a PlayReady, Widevine és FairPlay összes egy további titkosítási szint adja meg a kulcs használatával fejti vissza a tartalom AES-128 titkosítatlan kulcs képest. A tartalomkulcsot a DRM-modul az által védett kulcs van titkosítva, bármely átviteli fájlszintű titkosítás TLS által biztosított további. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

## <a name="dynamic-encryption-and-key-delivery-service"></a>A dinamikus titkosítás és kulcstovábbítást

A Media Services v3, tartalomkulcs Streamelési lokátor társítva (lásd: [ebben a példában](protect-with-aes128.md)). Ha használja a Media Services kulcstovábbítást, hagyhatja, hogy az Ön számára a tartalomkulcs létrehozása az Azure Media Services. Kell létrehoznia a tartalomkulcs saját kezűleg használatakor, saját kulcstovábbítást, vagy ha egy magas rendelkezésre állású forgatókönyv kezel, amelyekben két adatközpontban tartalom ugyanazzal a kulccsal van szükségük.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. Visszafejteni a streamet, a Windows Media player kér a kulcsot a Media Services kulcstovábbítást vagy a kulcstovábbítást megadott. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

A Media Services DRM (PlayReady, Widevine, FairPlay) licencek és az AES-kulcsok kézbesítéséhez a hitelesített ügyfelek kulcskézbesítési szolgáltatást biztosít. A REST API vagy egy Media Services ügyféloldali kódtárának használatával konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

### <a name="custom-key-and-license-acquisition-url"></a>Egyéni kulcs és a licenc licenckérési URL-cím

Ha meg szeretné határozni a más kulcs és a licenc kézbesítési szolgáltatás (nem a Media Services), használja az alábbi sablonok. A sablonok két cserélhető mezőinek vannak-e, hogy a Streamelési szabályzat megosztása számos eszközök eszközönként egy adatfolyam-szabályzat létrehozása helyett. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - sablon az egyéni szolgáltatás, amely a kulcsok végfelhasználói lejátszók URL-címe. Az Azure Media Services számára kibocsátott kulcsok használata esetén nem szükséges. A sablon támogatja a cserélhető jogkivonatok, amelyek a szolgáltatás futásidőben a kérelemre adott értékkel frissíti.  A jelenleg támogatott jogkivonat értékek: {AlternativeMediaId}, amelyet a rendszer felhasználónévre StreamingLocatorId.AlternativeMediaId és {ContentKeyId} értékét, amely helyébe a következő értékkel: a kért kulcs azonosítója.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - sablon az egyéni szolgáltatás, amely a licencek végfelhasználói lejátszók URL-címe. Licencek kiadása az Azure Media Services használata esetén nem szükséges. A sablon támogatja a cserélhető jogkivonatok, amelyek a szolgáltatás futásidőben a kérelemre adott értékkel frissíti. A jelenleg támogatott jogkivonat értékek: {AlternativeMediaId}, amelyet a rendszer felhasználónévre StreamingLocatorId.AlternativeMediaId és {ContentKeyId} értékét, amely helyébe a következő értékkel: a kért kulcs azonosítója. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for FairPlay.  

Példa:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

A `ContentKeyId` -kérelem vonatkozik a kulcs értéke és a `AlternativeMediaId` is használható, ha azt szeretné, a kérelem leképezése egy entitás az oldalon. Ha például a `AlternativeMediaId` segítségével megkeresheti a engedélyeket is használható.

Egyéni használó REST-példák kulcs, és a licenc-licenckérési URL-címek, lásd: [Streamelési házirendek – létrehozása](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Tartalom-hozzáférés vezérlése

Szabályozhatja, hogy ki férhet hozzá a tartalom a content key házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalom fő házirendet kell konfigurálnia. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalom kulcs házirendhez **nyissa meg a** vagy **token** korlátozás. 

A jogkivonattal korlátozott tartalom kulcs szabályzattal a tartalomkulcs van csak ügyfélnek küldött, amely egy érvényes JSON webes jogkivonat (JWT) vagy simple web Tokens (SWT) jeleníti meg a kulcs kérésben. Ez a token biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) kell kiállítaniuk. Használja az Azure Active Directory, az STS szolgáltatással, vagy egy egyéni STS üzembe helyezése. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást adja vissza a kért kulcs licenc az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs/licenc.

A tokennel korlátozott szabályzatokhoz konfigurálásakor adjon meg, hogy az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség, más néven a hatókör, ismerteti a token szándéka, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

Ügyfeleink gyakran használják egyéni STS egyéni jogcímek foglalandó választhat másik ContentKeyPolicyOptions különböző paraméterekkel DRM licenc (egy előfizetési licenc és a egy bérleti licencet) vagy a tartalomkulcs jelölő jogcím tartalmazza a tokent a token engedélyt biztosít a kulcs azonosítója.
 
## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services v3:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|
|Media Services, tárolás titkosítása| AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` hiba, győződjön meg arról, adja meg a Streamelési megfelelő szabályzatot.

Ha hibaüzenet jelenik meg, amely végződhet `_NOT_SPECIFIED_IN_URL`, győződjön meg arról, hogy az URL-címben adja meg a titkosításának formátuma. Például: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Lásd: [Streamelési protokollok és a titkosítási típusok](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

* [Az AES-titkosítással védeni](protect-with-aes128.md)
* [DRM védelme](protect-with-drm.md)
* [A hozzáférés-vezérléssel DRM-mel a content protection rendszer tervezése](design-multi-drm-system-with-access-control.md)
* [Gyakori kérdések](frequently-asked-questions.md)

