---
title: Multi-DRM Content Protection rendszer
description: Ebből a cikkből megtudhatja, hogyan tervezhet meg több DRM-mel rendelkező tartalomkezelő rendszerét Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 48ae7b2356f7313acbfb298b7a492a0482e3106b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068132"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A felső (OTT) vagy online streaming megoldáshoz tartozó digitális Rights Management (DRM) alrendszer tervezése és létrehozása összetett feladat. A kezelők/online szolgáltatók jellemzően kiszervezik ezt a feladatot a speciális DRM-szolgáltatók számára. A jelen dokumentum célja, hogy egy hivatkozási tervet és egy teljes körű DRM-alrendszer hivatkozását egy OTT vagy online streaming megoldásban.

A dokumentum megcélozott olvasói olyan mérnökök, akik az OTT vagy online streaming/osztott megoldások vagy a DRM-alrendszerek iránt érdeklődők számára készült DRM-alrendszerekben működnek. Feltételezhető, hogy az olvasók a piacon legalább egy olyan DRM-technológiával rendelkeznek, mint például a PlayReady, a Widevine, a FairPlay vagy az Adobe Access.

Ebben a vitában a több DRM által támogatott 3 DRMs Azure Media Services: Common Encryption (CENC) támogatja a PlayReady és a Widevine, a FairPlay, valamint az AES-128 titkosítatlan kulcs titkosításához. Az online streaming és az OTT iparág egyik fő trendje a natív DRMs használata a különböző ügyféloldali platformokon. Ez a trend az előzőtől való elmozdulás, amely egyetlen DRM-t és az ügyféloldali SDK-t használta a különböző ügyféloldali platformokhoz. Ha a CENC-t több natív DRM-mel használja, a PlayReady és a Widevine is titkosítva van a [Common encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikáció alapján.

A natív multi-DRM használatának előnyei a tartalomvédelem esetében:

* Csökkenti a titkosítási költségeket, mivel a rendszer egyetlen titkosítási folyamatot használ a különböző platformok natív DRMs való célzására.
* Csökkenti az eszközök kezelésének költségeit, mivel csak egyetlen példányra van szükség a tárolóban.
* Kiküszöböli a DRM-ügyfél licencelési költségeit, mivel a natív DRM-ügyfél általában ingyenes a natív platformon.

### <a name="goals-of-the-article"></a>A cikk célja

A cikk célja:

* Adjon meg egy olyan DRM-alrendszer referenciáját, amely mind a 3 DRMs (CENC for DASH, FairPlay for HLS és PlayReady for Smooth streaming) használja.
* Adjon meg egy referenciát az Azure-ban és Azure Media Services platformon.
* Megismerheti a tervezési és megvalósítási témaköröket.

Az alábbi táblázat összefoglalja a natív DRM-támogatást a különböző platformokon, és az EME-támogatást különböző böngészőkben.

| **Ügyfélplatform** | **Natív DRM** | **EME** |
| --- | --- | --- |
| **Intelligens TV-k, STBs** | PlayReady, Widevine és/vagy egyéb | Beágyazott böngésző/EME PlayReady és/vagy Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 a PlayReady|
| **Android-eszközök (telefon, Tablet, TV)** |Widevine |Widevine-hez készült Chrome |
| **iOS** | FairPlay | Safari for FairPlay (iOS 11,2 óta) |
| **macOS** | FairPlay | Safari for FairPlay (mivel a Safari 9 + macOS X 10.11 + el Capitan)|
| **tvOS** | FairPlay | |

Az egyes DRM-eszközök üzembe helyezésének aktuális állapotát figyelembe véve a szolgáltatás általában két vagy három DRMs kíván megvalósítani, hogy a lehető legjobb megoldást biztosítsa a végpontok összes típusának kezeléséhez.

A szolgáltatás logikájának összetettsége és az ügyféloldali összetettsége közötti kompromisszum a különböző ügyfeleken a felhasználói élmény bizonyos szintjének elérése érdekében.

A kiválasztás elvégzéséhez tartsa szem előtt a következőket:

* A PlayReady natív módon implementálva van minden Windows-eszközön, bizonyos Android-eszközökön, és gyakorlatilag bármilyen platformon elérhető szoftveres SDK-n keresztül érhető el.
* A Widevine natív módon implementálva van minden Android-eszközön, a Chrome-ban és más eszközökön. A Widevine a Firefox és az Opera böngészőkben is támogatott a DASH-en keresztül.
* A FairPlay iOS-, macOS-és tvOS-eszközökön érhető el.

## <a name="a-reference-design"></a>Hivatkozási terv

Ez a szakasz egy olyan hivatkozási kialakítást mutat be, amely a megvalósításához használt technológiákkal kapcsolatos.

A DRM-alrendszer a következő összetevőket tartalmazza:

* Kulcskezelés
* DRM-titkosítás csomagolása
* DRM-licenckézbesítés
* Jogosultság ellenőrzése/hozzáférés-vezérlés
* Felhasználói hitelesítés/engedélyezés
* Player-alkalmazás
* Forrás/Content Delivery Network (CDN)

A következő ábra a DRM-alrendszerek összetevői közötti magas szintű interakciókat mutatja be:

![CENC rendelkező DRM-alrendszer](./media/architecture-design-multi-drm-system/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Biztonságos jogkivonat-szolgáltatás (STS)** |Azure AD |
| **DRM-védelem munkafolyamata** |Azure Media Services dinamikus védelem |
| **DRM-licenckézbesítés** |* Media Services licenc kézbesítése (PlayReady, Widevine, FairPlay) <br/>* Axinom-licenckiszolgáló <br/>* Egyéni PlayReady-licenckiszolgáló |
| **Származási** |Azure Media Services streaming végpont |
| **Kulcskezelés** |A hivatkozás megvalósításához nem szükséges |
| **Tartalomkezelés** |C# konzolos alkalmazás |

Más szóval a IDENTITÁSSZOLGÁLTATÓ és az STS is elérhető az Azure AD-ben. A lejátszóhoz a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) használatos. Mind a Azure Media Services, mind a Azure Media Player támogatja a CENC-t a DASH-en keresztül, a HLS, a PlayReady a Smooth streaming és az AES-128 titkosítást a DASH, a HLS és a Smooth FairPlay.

Az alábbi ábrán az előző technológiai leképezéssel rendelkező általános struktúra és folyamat látható:

![CENC Media Services](./media/architecture-design-multi-drm-system/media-services-cenc-subsystem-on-AMS-platform.png)

A DRM-tartalmak védelmének beállításához a tartalomkezelési eszköz a következő bemeneteket használja:

* Tartalom megnyitása
* Key Management-tartalom kulcsa
* Licenc-beszerzési URL-címek
* Az Azure AD-ból származó információk, például a célközönség, a kibocsátó és a jogkivonat-jogcímek listája

A Content Management eszköz kimenete:

* A ContentKeyPolicy leírja a DRM-licencek sablonját minden felhasznált DRM-típushoz;
* A ContentKeyPolicyRestriction a DRM-licencek kiadása előtt ismerteti a hozzáférés-vezérlést
* A Streamingpolicy leírja a DRM-titkosítási mód – Streaming Protocol – tároló formátumának különböző kombinációit a folyamatos átvitelhez
* A StreamingLocator a titkosításhoz és a folyamatos átviteli URL-címekhez használt Content Key/IV 

A folyamat futása közben:

* A felhasználó hitelesítése után létrejön egy JWT.
* A JWT lévő jogcímek egyike egy olyan csoportos jogcím, amely tartalmazza a EntitledUserGroup AZONOSÍTÓját. Ez a jogcím a jogosultsági ellenőrzések átadására szolgál.
* A lejátszó letölti az CENC-védelemmel ellátott tartalomhoz tartozó ügyfél-jegyzékfájlt, és azonosítja a következőket:
   * Kulcs azonosítója.
   * A tartalom DRM-védelemmel van ellátva.
   * Licenc-beszerzési URL-címek.
* A lejátszó a licencek beszerzésére irányuló kérést a böngésző/DRM által támogatott böngészők alapján teszi elérhetővé. A licenc-beszerzési kérelemben a kulcs AZONOSÍTÓját és a JWT is elküldi a rendszer. A licenc-kézbesítési szolgáltatás ellenőrzi a JWT és a jogcímeket, mielőtt kiadja a szükséges licencet.

## <a name="implementation"></a>Implementálás
### <a name="implementation-procedures"></a>Megvalósítási eljárások
A megvalósítás a következő lépéseket tartalmazza:

1. Tesztelési eszközök előkészítése. Videó kódolása/becsomagolása többszörös sávszélességű, töredezett MP4-Media Services. Ez az eszköz *nem* DRM-védelemmel ellátott. A DRM elleni védelmet később a dinamikus védelem végzi.

2. Hozzon létre egy kulcs-azonosítót és egy tartalmi kulcsot (opcionálisan egy kulcsfontosságú magot is). Ebben a példányban nincs szükség a kulcskezelő rendszerre, mert néhány tesztelési eszközhöz csak egyetlen kulcs-azonosító és egy tartalmi kulcs szükséges.

3. A Media Services API használatával konfigurálhatja a több DRM-alapú licencelési szolgáltatásokat a tesztelési eszközhöz. Ha a vállalata vagy a vállalata szállítói nem a Media Servicesban lévő licencelési szolgáltatásokat használják, akkor kihagyhatja ezt a lépést. A licencek kézbesítésének konfigurálásakor megadhatja a licencelési URL-címeket a lépésben. A Media Services API-nak meg kell adnia néhány részletes konfigurációt, például az engedélyezési házirend korlátozását és a licenccel kapcsolatos válaszokat a különböző DRM-licencelési szolgáltatásokhoz. Jelenleg a Azure Portal nem biztosítja a szükséges felhasználói felületet ehhez a konfigurációhoz. Az API-szintű információk és a mintakód esetében lásd: [PlayReady és/vagy Widevine dinamikus közös titkosítás használata](drm-protect-with-drm-tutorial.md).

4. A Media Services API-val konfigurálhatja az eszköz kézbesítési házirendjét a tesztelési eszközhöz. Az API-szintű információk és a mintakód esetében lásd: [PlayReady és/vagy Widevine dinamikus közös titkosítás használata](drm-protect-with-drm-tutorial.md).

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

* A fejlesztői információk a [Azure Active Directory fejlesztői útmutatójában](../../active-directory/develop/v2-overview.md)találhatók.
* A rendszergazdai információk az [Azure ad-bérlői címtár felügyeletében](../../active-directory/fundamentals/active-directory-whatis.md)találhatók.

### <a name="some-issues-in-implementation"></a>Néhány probléma a megvalósításban

A megvalósítással kapcsolatos problémák megoldásához használja az alábbi hibaelhárítási információkat.

* A kiállító URL-címnek a "/" értékkel kell végződnie. A célközönségnek a Player Application Client ID azonosítónak kell lennie. Emellett adja hozzá a "/" parancsot a kiállító URL-címének végén.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    A [JWT-dekóderben](http://jwt.calebb.net/)az **AUD** és az **ISS** jelenik meg, ahogy az a JWT is látható:

    ![JWT](./media/architecture-design-multi-drm-system/media-services-1st-gotcha.png)

* Engedélyeket adhat az alkalmazáshoz az Azure AD-ben az alkalmazás **Konfigurálás** lapján. Az egyes alkalmazásokhoz (helyi és telepített verziókhoz) engedélyek szükségesek.

    ![Engedélyek](./media/architecture-design-multi-drm-system/media-services-perms-to-other-apps.png)

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

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Mivel a SWT (ACS) mellett a JWT (Azure AD) támogatását is hozzáadja, az alapértelmezett TokenType a TokenType. JWT. Ha a SWT/ACS-t használja, a tokent a TokenType. SWT értékre kell állítania.

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

![Egyéni Azure AD-bérlői tartományi fiók](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: a Microsoft vállalat által a kétfaktoros hitelesítéssel testreszabott bejelentkezési oldal.

![Egyéni Azure AD-bérlői tartományi fiók – kettő](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: a Microsoft-fiók bejelentkezési lapja a felhasználók számára.

![Egyéni Azure AD-bérlői tartományi fiók – három](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Titkosított adathordozó-bővítmények használata a PlayReady

A PlayReady-támogatáshoz használható, titkosított adathordozó-bővítményekkel (EME) rendelkező modern böngészővel, például az Internet Explorer 11 Windows 8,1-es vagy újabb verziójával és a Windows 10 rendszerű Microsoft Edge böngészővel a PlayReady a mögöttes DRM.

![Az EME PlayReady használata](./media/architecture-design-multi-drm-system/media-services-eme-for-playready1.png)

A sötét lejátszó területe az, hogy a PlayReady-védelem megakadályozza a védett videók képernyőfelvételét.

A következő képernyőképen a Player beépülő moduljai és a Microsoft Security Essentials (MSE)/EME támogatása látható:

![A PlayReady lejátszó beépülő moduljai](./media/architecture-design-multi-drm-system/media-services-eme-for-playready2.png)

A Microsoft Edge és az Internet Explorer 11 a Windows 10-es verzióban lehetővé teszi a [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) meghívását az azt támogató Windows 10-es eszközökön. A PlayReady SL3000 feloldja a bővített prémium szintű tartalom (4K, HDR) és az új Content Delivery models (bővített tartalom) folyamatát.

Ahhoz, hogy a Windows-eszközökre összpontosítsanak, a PlayReady az egyetlen DRM a Windows-eszközökön elérhető hardveren (PlayReady SL3000). Az adatfolyam-szolgáltatás a PlayReady-t az EME-n keresztül, vagy egy Univerzális Windows-platform alkalmazáson keresztül is használhatja, és magasabb színvonalú képminőséget kínál, ha más DRM-PlayReady SL3000 használ. Általában a-ig terjedő tartalom a Chrome-on vagy a Firefoxban keresztül zajlik, és a Microsoft Edge/Internet Explorer 11 vagy egy Univerzális Windows-platform alkalmazás használatával akár 4K-ra is áthaladhat. Az összeg a szolgáltatás beállításaitól és a megvalósítástól függ.

#### <a name="use-eme-for-widevine"></a>Az EME Widevine használata

Az EME/Widevine-támogatással rendelkező modern böngészőkben, például a Chrome 41 + Windows 10, a Windows 8,1, a Mac OSX Yosemite és a Chrome on Android 4.4.4, a Google Widevine a DRM mögött található.

![Az EME Widevine használata](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine1.png)

A Widevine nem akadályozza meg a védett videó képernyőfelvételének készítését.

![A Widevine lejátszó beépülő moduljai](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Az EME FairPlay használata

Ehhez hasonlóan a FairPlay védett tartalmait is tesztelheti a Safariban macOS vagy iOS 11,2 és újabb rendszereken.

Győződjön meg arról, hogy a "FairPlay" értéket protectionInfo. Type értékre helyezi, és az alkalmazás tanúsítványának megfelelő URL-címet helyezi el az FPS AC útvonalon (FairPlay streaming Application Certificate Path).

### <a name="unentitled-users"></a>Nem jogosult felhasználók

Ha a felhasználó nem tagja a "jogosult felhasználók" csoportnak, a felhasználó nem adja át a jogosultság-ellenőrzését. A multi-DRM licencelési szolgáltatás ezután elutasítja a kért licenc kiküldését az ábrán látható módon. A részletes leírás "a licenc beolvasásának hibája", amely a tervezett.

![Nem jogosult felhasználók](./media/architecture-design-multi-drm-system/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonsági jogkivonat-szolgáltatás futtatása

Ha egyéni STS-t futtat, a JWT az egyéni STS állítja ki egy szimmetrikus vagy egy aszimmetrikus kulcs használatával.

Az alábbi képernyőfelvételen egy szimmetrikus kulcsot használó forgatókönyv látható (a Chrome használatával):

![Egyéni STS szimmetrikus kulccsal](./media/architecture-design-multi-drm-system/media-services-running-sts1.png)

Az alábbi képernyőképen egy olyan forgatókönyv látható, amely aszimmetrikus kulcsot használ egy X509-tanúsítványon keresztül (egy Microsoft modern böngésző használatával):

![Egyéni STS aszimmetrikus kulccsal](./media/architecture-design-multi-drm-system/media-services-running-sts2.png)

Mindkét előző esetben a felhasználói hitelesítés ugyanaz marad. Az Azure AD-n keresztül zajlik. Az egyetlen különbség, hogy a JWTs az Azure AD helyett az egyéni STS állítja ki. A dinamikus CENC-védelem konfigurálásakor a licenc-kézbesítési szolgáltatási korlátozás meghatározza a JWT típusát (szimmetrikus vagy aszimmetrikus kulcs).
