---
title: Microsoft Enterprise egyszeri bejelentkezéses beépülő modul Apple-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Ismerje meg a Microsoft iOS-, iPadOS-és macOS-eszközökhöz készült Azure Active Directory SSO beépülő modulját.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427096"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO beépülő modul Apple-eszközökhöz (előzetes verzió)

>[!IMPORTANT]
> Ez a funkció [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Az *Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul* egyszeri bejelentkezést (SSO) biztosít az Azure Active Directory (Azure ad) fiókokhoz MacOS, iOS és iPadOS rendszeren minden olyan alkalmazás esetében, amely támogatja az Apple [vállalati egyszeri bejelentkezés](https://developer.apple.com/documentation/authenticationservices) funkcióját. Ide tartoznak azok a régebbi alkalmazások is, amelyek a vállalattól függenek, de még nem támogatják a legújabb Identity Library-kódtárakat vagy-protokollokat. A Microsoft az Apple-szel szorosan együttműködve fejleszti ezt a beépülő modult az alkalmazás használhatóságának növeléséhez, miközben az Apple és a Microsoft által biztosított legjobb védelmet nyújtja.

A vállalati egyszeri bejelentkezés beépülő modul jelenleg a következő alkalmazások beépített funkciójaként érhető el:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) – iOS, iPadOS
* Microsoft Intune [céges portál](/mem/intune/apps/apps-company-portal-macos) – MacOS

## <a name="features"></a>Funkciók

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul a következő előnyöket kínálja:

- Egyszeri bejelentkezést biztosít az Azure AD-fiókokhoz minden olyan alkalmazás esetében, amely támogatja az Apple Enterprise Sign-On funkciót.
- Bármely mobileszköz-kezelési (MDM) megoldással engedélyezhető.
- Kiterjeszti az egyszeri bejelentkezést olyan alkalmazásokra, amelyek még nem használják a Microsoft Identity platform kódtárait.
- Kiterjeszti az egyszeri bejelentkezést a OAuth2, az OpenID Connect és az SAML alkalmazást használó alkalmazásokba.

## <a name="requirements"></a>Követelmények

A Microsoft Enterprise SSO beépülő modul használata Apple-eszközökhöz:

- Az eszköznek **támogatnia** kell és rendelkeznie kell olyan alkalmazással, amely tartalmazza a Microsoft Enterprise SSO beépülő modult a **telepített** Apple-eszközökhöz:
  - iOS 13.0 +: [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 +: [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [Intune céges portál alkalmazás](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Az eszköznek **Mdm** kell lennie (például Microsoft Intune).
- A konfigurációt **az eszközön kell leküldeni** , hogy engedélyezze a vállalati egyszeri bejelentkezés beépülő modult az eszközön. Ezt a biztonsági korlátozást az Apple igényli.

### <a name="ios-requirements"></a>iOS-követelmények:
- az iOS 13,0-es vagy újabb verziójának telepítve kell lennie az eszközön.
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ezek az alkalmazások a [Microsoft Authenticator alkalmazás](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>macOS-követelmények:
- a macOS 10,15-es vagy újabb verziójának telepítve kell lennie az eszközön. 
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ezek az alkalmazások a [Intune céges portál alkalmazást](/intune/user-help/enroll-your-device-in-intune-macos-cp.md)tartalmazzák.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Egyszeri bejelentkezéses beépülő modul engedélyezése a mobileszköz-felügyelettel (MDM)

### <a name="microsoft-intune-configuration"></a>Microsoft Intune konfiguráció

Ha a Microsoft Intunet használja a MDM szolgáltatásként, a beépített konfigurációs profil beállításaival engedélyezheti a Microsoft vállalati egyszeri bejelentkezés beépülő modulját.

Először konfigurálja egy konfigurációs profil [egyszeri bejelentkezési alkalmazás-bővítményének](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) beállításait, és [rendelje hozzá a profilt egy felhasználóhoz vagy az eszköz csoportjához](/mem/intune/configuration/device-profile-assign) (ha még nincs hozzárendelve).

Az egyszeri bejelentkezéses beépülő modult engedélyező Profilbeállítások automatikusan a csoport eszközeire lesznek alkalmazva, amikor legközelebb minden eszköz bejelentkezik az Intune-ba.

### <a name="manual-configuration-for-other-mdm-services"></a>Más MDM-szolgáltatások manuális konfigurálása

Ha nem használ Microsoft Intune a mobileszköz-kezeléshez, a következő paraméterekkel konfigurálhatja a Microsoft Enterprise SSO beépülő modult az Apple-eszközökhöz.

#### <a name="ios-settings"></a>iOS-beállítások:

- **BŐVÍTMÉNY azonosítója**: `com.microsoft.azureauthenticator.ssoextension`
- **Csoport azonosítója**: (ez a mező nem szükséges az iOS esetében)

#### <a name="macos-settings"></a>macOS-beállítások:

- **BŐVÍTMÉNY azonosítója**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Csoport azonosítója**: `UBF8T346G9`

#### <a name="common-settings"></a>Általános beállítások:

- **Típus**: átirányítás
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>További konfigurációs beállítások
További konfigurációs beállítások adhatók hozzá az SSO-funkciók további alkalmazásokra való kiterjesztéséhez.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Egyszeri bejelentkezés engedélyezése olyan alkalmazásokhoz, amelyek nem használnak Microsoft Identity platform-függvénytárat

Az egyszeri bejelentkezéses beépülő modul lehetővé teszi az alkalmazások számára az egyszeri bejelentkezést, még akkor is, ha nem Microsoft SDK-val, például a Microsoft Authentication Library (MSAL) használatával lett kifejlesztve.

Az egyszeri bejelentkezéses beépülő modul automatikusan települ az iOS-és iPadOS-alapú Microsoft Authenticator alkalmazást vagy a macOS rendszeren Intune Céges portál alkalmazást futtató eszközökön, és regisztrálta az eszközét a szervezetében. A szervezet valószínűleg a hitelesítő alkalmazást használja olyan forgatókönyvek esetén, mint például a többtényezős hitelesítés, a jelszó nélküli hitelesítés és a feltételes hozzáférés. Az alkalmazások bármely MDM-szolgáltató használatával bekapcsolhatók, bár a Microsoft az Intune Microsoft Endpoint Managerben való konfigurálását egyszerűen megteheti. Az engedélyezési lista használatával konfigurálhatja ezeket az alkalmazásokat az egyszeri bejelentkezés beépülő modul használatára.

>[!IMPORTANT]
> Csak a natív Apple hálózati technológiákat vagy webnézeteket használó alkalmazások támogatottak. Ha egy alkalmazás a saját hálózati rétegének megvalósítását is elvégezte, a Microsoft Enterprise SSO beépülő modul nem támogatott.  

A következő paraméterek használatával konfigurálhatja a Microsoft vállalati egyszeri bejelentkezés beépülő modulját olyan alkalmazásokhoz, amelyek nem használnak Microsoft Identity platform-függvénytárat:

Ha meg szeretné adni az adott alkalmazások listáját:

- **Kulcs**: `AppAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az alkalmazási köteg azonosítóinak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre
- **Példa**: `com.contoso.workapp, com.contoso.travelapp`

Vagy ha meg szeretné adni az előtagok listáját:
- **Kulcs**: `AppPrefixAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az ALKALMAZÁSCSOMAG-azonosító előtagjainak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre. Vegye figyelembe, hogy ez lehetővé teszi, hogy az összes alkalmazás egy adott előtaggal kezdődik az SSO-ban való részvételhez
- **Példa**: `com.contoso., com.fabrikam.`

A MDM-rendszergazda által az SSO-ben való részvételre engedélyezett, belefoglalt [alkalmazások](./application-consent-experience.md) a végfelhasználók számára csendes jogkivonatot szerezhetnek. Ezért fontos, hogy csak megbízható alkalmazásokat adjon hozzá az engedélyezési listához. 

>[!NOTE]
> Ehhez a listához nem kell hozzáadnia MSAL vagy ASWebAuthenticationSession-t használó alkalmazásokat. Ezek az alkalmazások alapértelmezés szerint engedélyezve vannak. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>App Bundle-azonosítók felderítése iOS-eszközökön

Az Apple nem nyújt egyszerű módszert az App Store-ból származó köteg-azonosítók felderítésére. Az egyszeri bejelentkezéshez használni kívánt alkalmazások köteg-azonosítóinak megkeresésének legegyszerűbb módja, ha megkérdezi a gyártó vagy az alkalmazás fejlesztőjét. Ha ez a lehetőség nem érhető el, használhatja a MDM konfigurációját a köteg-azonosítók felderítésére. 

A következő jelző ideiglenes engedélyezése a MDM-konfigurációban:

- **Kulcs**: `admin_debug_mode_enabled`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Ha ez a jelző be van jelentkezve az iOS-alkalmazásokra azon az eszközön, amelyről tudni szeretné a köteg AZONOSÍTÓját. Ezután nyissa meg Microsoft Authenticator app-> Súgó-> naplók küldése – > View naplók. 

A naplófájlban keresse meg a következő sort:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Ennek során rögzíteni kell az összes alkalmazáscsomag-azonosítót az SSO-bővítmény számára. Ezután ezeket az azonosítókat használhatja az egyszeri bejelentkezés konfigurálásához az alkalmazások számára. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Lehetővé teszi, hogy a felhasználó ismeretlen alkalmazásokból és a Safari böngészőből jelentkezzen be.

Alapértelmezés szerint a Microsoft Enterprise SSO beépülő modulja csak akkor biztosít egyszeri bejelentkezést a hitelesített alkalmazásokhoz, ha egy felhasználó olyan alkalmazásból jelentkezett be, amely Microsoft Identity platform-függvénytárat (például ADAL vagy MSAL) használ. A Microsoft Enterprise SSO beépülő modulja megosztott hitelesítő adatokat is megadhat, ha egy másik alkalmazás hívja meg a Microsoft Identity platform-függvénytárat egy új jogkivonat beszerzése során.

`browser_sso_interaction_enabled`A Flag engedélyezése lehetővé teszi, hogy az alkalmazás ne használjon Microsoft Identity platform-függvénytárat a kezdeti rendszerindítás elvégzéséhez és a megosztott hitelesítő adatok beszerzéséhez. Emellett lehetővé teszi a Safari böngésző számára a kezdeti indítást és a megosztott hitelesítő adatok beszerzését. Ha a Microsoft vállalati egyszeri bejelentkezés beépülő modulja még nem rendelkezik megosztott hitelesítő adatokkal, akkor a rendszer megkísérli a bejelentkezést, amikor egy Azure AD-URL-címről kéri a Safari böngészőben, a ASWebAuthenticationSession-ban, a SafariViewController-ban vagy más engedélyezett natív alkalmazásban.  

- **Kulcs**: `browser_sso_interaction_enabled`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

MacOS esetén ez a beállítás szükséges ahhoz, hogy egységesebb élményt kapjon az összes alkalmazásban. IOS-és iPadOS esetén ez a beállítás nem szükséges, mivel a legtöbb alkalmazás a Microsoft Authenticator alkalmazást használja a bejelentkezéshez. Ha azonban olyan alkalmazásokkal rendelkezik, amelyek nem az iOS-es vagy iPadOS-es Microsoft Authenticator használják, akkor ez a jelző javítja a felhasználói élményt, ezért javasoljuk, hogy engedélyezze a beállítást. Ez alapértelmezés szerint le van tiltva.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Az MFA kérésének letiltása a kezdeti rendszerindítás során

Alapértelmezés szerint a Microsoft vállalati egyszeri bejelentkezés beépülő modulja a kezdeti indításkor és a megosztott hitelesítő adatok beszerzése során mindig kéri a felhasználót a többtényezős hitelesítésre (MFA), még akkor is, ha ez nem szükséges a felhasználó által indított aktuális alkalmazáshoz. Így a megosztott hitelesítő adatok könnyen használhatók az összes további alkalmazásban anélkül, hogy a felhasználót megkéri, ha az MFA később válik szükségessé. Ez csökkenti azokat az időpontokat, amikor a felhasználónak meg kell adnia az eszközt, és általában jó döntés.

Ha engedélyezi ezt a beállítást `browser_sso_disable_mfa` , és csak akkor kéri a felhasználót, ha az MFA-t egy alkalmazás vagy erőforrás igényli. 

- **Kulcs**: `browser_sso_disable_mfa`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Azt javasoljuk, hogy a jelzőt ne tiltsa le, mert csökkenti a felhasználó által az eszközre vonatkozó kérés időpontját. Ha a szervezete ritkán használ MFA-t, érdemes lehet engedélyezni a jelzőt, de azt javasoljuk, hogy használjon inkább MFA-t gyakrabban. Emiatt alapértelmezés szerint le van tiltva.

#### <a name="disable-oauth2-application-prompts"></a>OAuth2-alkalmazási kérések letiltása

A Microsoft Enterprise SSO beépülő modulja egyszeri bejelentkezést biztosít az engedélyezett alkalmazásokból érkező hálózati kérésekhez tartozó megosztott hitelesítő adatok hozzáfűzésével. Előfordulhat azonban, hogy egyes OAuth2-alkalmazások helytelenül kényszerítik a végfelhasználói kéréseket a protokoll rétegében. Ha ez történik, látni fogja, hogy a rendszer figyelmen kívül hagyja a megosztott hitelesítő adatokat azokon az alkalmazásokban, és a felhasználó kéri a bejelentkezést, annak ellenére, hogy a Microsoft Enterprise SSO beépülő modul más alkalmazásokhoz is működik.  

A `disable_explicit_app_prompt` jelölő engedélyezése korlátozza a natív és a webalkalmazások képességét, hogy a végfelhasználói kérdést kényszerítse a protokoll rétegén és az egyszeri bejelentkezés mellőzését.

- **Kulcs**: `disable_explicit_app_prompt`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Javasoljuk, hogy ezt a jelzőt úgy engedélyezze, hogy következetesebb élményt kapjon az összes alkalmazásban. Ez alapértelmezés szerint le van tiltva. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Egyszeri bejelentkezés engedélyezése a cookie-kon keresztül adott alkalmazáshoz

Előfordulhat, hogy egy kis számú alkalmazás nem kompatibilis az SSO-bővítménnyel. A speciális hálózati beállításokkal rendelkező alkalmazások esetében váratlan problémák merülhetnek fel, ha engedélyezve vannak az egyszeri bejelentkezés (például a hálózati kérelem megszakított vagy megszakított hibát jelez). 

Ha problémákba ütközik a szakaszban ismertetett módszer használatával, az `Enable SSO for apps that don't use MSAL` alkalmazások alternatív konfigurációját is kipróbálhatja. 

A következő paraméterek használatával konfigurálhatja a Microsoft Enterprise SSO beépülő modult az adott alkalmazásokhoz:

- **Kulcs**: `AppCookieSSOAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az ALKALMAZÁSCSOMAG-azonosító előtagjainak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre. Vegye figyelembe, hogy ez lehetővé teszi, hogy az összes alkalmazás egy adott előtaggal kezdődik az SSO-ban való részvételhez
- **Példa**: `com.contoso.myapp1, com.fabrikam.myapp2`

Vegye figyelembe, hogy a mechanizmust használó egyszeri bejelentkezéshez engedélyezett alkalmazásokat is hozzá kell adni a és a rendszerhez `AppCookieSSOAllowList` `AppPrefixAllowList` .

Javasoljuk, hogy ezt a beállítást csak a váratlan bejelentkezési hibákat észlelő alkalmazások esetében próbálja meg. 

#### <a name="use-intune-for-simplified-configuration"></a>Az Intune használata egyszerűsített konfigurációhoz

Ahogy azt korábban már említettük, a Microsoft Intune MDM szolgáltatásával egyszerűen beállíthatja a Microsoft vállalati egyszeri bejelentkezés beépülő modulját, beleértve a beépülő modul engedélyezését és a régebbi alkalmazások engedélyezési listához való hozzáadását, így azok egyszeri bejelentkezést kapnak. További információt az [Intune konfigurációs dokumentációjában](/intune/configuration/ios-device-features-settings)talál.

## <a name="using-the-sso-plug-in-in-your-application"></a>Az SSO beépülő modul használata az alkalmazásban

Az Apple-eszközök 1.1.0-es és újabb verziójának [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verziója támogatja az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult. Javasoljuk, hogy támogassa a Microsoft Enterprise SSO beépülő modul támogatását, és biztosítsa a Microsoft Identity platform teljes körű képességeit.

Ha olyan alkalmazást hoz létre, amely Frontline Worker-forgatókönyvekhez készült, tekintse meg az [iOS-eszközök megosztott eszköz üzemmódját](msal-ios-shared-devices.md) a funkció további beállításához.

## <a name="how-the-sso-plug-in-works"></a>Az SSO beépülő modul működése

A Microsoft Enterprise SSO beépülő modul az [Apple Enterprise Single Sign-On Framework-keretrendszerre](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)támaszkodik. A keretrendszerbe feltüntető szolgáltatók képesek a tartományok hálózati forgalmának elfogására és a kérelmek kezelésének javítására vagy módosítására. Az SSO beépülő modul például további felhasználói felületet jeleníthet meg a végfelhasználói hitelesítő adatok biztonságos összegyűjtéséhez, a többtényezős hitelesítés megköveteléséhez, vagy a tokenek csendes biztosításához az alkalmazáshoz.

A natív alkalmazások egyéni műveleteket is megvalósítanak, és közvetlenül az egyszeri bejelentkezéses beépülő modullal kommunikálnak.
Az egyszeri bejelentkezési keretrendszerről ebben a [2019 WWDC-videóban olvashat az Apple-től](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Microsoft Identity platform könyvtárat használó alkalmazások

A [Microsoft Authentication Library (MSAL) for Apple Devices](https://github.com/AzureAD/microsoft-authentication-library-for-objc) a 1.1.0 és a magasabb verziója támogatja a Microsoft Enterprise SSO beépülő modult az Apple-eszközökön natív módon munkahelyi és iskolai fiókokhoz. 

Ha követte az [összes javasolt lépést](./quickstart-v2-ios.md) , és az alapértelmezett [átirányítási URI-formátumot](./redirect-uris-ios.md)használta, nincs szükség speciális konfigurációra. Ha egy olyan eszközön fut, amelyen megtalálható az SSO beépülő modul, a MSAL automatikusan meghívja az összes interaktív és csendes jogkivonat-kérelemre, valamint a fiókok enumerálására és a fiókok eltávolítására irányuló műveletekre. Mivel a MSAL olyan natív SSO beépülő modult valósít meg, amely egyéni műveletekre támaszkodik, ez a telepítő a legsimább natív élményt biztosítja a végfelhasználók számára. 

Ha az SSO beépülő modult nem engedélyezi a MDM, de a Microsoft Authenticator alkalmazás megtalálható az eszközön, akkor a MSAL Ehelyett az Microsoft Authenticator alkalmazást használja az interaktív jogkivonat-kérelmekhez. Az egyszeri bejelentkezéses beépülő modul a Microsoft Authenticator alkalmazással is megosztja az egyszeri bejelentkezést.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft Identity platform-függvénytárat nem használó alkalmazások

Azok az alkalmazások, amelyek nem használnak Microsoft Identity platform-függvénytárat (például MSAL), továbbra is kaphatnak SSO-t, ha a rendszergazda explicit módon hozzáadja azokat az engedélyezési listához. 

Ezekben az alkalmazásokban nincs szükség kód módosítására, ha a következő feltételek teljesülnek:

- Az alkalmazás Apple-keretrendszereket használ a hálózati kérelmek végrehajtásához (például [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Az alkalmazás szabványos protokollokat használ az Azure AD-vel való kommunikációhoz (például OAuth2, SAML, WS-Federation)
- Az alkalmazás nem gyűjti az egyszerű szöveges felhasználónevet és a jelszót a natív felhasználói felületen

Ebben az esetben az SSO akkor van megadva, amikor az alkalmazás létrehoz egy hálózati kérelmet, és megnyit egy webböngészőt a felhasználó aláírásához. Amikor egy felhasználó átirányítja egy Azure AD bejelentkezési URL-címre, az SSO beépülő modul ellenőrzi az URL-címet, és ellenőrzi, hogy van-e elérhető SSO hitelesítő adat az adott URL-címhez. Ha van ilyen, az egyszeri bejelentkezéses beépülő modul átadja az egyszeri bejelentkezéshez használt hitelesítő adatokat az Azure AD-nek, amely engedélyezi az alkalmazás számára a hálózati kérelem elvégzését anélkül, hogy a felhasználónak meg kellene adnia a hitelesítő adatait. Továbbá, ha az eszköz ismert az Azure AD-ben, az egyszeri bejelentkezéses beépülő modul is továbbítja az eszköz tanúsítványát az eszköz alapú feltételes hozzáférés-ellenőrzések teljesítéséhez. 

A nem MSAL alkalmazások egyszeri bejelentkezésének támogatásához az SSO beépülő modul olyan protokollt valósít meg, amely a [Mi az elsődleges frissítési jogkivonat?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)című témakörben ismertetett Windows-böngésző beépülő modulhoz hasonló. 

A MSAL-alapú alkalmazásokhoz képest az SSO beépülő modul átláthatóbbá teszi a nem MSAL alkalmazások számára az alkalmazások által biztosított meglévő böngészőbeli bejelentkezési élmény integrálásával. A végfelhasználók láthatják a megszokott tapasztalataikat, így nem kell további bejelentkezéseket végezniük az egyes alkalmazásokban. A natív fiók választójának megjelenítése helyett például az SSO beépülő modul SSO-munkameneteket biztosít a webalapú fiók-választói élményhez. 

## <a name="next-steps"></a>Következő lépések

További információ az iOS-es megosztott eszköz üzemmódról: [megosztott eszköz mód iOS-eszközökhöz](msal-ios-shared-devices.md).
