---
title: Hozzáférés-vezérlést biztosító tartalomkezelő rendszer tervezése Azure Media Services használatával | Microsoft Docs
description: Ismerje meg, hogyan lehet licencet beépíteni a Microsoft Smooth Streaming ügyfél-Porting Kit csomagba.
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
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a4f151b597b57b77fa6517c6ea0d586c1106986
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017137"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Hozzáférés-vezérlést használó tartalomkezelő rendszer tervezése Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Áttekintés

Egy több mint felső (OTT) vagy online streaming megoldáshoz tartozó digitális jogkezelési (DRM) alrendszer tervezése és létrehozása összetett feladat. A kezelők/online szolgáltatók jellemzően kiszervezik ezt a feladatot a speciális DRM-szolgáltatók számára. A jelen dokumentum célja, hogy egy végpontok közötti DRM-alrendszer referenciáját és megvalósítását egy OTT-vagy online streaming megoldásban mutassa.

A dokumentum megcélozott olvasói olyan mérnökök, akik az OTT vagy online streaming/osztott megoldások vagy a DRM-alrendszerek iránt érdeklődők számára készült DRM-alrendszerekben működnek. Feltételezhető, hogy az olvasók a piacon legalább egy olyan DRM-technológiával rendelkeznek, mint például a PlayReady, a Widevine, a FairPlay vagy az Adobe Access.

A DRM jelen vitájában a közös titkosítást (CENC) is tartalmazza a multi-DRM használatával. Az online streaming és az OTT iparág egyik fő trendje, hogy a CENC-t több natív DRM-mel használja különböző ügyféloldali platformokon. Ez a trend az előzőtől való elmozdulás, amely egyetlen DRM-t és az ügyféloldali SDK-t használta a különböző ügyféloldali platformokhoz. Ha a CENC-t több natív DRM-mel használja, a PlayReady és a Widevine is titkosítva van a [Common encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikáció alapján.

A többplatformos DRM-mel való CENC előnyei a következők:

* Csökkenti a titkosítási költségeket, mivel a rendszer egyetlen titkosítási folyamatot használ a különböző platformok natív DRMs való célzására.
* Csökkenti a titkosított eszközök kezelésének költségeit, mivel csak egyetlen másolat szükséges a titkosított eszközökről.
* Kiküszöböli a DRM-ügyfél licencelési költségeit, mivel a natív DRM-ügyfél általában ingyenes a natív platformon.

A Microsoft aktívan támogatja a DASH és a CENC, valamint néhány jelentős iparági játékost. Azure Media Services támogatja a kötőjel-és CENC. A legutóbbi bejelentések esetében tekintse meg a következő blogokat:

*  [Announcing Google Widevine license delivery services in Azure Media Services (A Google Widevine-licenctovábbítási szolgáltatás megjelenése az Azure Media Services szolgáltatásban)](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services hozzáadja a Google Widevine-csomagolást a több DRM-alapú stream továbbításához](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>A cikk célja

A cikk célja:

* Adjon meg egy olyan DRM-alrendszer hivatkozási kialakítását, amely a CENC-t használja több DRM-mel.
* Adjon meg egy Azure/Media Services platformon történő referenciát.
* Megismerheti a tervezési és megvalósítási témaköröket.

A cikkben a "multi-DRM" kifejezés a következő termékekre terjed ki:

* Microsoft-PlayReady
* Google Widevine
* Apple FairPlay 

A következő táblázat összefoglalja az egyes DRM által támogatott natív platformot/natív alkalmazást és böngészőket.

| **Ügyfélplatform** | **Natív DRM-támogatás** | **Böngésző/alkalmazás** | **Folyamatos átviteli formátumok** |
| --- | --- | --- | --- |
| **Intelligens TV-k, kezelő STBs, OTT STBs** |PlayReady elsősorban és/vagy Widevine, és/vagy egyéb |Linux, Opera, WebKit, egyéb |Különböző formátumok |
| **Windows 10-es eszközök (Windows PC, Windows Tablet, Windows Phone-telefon, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Univerzális Windows-platform |DASH (a HLS esetében a PlayReady nem támogatott)<br/><br/>DASH, Smooth Streaming (a HLS esetében a PlayReady nem támogatott) |
| **Android-eszközök (telefon, Tablet, TV)** |Widevine |Chrome/EME |KÖTŐJEL, HLS |
| **iOS (iPhone, iPad), OS X-ügyfelek és Apple TV** |FairPlay |Safari 8 +/EME |HLS |

Az egyes DRM-eszközök üzembe helyezésének aktuális állapotát figyelembe véve a szolgáltatás általában két vagy három DRMs kíván megvalósítani, hogy a lehető legjobb megoldást biztosítsa a végpontok összes típusának kezeléséhez.

A szolgáltatás logikájának összetettsége és az ügyféloldali összetettsége közötti kompromisszum a különböző ügyfeleken a felhasználói élmény bizonyos szintjének elérése érdekében.

A kiválasztás elvégzéséhez tartsa szem előtt a következőket:

* A PlayReady natív módon implementálva van minden Windows-eszközön, bizonyos Android-eszközökön, és gyakorlatilag bármilyen platformon elérhető szoftveres SDK-n keresztül érhető el.
* A Widevine natív módon implementálva van minden Android-eszközön, a Chrome-ban és más eszközökön.
* A FairPlay csak iOS-és Mac OS-ügyfeleken, vagy az iTunes-on keresztül érhető el.

Egy tipikus többplatformos DRM esetében két lehetőség áll rendelkezésre:

* PlayReady és Widevine
* PlayReady, Widevine és FairPlay

## <a name="a-reference-design"></a>Hivatkozási terv
Ez a szakasz egy olyan hivatkozási kialakítást mutat be, amely a megvalósításához használt technológiákkal kapcsolatos.

A DRM-alrendszer a következő összetevőket tartalmazza:

* Kulcskezelés
* DRM-csomagolás
* DRM-licenckézbesítés
* Jogosultság-ellenőrzési
* Hitelesítés/engedélyezés
* Lejátszó
* Forrás/Content Delivery Network (CDN)

A következő ábra a DRM-alrendszerek összetevői közötti magas szintű interakciókat mutatja be:

![CENC rendelkező DRM-alrendszer](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

A terv három alapszintű réteget tartalmaz:

* A Back-Office réteg (fekete) nem külsőleg van kitéve.
* A DMZ-réteg (sötétkék) a nyilvánosságot képező összes végpontot tartalmazza.
* A nyilvános internetes réteg (világoskék) a CDN-t és a nyilvános interneten keresztül forgalomban lévő játékosokat tartalmazza.

A DRM-védelem szabályozására is érdemes tartalomkezelő eszköznek kell lennie, függetlenül attól, hogy statikus vagy dinamikus titkosítást használ-e. A DRM-titkosítás bemenetei a következők:

* MBR-videó tartalma
* Tartalmi kulcs
* Licenc-beszerzési URL-címek

A lejátszás ideje alatt a következő magas szintű folyamat érhető el:

* A felhasználó hitelesítése megtörtént.
* A rendszer létrehoz egy engedélyezési jogkivonatot a felhasználó számára.
* A rendszer letölti a DRM-védelemmel ellátott tartalmat (manifest) a lejátszóra.
* A lejátszó licenc-beszerzési kérelmet küld a licenckiszolgálók számára a kulcs-AZONOSÍTÓval és egy engedélyezési jogkivonattal együtt.

A következő szakasz a kulcskezelő tervét tárgyalja.

| **ContentKey** | **Forgatókönyv** |
| --- | --- |
| 1 – 1 |A legegyszerűbb eset. A legfinomabb vezérlőt biztosítja. Ez a megoldás azonban általában a legmagasabb licenc-kézbesítési költségeket eredményezi. Az egyes védett eszközökhöz legalább egy licencelési kérelem szükséges. |
| 1 – a-többhöz |Ugyanazt a tartalmi kulcsot használhatja több eszközhöz is. Például egy logikai csoport összes eszköze, például a műfaj vagy a műfaj (vagy film gén) részhalmaza esetében egyetlen tartalmi kulcsot is használhat. |
| Több-az-1 |Az egyes eszközökhöz több tartalmi kulcs szükséges. <br/><br/>Ha például dinamikus CENC-védelmet szeretne alkalmazni a többplatformos DRM-mel az MPEG-DASH és a dinamikus AES-128 titkosításhoz a HLS-hoz, két különálló tartalmi kulcsra van szüksége. Mindegyik tartalmi kulcshoz saját ContentKeyType szükséges. (A dinamikus CENC-védelemhez használt tartalmi kulcshoz használja a ContentKeyType. CommonEncryption. A dinamikus AES-128 titkosításhoz használt tartalmi kulcshoz használja a következőt: ContentKeyType. EnvelopeEncryption.)<br/><br/>Egy másik példaként a DASH-tartalmak CENC-védelméhez elméletileg egy tartalmi kulcsot használhat a videó stream védelméhez és egy másik tartalmi kulcshoz a hangstream védelméhez. |
| Több-a-többhöz |Az előző két forgatókönyv kombinációja. Az egyazon csoportba tartozó több objektum esetében az egyik tartalmi kulcs van használatban. |

Egy másik fontos szempont az állandó és a nem állandó licencek használata.

Miért fontos ezeket a szempontokat?

Ha nyilvános felhőt használ a licencek kézbesítéséhez, az állandó és a nem állandó licencek közvetlen hatással vannak a licencek kézbesítési díjszabására. A következő két különböző kialakítási eset a következőket szemlélteti:

* Havi előfizetés: használjon állandó licencet és egy-a-többhöz tartalmat kulcs – eszköz hozzárendelést. Például a gyerekek teljes filmjei esetében egyetlen tartalmi kulcsot használunk a titkosításhoz. Ebben az esetben:

    Az összes gyermek mozi/eszköz = 1 esetében kért licencek száma összesen

* Havi előfizetés: használjon nem állandó licencet és 1 – 1 leképezést a tartalmi kulcs és az eszköz között. Ebben az esetben:

    Minden gyermek mozi/eszköz esetében kért licencek száma összesen: [figyelt filmek száma] x [munkamenetek száma]

A két különböző terv nagyon eltérő licencelési mintákat eredményez. A különböző minták a licenc kézbesítésének különböző költségeit eredményezik, ha a licenc kézbesítési szolgáltatását egy nyilvános felhő (például Media Services) szolgáltatja.

## <a name="map-design-to-technology-for-implementation"></a>Tervezés a technológiához a megvalósításhoz
Ezután az általános kialakítás az Azure/Media Services platformon található technológiákra van leképezve azáltal, hogy meghatározza, hogy melyik technológiát kell használni az egyes építőelemek esetében.

A következő táblázat a leképezést mutatja be.

| **Építőelem** | **Technológia** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitás-szolgáltató (IDENTITÁSSZOLGÁLTATÓ)** |Azure Active Directory (Azure AD) |
| **Biztonsági jogkivonat szolgáltatás (STS)** |Azure AD |
| **DRM-védelem munkafolyamata** |Media Services dinamikus védelem |
| **DRM-licenckézbesítés** |* Media Services licenc kézbesítése (PlayReady, Widevine, FairPlay) <br/>* Axinom-licenckiszolgáló <br/>* Egyéni PlayReady-licenckiszolgáló |
| **Származási** |Media Services streaming végpont |
| **Kulcskezelés** |A hivatkozás megvalósításához nem szükséges |
| **Tartalomkezelés** |C# konzolos alkalmazás |

Más szóval a IDENTITÁSSZOLGÁLTATÓ és az STS is az Azure AD-vel együtt használható. A lejátszóhoz a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) használatos. Mind a Media Services, mind a Media Player támogatja a DASH és a CENC-et több DRM-mel.

Az alábbi ábrán az előző technológiai leképezéssel rendelkező általános struktúra és folyamat látható:

![CENC Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

A dinamikus CENC-titkosítás beállításához a tartalomkezelési eszköz a következő bemeneteket használja:

* Tartalom megnyitása
* Kulcs létrehozása/kezelése
* Licenc-beszerzési URL-címek
* Az Azure AD-ből származó információk listája

A Content Management eszköz kimenete:

* A ContentKeyAuthorizationPolicy tartalmazza azt a specifikációt, hogy a licenc-kézbesítés hogyan ellenőrzi a JSON Web Token (JWT) és a DRM-licencek specifikációit.
* A AssetDeliveryPolicy a folyamatos átviteli formátum, a DRM-védelem és a licenc-beszerzési URL-címek specifikációit tartalmazza.

A folyamat futása közben:

* A felhasználó hitelesítése után létrejön egy JWT.
* A JWT lévő jogcímek egyike egy olyan csoportos jogcím, amely tartalmazza a EntitledUserGroup AZONOSÍTÓját. Ez a jogcím a jogosultsági ellenőrzések átadására szolgál.
* A lejátszó letölti az CENC-védelemmel ellátott tartalomhoz tartozó ügyfél-jegyzékfájlt, és azonosítja a következőket:
   * Kulcs azonosítója.
   * A tartalom védett CENC.
   * Licenc-beszerzési URL-címek.
* A lejátszó a licencek beszerzésére irányuló kérést a böngésző/DRM által támogatott böngészők alapján teszi elérhetővé. A licenc-beszerzési kérelemben a kulcs AZONOSÍTÓját és a JWT is elküldi a rendszer. A licenc-kézbesítési szolgáltatás ellenőrzi a JWT és a jogcímeket, mielőtt kiadja a szükséges licencet.

## <a name="implementation"></a>Implementálás
### <a name="implementation-procedures"></a>Megvalósítási eljárások
A megvalósítás a következő lépéseket tartalmazza:

1. Tesztelési eszközök előkészítése. Videó kódolása/becsomagolása többszörös sávszélességű, töredezett MP4-Media Services. Ez az eszköz *nem* DRM-védelemmel ellátott. A DRM elleni védelmet később a dinamikus védelem végzi.

2. Hozzon létre egy kulcs-azonosítót és egy tartalmi kulcsot (opcionálisan egy kulcsfontosságú magot is). Ebben a példányban nincs szükség a kulcskezelő rendszerre, mert néhány tesztelési eszközhöz csak egyetlen kulcs-azonosító és egy tartalmi kulcs szükséges.

3. A Media Services API használatával konfigurálhatja a több DRM-alapú licencelési szolgáltatásokat a tesztelési eszközhöz. Ha a vállalata vagy a vállalata szállítói nem a Media Servicesban lévő licencelési szolgáltatásokat használják, akkor kihagyhatja ezt a lépést. A licencek kézbesítésének konfigurálásakor megadhatja a licencelési URL-címeket a lépésben. A Media Services API-nak meg kell adnia néhány részletes konfigurációt, például az engedélyezési házirend korlátozását és a licenccel kapcsolatos válaszokat a különböző DRM-licencelési szolgáltatásokhoz. Jelenleg a Azure Portal nem biztosítja a szükséges felhasználói felületet ehhez a konfigurációhoz. Az API-szintű információk és a mintakód esetében lásd: [PlayReady és/vagy Widevine dinamikus közös titkosítás használata](media-services-protect-with-playready-widevine.md).

4. A Media Services API-val konfigurálhatja az eszköz kézbesítési házirendjét a tesztelési eszközhöz. Az API-szintű információk és a mintakód esetében lásd: [PlayReady és/vagy Widevine dinamikus közös titkosítás használata](media-services-protect-with-playready-widevine.md).

5. Azure AD-bérlő létrehozása és konfigurálása az Azure-ban.

6. Hozzon létre néhány felhasználói fiókot és csoportot az Azure AD-bérlőben. Hozzon létre legalább egy "jogosult felhasználó" csoportot, és adjon hozzá egy felhasználót a csoporthoz. Az ebben a csoportban lévő felhasználók átadják a jogosultságok ellenőrzését a licencek beszerzése során. A csoportba nem tartozó felhasználók nem tudják átadni a hitelesítési ellenőrzést, és nem tudnak licencet beszerezni. A "jogosult felhasználó" csoport tagsága kötelező csoportos jogcím az Azure AD által kiadott JWT. Ezt a jogcím-követelményt a több DRM-licenc kézbesítési szolgáltatásainak konfigurálásakor kell megadnia a lépésben.

7. Hozzon létre egy ASP.NET MVC-alkalmazást a videolejátszó üzemeltetéséhez. Ez a ASP.NET-alkalmazás az Azure AD-Bérlővel szembeni felhasználói hitelesítéssel van védve. A megfelelő jogcímeket a felhasználói hitelesítés után kapott hozzáférési jogkivonatok tartalmazzák. Ehhez a lépéshez az OpenID Connect API használatát javasoljuk. Telepítse az alábbi NuGet-csomagokat:

   * Install-Package Microsoft. Azure. ActiveDirectory. GraphClient
   * Install-Package Microsoft. Owin. Security. OpenIdConnect
   * Install-Package Microsoft. Owin. Security. cookie-k
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft. IdentityModel. clients. ActiveDirectory

8. Hozzon létre egy lejátszót a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)használatával. A [Azure Media Player PROTECTIONINFO API](https://amp.azure.net/libs/amp/latest/docs/) segítségével meghatározhatja, hogy melyik DRM-technológiát használja a különböző DRM-platformokon.

9. A következő táblázat a tesztelési mátrixot mutatja be.

    | **DRM** | **Böngésző** | **A jogosult felhasználóhoz tartozó eredmény** | **A nem jogosult felhasználóhoz tartozó eredmény** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge vagy Internet Explorer 11 a Windows 10 rendszeren |Sikerül |Sikertelen |
    | **Widevine** |Chrome, Firefox, Opera |Sikerül |Sikertelen |
    | **FairPlay** |Safari macOS rendszeren      |Sikerül |Sikertelen |
    | **AES – 128** |Legmodernebb böngészők  |Sikerül |Sikertelen |

Az Azure AD ASP.NET MVC Player-alkalmazáshoz való beállításával kapcsolatos további információkért lásd: [Azure Media Services OWIN MVC-alapú alkalmazás integrálása Azure Active Directory és a JWT jogcímek alapján történő kézbesítés korlátozása](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

További információ: [JWT-jogkivonat hitelesítése Azure Media Services és dinamikus titkosítással](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

További információ az Azure AD-ről:

* A fejlesztői információk a [Azure Active Directory fejlesztői útmutatójában](../../active-directory/azuread-dev/v1-overview.md)találhatók.
* A rendszergazdai információk az [Azure ad-bérlői címtár felügyeletében](../../active-directory/fundamentals/active-directory-whatis.md)találhatók.

### <a name="some-issues-in-implementation"></a>Néhány probléma a megvalósításban
A megvalósítással kapcsolatos problémák megoldásához használja az alábbi hibaelhárítási információkat.

* A kiállító URL-címnek a "/" értékkel kell végződnie. A célközönségnek a Player Application Client ID azonosítónak kell lennie. Emellett adja hozzá a "/" parancsot a kiállító URL-címének végén.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    A [JWT-dekóderben](http://jwt.calebb.net/)az **AUD** és az **ISS** jelenik meg, ahogy az a JWT is látható:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Engedélyeket adhat az alkalmazáshoz az Azure AD-ben az alkalmazás **Konfigurálás** lapján. Az egyes alkalmazásokhoz (helyi és telepített verziókhoz) engedélyek szükségesek.

    ![Engedélyek](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* A dinamikus CENC-védelem beállításakor használja a megfelelő kiállítót.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    A következő nem működik:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    A GUID az Azure AD-bérlő azonosítója. A GUID a Azure Portal **végpontok** előugró menüjében található.

* Csoporttagság-jogcímek jogosultságának megadása. Győződjön meg arról, hogy a következők szerepelnek az Azure AD Application manifest-fájlban: 

    "groupMembershipClaims": "all" (az alapértelmezett érték null)

* A korlátozási követelmények létrehozásakor állítsa be a megfelelő TokenType.

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Mivel a SWT (ACS) mellett a JWT (Azure AD) támogatását is hozzáadja, az alapértelmezett TokenType a TokenType. JWT. Ha a SWT/ACS-t használja, a tokent a TokenType. SWT értékre kell állítania.

## <a name="additional-topics-for-implementation"></a>További témakörök a megvalósításhoz
Ez a szakasz a tervezéssel és megvalósítással kapcsolatos további témaköröket tárgyalja.

### <a name="http-or-https"></a>HTTP vagy HTTPS?
A ASP.NET MVC Player alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely a HTTPS alatt található.
* JWT az ügyfél és az Azure AD között, amely HTTPS-alapú.
* Az ügyfél által megvásárolt DRM-licenc, amelynek a HTTPS alatt kell lennie, ha a Media Services biztosít a licencek kézbesítését. A PlayReady termék csomagja nem kötelezi a HTTPS protokoll megadását a licencek kézbesítéséhez. Ha a PlayReady-licenckiszolgáló kívül esik Media Serviceson, HTTP vagy HTTPS protokollt is használhat.

A ASP.NET Player alkalmazás a HTTPS protokollt használja ajánlott eljárásként, így Media Player a HTTPS alatt található oldalon. A HTTP azonban a streaming számára ajánlott, ezért érdemes figyelembe vennie a vegyes tartalommal kapcsolatos problémákat.

* A böngésző nem engedélyezi a kevert tartalmat. A beépülő modulok (például a Silverlight és a OSMF beépülő modul) a zökkenőmentes és a kötőjel esetében is lehetővé teszik. A vegyes tartalom biztonsági problémát jelent a rosszindulatú JavaScript beadására irányuló fenyegetés miatt, ami kockázatot jelenthet az ügyfelek számára. A böngészők alapértelmezés szerint blokkolja ezt a funkciót. Az egyetlen módja annak, hogy megkerülje a kiszolgálót (forrás) az összes tartomány (a HTTPS vagy HTTP protokolltól függetlenül) engedélyezésével. Ez valószínűleg nem jó ötlet.
* Kerülje a kevert tartalmat. A Player alkalmazásnak és a Media Playernek egyaránt HTTP vagy HTTPS protokollt kell használnia. Ha vegyes tartalmat játszik, a Silverlight-technológiához vegyes tartalmú figyelmeztetést kell törölni. A flashs Tech vegyes tartalmú figyelmeztetés nélkül kezeli a kevert tartalmakat.
* Ha a folyamatos átviteli végpontot 2014 augusztusa előtt hozták létre, nem támogatja a HTTPS protokollt. Ebben az esetben hozzon létre és használjon egy új streaming-végpontot a HTTPS-hez.

A DRM-védelemmel ellátott tartalmak referenciájának implementációjában az alkalmazás és a folyamatos átvitel is HTTPS-alapú. A megnyitott tartalmakhoz a lejátszónak nincs szüksége hitelesítésre vagy licencre, így HTTP-vagy HTTPS-kapcsolat is használható.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory aláíró kulcsának átgördülése
Az aláíró kulcsok átváltásának fontos szempontja, hogy figyelembe vegye a megvalósítást. Ha figyelmen kívül hagyja, a befejezett rendszerek a legtöbbet hat héten belül abbahagyják a munkát.

Az Azure AD az iparági szabványokat használja az Azure AD-t használó saját maga és az alkalmazások közötti megbízhatósági kapcsolat létrehozására. Az Azure AD egy nyilvános és titkos kulcspárből álló aláíró kulcsot használ. Ha az Azure AD olyan biztonsági jogkivonatot hoz létre, amely a felhasználóval kapcsolatos információkat tartalmaz, azt az Azure AD írja alá egy titkos kulccsal, mielőtt visszaküldi az alkalmazásnak. Annak ellenőrzéséhez, hogy a jogkivonat érvényes-e, és az Azure AD-ből származik, az alkalmazásnak ellenőriznie kell a jogkivonat aláírását. Az alkalmazás az Azure AD által elérhető nyilvános kulcsot használja, amelyet a bérlő összevonási metaadatait tartalmazó dokumentum tartalmaz. Ez a nyilvános kulcs és az azt tartalmazó aláíró kulcs ugyanaz, mint az Azure AD összes bérlője számára.

Az Azure AD-kulcsok átváltásával kapcsolatos további információkért tekintse meg az [Azure ad-ban az aláíró kulcsok átváltásával kapcsolatos fontos információkat](../../active-directory/develop/active-directory-signing-key-rollover.md).

A [nyilvános titkos kulcspár](https://login.microsoftonline.com/common/discovery/keys/)között:

* A titkos kulcsot az Azure AD használja JWT létrehozásához.
* A nyilvános kulcsot egy olyan alkalmazás használja, mint például a DRM-licenc kézbesítési szolgáltatása a Media Services a JWT ellenőrzéséhez.

Biztonsági okokból az Azure AD rendszeresen elforgatja a tanúsítványt (hat hetente). Biztonsági rések esetén a kulcsok átváltására bármikor sor kerülhet. Ezért a Media Services licenc kézbesítési szolgáltatásainak frissíteniük kell az Azure AD-ben használt nyilvános kulcsot, amely elforgatja a kulcspárt. Ellenkező esetben a jogkivonat-hitelesítés Media Services meghiúsul, és nincs licenc kibocsátva.

A szolgáltatás beállításához a TokenRestrictionTemplate. OpenIdConnectDiscoveryDocument a DRM-licenc kézbesítési szolgáltatásainak konfigurálásakor kell beállítania.

Itt látható a JWT folyamat:

* Az Azure AD kiadja a JWT a hitelesített felhasználó aktuális titkos kulcsával.
* Ha egy játékos több DRM-védelemmel ellátott tartalommal rendelkező CENC lát, először megkeresi az Azure AD által kiadott JWT.
* A lejátszó licenc-beszerzési kérelmet küld a JWT a Media Services-ben a licenc kézbesítési szolgáltatásaihoz.
* Media Services a licenc kézbesítési szolgáltatásai az Azure AD-ből származó aktuális/érvényes nyilvános kulcsot használják a JWT ellenőrzéséhez a licencek kiadása előtt.

A DRM-licenc kézbesítési szolgáltatásai mindig az Azure AD-től származó aktuális/érvényes nyilvános kulcsot keresik. Az Azure AD által bemutatott nyilvános kulcs az Azure AD által kiadott JWT ellenőrzéséhez használt kulcs.

Mi a teendő, ha az Azure AD egy JWT hoz létre, de még azelőtt, hogy a játékosok a Media Services a DRM-licenc kézbesítési szolgáltatásait az ellenőrzéshez?

Mivel a kulcsok bármikor átállíthatók, egynél több érvényes nyilvános kulcs mindig elérhető az összevonási metaadatokat tartalmazó dokumentumban. Media Services a licencek kézbesítése a dokumentumban megadott kulcsok bármelyikét használhatja. Mivel előfordulhat, hogy az egyik kulcs hamarosan fel van tekerve, egy másik lehet a pótlása, és így tovább.

### <a name="where-is-the-access-token"></a>Hol található a hozzáférési jogkivonat?
Ha azt tapasztalja, hogy egy webalkalmazás hogyan hív meg egy API-alkalmazást az [alkalmazás identitása alatt az OAuth 2,0 ügyfél hitelesítő adataival](../../active-directory/azuread-dev/web-api.md), a hitelesítési folyamat a következő:

* A felhasználó bejelentkezik az Azure AD-be a webalkalmazásban. További információ: webböngésző [webalkalmazáshoz](../../active-directory/azuread-dev/web-app.md).
* Az Azure AD engedélyezési végpontja átirányítja a felhasználói ügynököt az ügyfélalkalmazás számára egy engedélyezési kóddal. A felhasználói ügynök az engedélyezési kódot az ügyfélalkalmazás átirányítási URI azonosítójának adja vissza.
* A webalkalmazásnak meg kell szereznie egy hozzáférési jogkivonatot, hogy az hitelesíthető legyen a webes API-ban, és beolvassa a kívánt erőforrást. Kérést küld az Azure AD-jogkivonat végpontjának, és megadja a hitelesítő adatokat, az ügyfél-azonosítót és a webes API alkalmazásspecifikus AZONOSÍTÓjának URI azonosítóját. Megadja az engedélyezési kódot, amely igazolja, hogy a felhasználó beleegyezett.
* Az Azure AD hitelesíti az alkalmazást, és egy JWT hozzáférési tokent ad vissza, amely a webes API meghívásához használatos.
* HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT hozzáférési tokent használja, hogy hozzáadja a JWT karakterláncot a "tulajdonos" jelöléssel a webes API-nak küldött kérelem "engedélyezés" fejlécében. A webes API ezt követően ellenőrzi a JWT. Ha az érvényesítés sikeres, akkor a rendszer visszaadja a kívánt erőforrást.

Az alkalmazás-identitás folyamatában a webes API megbízik abban, hogy a webalkalmazás hitelesítette a felhasználót. Emiatt ezt a mintát megbízható alrendszernek nevezzük. Az [engedélyezési folyamat diagramja](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) leírja, hogyan működik az engedélyezési kód – engedélyezés folyamata.

A jogkivonat-korlátozással rendelkező licenc-beszerzések ugyanazt a megbízható alrendszer mintát követik. A Media Services a licenc kézbesítési szolgáltatása a webes API-erőforrás, vagy a "háttérbeli erőforrás", amelyet egy webalkalmazásnak el kell érnie. Hol található a hozzáférési jogkivonat?

A hozzáférési jogkivonatot az Azure AD-ből szerezték be. A sikeres felhasználói hitelesítés után a rendszer egy engedélyezési kódot ad vissza. Ezt követően az engedélyezési kódot az ügyfél-AZONOSÍTÓval és az alkalmazás kulcsával együtt kell használni a hozzáférési jogkivonat cseréjéhez. A hozzáférési jogkivonat egy olyan "mutató" alkalmazás elérésére szolgál, amely a Media Services licenc kézbesítési szolgáltatására mutat vagy azt jelképezi.

A mutató alkalmazás Azure AD-beli regisztrálásához és konfigurálásához hajtsa végre a következő lépéseket:

1. Az Azure AD-bérlőben:

   * Adjon hozzá egy alkalmazást (erőforrás) a bejelentkezési URL-cím https://[resource_name]. azurewebsites. net/. 
   * Adja hozzá az alkalmazás AZONOSÍTÓját a https://[aad_tenant_name]. onmicrosoft. com/[resource_name] URL-címmel.

2. Adjon hozzá egy új kulcsot az erőforrás-alkalmazáshoz.

3. Frissítse az alkalmazás jegyzékfájlját úgy, hogy a groupMembershipClaims tulajdonság értéke "groupMembershipClaims": "all".

4. Adja hozzá az 1. lépésben hozzáadott erőforrás-alkalmazást az Azure AD-alkalmazásban, amely a Player webalkalmazásra mutat, a **más alkalmazásokra vonatkozó engedélyek** szakaszban. A **delegált engedély** területen válassza a **hozzáférés [resource_name]** lehetőséget. Ez a beállítás lehetővé teszi a webalkalmazás számára, hogy olyan hozzáférési jogkivonatokat hozzon létre, amelyek hozzáférnek az erőforrás-alkalmazáshoz. A Visual Studióval és az Azure-webalkalmazással való fejlesztés esetén ezt a webalkalmazás helyi és központilag telepített verziójára is használhatja.

Az Azure AD által kiadott JWT a mutató erőforráshoz való hozzáféréshez használt hozzáférési jogkivonat.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítéssel?
Az előző vita az igény szerinti eszközökre koncentrál. Mi a helyzet az élő közvetítéssel?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja a Media Services élő közvetítésének biztosításához, ha a programhoz társított eszközt a VOD-eszközként kezeli.

A Media Services élő közvetítésének megkezdéséhez létre kell hoznia egy csatornát, majd létre kell hoznia egy programot a csatorna alatt. A program létrehozásához létre kell hoznia egy olyan eszközt, amely tartalmazza a programhoz tartozó élő archívumot. Ha az élő tartalom több DRM-védelemmel ellátott CENC szeretné biztosítani, alkalmazza ugyanazt a telepítést/feldolgozást az objektumra, mintha egy VOD-eszköz lenne a program elindítása előtt.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a teendő a Media Serviceson kívüli licenckiszolgálóra?
Az ügyfelek gyakran a saját adatközpontjában vagy a DRM-szolgáltatók által üzemeltetett licencelési kiszolgálófarm egyikében fektettek be. A Media Services Content Protection használatával hibrid módban is működhet. A tartalmak a Media Servicesban tárolhatók és dinamikusan védhetők, míg a DRM-licenceket a Media Serviceson kívüli kiszolgálók is továbbítják. Ebben az esetben vegye figyelembe a következő változásokat:

* Az STS-nek olyan jogkivonatokat kell kiállítania, amelyek elfogadhatók, és a licenckiszolgáló-Farm ellenőrizheti őket. A Axinom által biztosított Widevine-licenckiszolgálók például egy adott JWT igényelnek, amely jogosultsági üzenetet tartalmaz. Ezért rendelkeznie kell egy STS-vel, hogy ilyen JWT adjon ki. Ezen implementációs típussal kapcsolatos információkért tekintse meg az [Azure Dokumentációs központját](https://azure.microsoft.com/documentation/) , és lásd: a [Axinom használata a Widevine-licencek továbbításához Azure Media Services](media-services-axinom-integration.md).
* Már nem kell konfigurálnia a ContentKeyAuthorizationPolicy-t a Media Services. Meg kell adnia a licenc-beszerzési URL-címeket (a PlayReady, a Widevine és a FairPlay), amikor konfigurálja a AssetDeliveryPolicy-t a többplatformos DRM-sel való beállításhoz.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Mi a teendő, ha egyéni STS-t kívánok használni?
Az ügyfél dönthet úgy, hogy egyéni STS-t használ a JWTs biztosításához. Az okok a következők:

* Az ügyfél által használt IDENTITÁSSZOLGÁLTATÓ nem támogatja az STS használatát. Ebben az esetben egy egyéni STS lehet egy lehetőség.
* Előfordulhat, hogy az ügyfélnek rugalmasabb vagy szigorúbb vezérléssel kell rendelkeznie az STS és az ügyfél előfizetői számlázási rendszere integrálásához. Előfordulhat például, hogy egy MVPD operátor több OTT előfizető csomagot is kínál, például prémium, alapszintű és sport. Előfordulhat, hogy az operátornak meg kell egyeznie a jogkivonatban lévő jogcímek egy előfizetői csomaggal való egyeztetésével, így csak az adott csomagban lévő tartalmak lesznek elérhetők. Ebben az esetben egy egyéni STS biztosítja a szükséges rugalmasságot és irányítást.

Ha egyéni STS-t használ, két módosítást kell elvégezni:

* Ha egy eszközhöz állít be licenc-kézbesítési szolgáltatást, meg kell adnia az egyéni STS általi ellenőrzéshez használt biztonsági kulcsot az Azure AD-ből származó aktuális kulcs helyett. (További részleteket a következő témakörben talál:.) 
* JTW-token létrehozásakor a rendszer egy biztonsági kulcsot ad meg az aktuális X509-tanúsítvány titkos kulcsa helyett az Azure AD-ben.

A biztonsági kulcsok két típusa létezik:

* Szimmetrikus kulcs: a JWT létrehozásához és ellenőrzéséhez ugyanazt a kulcsot használja a rendszer.
* Aszimmetrikus kulcs: egy X509-tanúsítványban található nyilvános titkos kulcspár titkos kulccsal van használatban a JWT titkosításához/létrehozásához, valamint a nyilvános kulccsal a jogkivonat ellenőrzéséhez.

> [!NOTE]
> Ha a .NET-keretrendszer/C#-et használja fejlesztői platformként, akkor az aszimmetrikus biztonsági kulcshoz használt X509-tanúsítványnak legalább 2048-es kulcs hosszúságú kell lennie. Ez a System. IdentityModel. tokens. X509AsymmetricSecurityKey osztály követelménye a .NET-keretrendszerben. Ellenkező esetben a rendszer a következő kivételt dobja:
> 
> IDX10630: az aláíráshoz használt "System. IdentityModel. tokens. X509AsymmetricSecurityKey" nem lehet kisebb, mint "2048" bit.

## <a name="the-completed-system-and-test"></a>A befejezett rendszerek és tesztek
Ez a szakasz végigvezeti az alábbi forgatókönyveken a befejezett végpontok közötti rendszeren, így a bejelentkezési fiók létrehozása előtt alapszintű képet kaphat a viselkedésről:

* Ha nem integrált forgatókönyvre van szüksége:

    * A Media Servicesban üzemeltetett, nem védett vagy DRM-védelemmel ellátott, de jogkivonat-hitelesítés nélküli (licencet kiállító) eszközök esetén tesztelheti a bejelentkezés nélkül. Váltson HTTP-re, ha a videó folyamatos átvitele HTTP-n keresztül történik.

* Ha végpontok közötti integrált forgatókönyvre van szüksége:

    * A Media Services dinamikus DRM-védelemmel ellátott videós eszközei esetében az Azure AD által generált jogkivonat-hitelesítéssel és JWT kell bejelentkeznie.

A Player webalkalmazáshoz és a bejelentkezéshez tekintse meg [ezt a webhelyet](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A végpontok közötti integrált DRM-rendszer teszteléséhez létre kell hoznia vagy hozzá kell adnia egy fiókot.

Milyen fiókkal rendelkezik?

Habár az Azure eredetileg csak Microsoft-fiók felhasználók számára engedélyezte a hozzáférést, a hozzáférést mostantól a felhasználók is használhatják mindkét rendszerből. Az Azure-tulajdonságok mostantól megbíznak az Azure AD-ben a hitelesítéshez, és az Azure AD hitelesíti a szervezeti felhasználókat. Létrejött egy összevonási kapcsolat, ahol az Azure AD megbízik az Microsoft-fiók fogyasztói identitás rendszerében a fogyasztói felhasználók hitelesítéséhez. Ennek eredményeképpen az Azure AD a vendég Microsoft-fiókokat és a natív Azure AD-fiókokat is képes hitelesíteni.

Mivel az Azure AD megbízhatónak tekinti a Microsoft-fiók tartományt, a következő tartományokból bármelyik fiókot hozzáadhatja az egyéni Azure AD-bérlőhöz, és a fiók használatával jelentkezhet be:

| **Tartománynév** | **Tartomány** |
| --- | --- |
| **Egyéni Azure AD-bérlői tartomány** |somename.onmicrosoft.com |
| **Vállalati tartomány** |microsoft.com |
| **Microsoft-fiók tartomány** |outlook.com, live.com, hotmail.com |

A szerzők bármelyikével felveheti a kapcsolatot az Ön által létrehozott vagy hozzáadott fiókkal.

A következő Képernyőképek a különböző tartományi fiókok által használt különböző bejelentkezési lapokat mutatják be:

**Egyéni Azure ad-bérlői tartományi fiók**: az egyéni Azure ad-bérlői tartomány testreszabott bejelentkezési lapja.

![Képernyőkép, amely az egyéni Azure A D bérlői tartomány testreszabott bejelentkezési oldalát jeleníti meg.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: a Microsoft vállalat által a kétfaktoros hitelesítéssel testreszabott bejelentkezési oldal.

![Képernyőfelvétel: a Microsoft vállalati I T által testreszabott bejelentkezési oldal a kétfaktoros hitelesítéssel.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: a Microsoft-fiók bejelentkezési lapja a felhasználók számára.

![Egyéni Azure AD-bérlői tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Titkosított adathordozó-bővítmények használata a PlayReady
A PlayReady-támogatáshoz használható, titkosított adathordozó-bővítményekkel (EME) rendelkező modern böngészővel, például az Internet Explorer 11 Windows 8,1-es vagy újabb verziójával és a Windows 10 rendszerű Microsoft Edge böngészővel a PlayReady a mögöttes DRM.

![Az EME PlayReady használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A sötét lejátszó területe az, hogy a PlayReady-védelem megakadályozza a védett videók képernyőfelvételét.

A következő képernyőképen a Player beépülő moduljai és a Microsoft Security Essentials (MSE)/EME támogatása látható:

![A PlayReady lejátszó beépülő moduljai](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

A Microsoft Edge és az Internet Explorer 11 a Windows 10-es verzióban lehetővé teszi a [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) meghívását az azt támogató Windows 10-es eszközökön. A PlayReady SL3000 feloldja a bővített prémium szintű tartalom (4K, HDR) és az új Content Delivery models (bővített tartalom) folyamatát.

Ahhoz, hogy a Windows-eszközökre összpontosítsanak, a PlayReady az egyetlen DRM a Windows-eszközökön elérhető hardveren (PlayReady SL3000). Az adatfolyam-szolgáltatás a PlayReady-t az EME-n keresztül, vagy egy Univerzális Windows-platform alkalmazáson keresztül is használhatja, és magasabb színvonalú képminőséget kínál, ha más DRM-PlayReady SL3000 használ. Általában a-ig terjedő tartalom a Chrome-on vagy a Firefoxban keresztül zajlik, és a Microsoft Edge/Internet Explorer 11 vagy egy Univerzális Windows-platform alkalmazás használatával akár 4K-ra is áthaladhat. Az összeg a szolgáltatás beállításaitól és a megvalósítástól függ.

#### <a name="use-eme-for-widevine"></a>Az EME Widevine használata
Az EME/Widevine-támogatással rendelkező modern böngészőkben, például a Chrome 41 + Windows 10, a Windows 8,1, a Mac OSX Yosemite és a Chrome on Android 4.4.4, a Google Widevine a DRM mögött található.

![Az EME Widevine használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

A Widevine nem akadályozza meg a védett videó képernyőfelvételének készítését.

![A Widevine lejátszó beépülő moduljai](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Nem jogosult felhasználók
Ha a felhasználó nem tagja a "jogosult felhasználók" csoportnak, a felhasználó nem adja át a jogosultság-ellenőrzését. A multi-DRM licencelési szolgáltatás ezután elutasítja a kért licenc kiküldését az ábrán látható módon. A részletes leírás "a licenc beolvasásának hibája", amely a tervezett.

![Nem jogosult felhasználók](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonsági jogkivonat-szolgáltatás futtatása
Ha egyéni STS-t futtat, a JWT az egyéni STS állítja ki egy szimmetrikus vagy egy aszimmetrikus kulcs használatával.

Az alábbi képernyőfelvételen egy szimmetrikus kulcsot használó forgatókönyv látható (a Chrome használatával):

![Egyéni STS szimmetrikus kulccsal](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Az alábbi képernyőképen egy olyan forgatókönyv látható, amely aszimmetrikus kulcsot használ egy X509-tanúsítványon keresztül (egy Microsoft modern böngésző használatával):

![Egyéni STS aszimmetrikus kulccsal](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Mindkét előző esetben a felhasználói hitelesítés ugyanaz marad. Az Azure AD-n keresztül zajlik. Az egyetlen különbség, hogy a JWTs az Azure AD helyett az egyéni STS állítja ki. A dinamikus CENC-védelem konfigurálásakor a licenc-kézbesítési szolgáltatási korlátozás meghatározza a JWT típusát (szimmetrikus vagy aszimmetrikus kulcs).

## <a name="summary"></a>Összefoglalás

Ez a dokumentum a többszörös natív DRM-mel és hozzáférés-vezérléssel, a CENC, annak kialakításával és az Azure, Media Services és Media Player használatával történő megvalósításával kapcsolatban tárgyalt.

* A rendszer egy olyan hivatkozási tervet mutatott be, amely a DRM/CENC alrendszer összes szükséges összetevőjét tartalmazza.
* A referenciák megvalósítását az Azure, Media Services és Media Player ismerteti.
* A tervezésben és a megvalósításban közvetlenül érintett témaköröket is megvitatták.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
