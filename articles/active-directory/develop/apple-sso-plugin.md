---
title: Microsoft Enterprise egyszeri bejelentkezéses beépülő modul Apple-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Ismerkedjen meg az iOS-, iPadOS-és macOS-eszközökhöz készült Azure Active Directory SSO beépülő modullal.
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
ms.openlocfilehash: 24a538686e101d40daba008f30a72ffc5078047a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674520"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO beépülő modul Apple-eszközökhöz (előzetes verzió)

>[!IMPORTANT]
> Ez a funkció [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Az *Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul* egyszeri bejelentkezést (SSO) biztosít az Azure Active Directory (Azure ad) fiókokhoz MacOS, iOS és iPadOS rendszeren minden olyan alkalmazás esetében, amely támogatja az Apple [vállalati egyszeri bejelentkezés](https://developer.apple.com/documentation/authenticationservices) funkcióját. A beépülő modul egyszeri bejelentkezést biztosít az olyan régi alkalmazások számára, amelyeknek a vállalata függhet, de még nem támogatja a legújabb Identitáskezelés-kódtárakat vagy-protokollokat. A Microsoft az Apple-szel szorosan együttműködve fejleszti ezt a beépülő modult az alkalmazás használhatóságának növeléséhez, miközben elérhetővé teszi a legjobb védelmet.

A vállalati egyszeri bejelentkezés beépülő modul jelenleg a következő alkalmazások beépített funkciója:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [céges portál](/mem/intune/apps/apps-company-portal-macos): MacOS

## <a name="features"></a>Funkciók

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul a következő előnyöket kínálja:

- Egyszeri bejelentkezést biztosít az Azure AD-fiókokhoz az Apple Enterprise SSO funkciót támogató összes alkalmazásban.
- Bármely mobileszköz-kezelési (MDM) megoldás engedélyezhető.
- Kiterjeszti az egyszeri bejelentkezést olyan alkalmazásokra, amelyek még nem használják a Microsoft Identity platform kódtárait.
- Kiterjeszti az SSO-t a OAuth 2, OpenID Connect és SAML-t használó alkalmazásokba.

## <a name="requirements"></a>Követelmények

A Microsoft Enterprise SSO beépülő modul használata Apple-eszközökhöz:

- Az eszköznek *támogatnia* kell és rendelkeznie kell egy telepített alkalmazással, amely rendelkezik az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modullal:
  - iOS 13,0 és újabb verziók: [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13,0 és újabb verziók: [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 és újabb verziók: [Intune céges portál alkalmazás](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Az eszközt regisztrálni kell a *Mdm-ben*, például Microsoft Intuneon keresztül.
- A vállalati egyszeri bejelentkezés beépülő modul engedélyezéséhez le kell *küldeni a konfigurációt az eszközre* . Az Apple megköveteli ezt a biztonsági korlátozást.

iOS-követelmények:
- az iOS 13,0-es vagy újabb verziójának telepítve kell lennie az eszközön.
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ez az alkalmazás a [Microsoft Authenticator alkalmazás](/intune/user-help/user-help-auth-app-overview.md).


macOS-követelmények:
- a macOS 10,15-es vagy újabb verziójának telepítve kell lennie az eszközön. 
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ez az alkalmazás a [Intune céges portál alkalmazás](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in"></a>Egyszeri bejelentkezéses beépülő modul engedélyezése

A következő információk segítségével engedélyezheti az egyszeri bejelentkezés beépülő modult a MDM használatával.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune konfiguráció

Ha a Microsoft Intunet használja a MDM szolgáltatásként, a beépített konfigurációs profil beállításaival engedélyezheti a Microsoft vállalati egyszeri bejelentkezés beépülő modulját:

1. Konfigurálhatja egy konfigurációs profil [egyszeri bejelentkezéses alkalmazás-bővítményének](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) beállításait. 
1. Ha a profil még nincs hozzárendelve, [rendelje hozzá a profilt egy felhasználóhoz vagy egy eszköz csoportjához](/mem/intune/configuration/device-profile-assign).

Az egyszeri bejelentkezéses beépülő modult engedélyező Profilbeállítások automatikusan a csoport eszközeire lesznek alkalmazva, amikor legközelebb minden eszköz bejelentkezik az Intune-ba.

### <a name="manual-configuration-for-other-mdm-services"></a>Más MDM-szolgáltatások manuális konfigurálása

Ha nem használja az Intune-t a MDM-hez, a következő paraméterekkel konfigurálhatja a Microsoft Enterprise SSO beépülő modult az Apple-eszközökhöz.

iOS-beállítások:

- **BŐVÍTMÉNY azonosítója**: `com.microsoft.azureauthenticator.ssoextension`
- **Csoport azonosítója**: Ez a mező nem szükséges az iOS-hez.

macOS-beállítások:

- **BŐVÍTMÉNY azonosítója**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Csoport azonosítója**: `UBF8T346G9`

Általános beállítások:

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
  
### <a name="more-configuration-options"></a>További konfigurációs beállítások
További konfigurációs beállításokat adhat meg az egyszeri bejelentkezés funkció más alkalmazásokra való kiterjesztéséhez.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Egyszeri bejelentkezés engedélyezése olyan alkalmazásokhoz, amelyek nem használnak Microsoft Identity platform-függvénytárat

Az egyszeri bejelentkezéses beépülő modul lehetővé teszi, hogy bármely alkalmazás az SSO-ban is részt vegyen, még akkor is, ha azt nem a Microsoft SDK, például a Microsoft Authentication Library (MSAL) használatával fejlesztették

Az SSO beépülő modult a rendszer automatikusan telepíti:
* Letöltötte a hitelesítő alkalmazást iOS-vagy iPadOS, vagy letölti a Intune Céges portál alkalmazást macOS rendszeren.
* Az eszköz regisztrálása a szervezetnél. 

A szervezet valószínűleg a hitelesítő alkalmazást használja olyan forgatókönyvekhez, mint például a többtényezős hitelesítés (MFA), a jelszóval való hitelesítés és a feltételes hozzáférés. MDM-szolgáltató használatával bekapcsolhatja az alkalmazások SSO beépülő modulját. A Microsoft megkönnyíti a beépülő modul konfigurálását a Microsoft Endpoint Managerben az Intune-ban. A engedélyezési az egyszeri bejelentkezéses beépülő modul használatára konfigurálja ezeket az alkalmazásokat.

>[!IMPORTANT]
> A Microsoft Enterprise SSO beépülő modul csak a natív Apple hálózati technológiákat vagy webnézeteket használó alkalmazásokat támogatja. Nem támogatja azokat az alkalmazásokat, amelyek a saját hálózati rétegük megvalósítását szállítják.  

A következő paraméterek használatával konfigurálhatja a Microsoft vállalati egyszeri bejelentkezés beépülő modulját olyan alkalmazásokhoz, amelyek nem használnak Microsoft Identity platform-függvénytárat.

Az adott alkalmazások listájának megadásához használja a következő paramétereket:

- **Kulcs**: `AppAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az SSO-ban részt vevő alkalmazások számára engedélyezett alkalmazáscsomag-azonosítók vesszővel tagolt listája.
- **Példa**: `com.contoso.workapp, com.contoso.travelapp`

Az előtagok listájának megadásához használja a következő paramétereket:
- **Kulcs**: `AppPrefixAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az ALKALMAZÁSCSOMAG-azonosító előtagjainak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre. Ez a paraméter lehetővé teszi az összes olyan alkalmazás számára, amely egy adott előtaggal kezdődik az SSO-ban való részvételhez.
- **Példa**: `com.contoso., com.fabrikam.`

Azok az [alkalmazások](./application-consent-experience.md) , amelyeket a Mdm-rendszergazda engedélyez az SSO-ben való részvételre, csendesen lekérhetnek egy jogkivonatot a végfelhasználó számára. Ezért csak megbízható alkalmazásokat adjon hozzá a engedélyezési. 

>[!NOTE]
> Nem kell hozzáadnia az MSAL-t vagy a ASWebAuthenticationSession-t használó alkalmazásokat azon alkalmazások listájához, amelyek részt vehetnek az SSO-ban. Ezek az alkalmazások alapértelmezés szerint engedélyezve vannak. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Alkalmazáscsomag-azonosítók keresése iOS-eszközökön

Az Apple nem egyszerű módszert kínál a köteg-azonosítók beszerzésére az App Store áruházból. Az egyszeri bejelentkezéshez használni kívánt alkalmazások köteg-azonosítóinak beszerzésének legegyszerűbb módja, ha megkérdezi a gyártó vagy az alkalmazás fejlesztőjét. Ha ez a lehetőség nem érhető el, a MDM-konfiguráció segítségével megkeresheti a csomagok azonosítóit: 

1. Ideiglenesen engedélyezze a következő jelzőt a MDM-konfigurációban:

    - **Kulcs**: `admin_debug_mode_enabled`
    - **Írja be a következőt**: `Integer`
    - **Érték**: 1 vagy 0
1. Ha bekapcsolja ezt a jelzőt, jelentkezzen be az iOS-alkalmazásokba azon az eszközön, amelyre vonatkozóan tudni szeretné a köteg AZONOSÍTÓját. 
1. A hitelesítő alkalmazásban válassza a **Súgó**  >  **naplók**  >  **megtekintése naplókat**. 
1. A naplófájlban keresse meg a következő sort: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Ebben a sorban az összes olyan alkalmazáscsomag-azonosítót rögzíteni kell, amelyek láthatók az SSO-bővítmény számára. 

A köteg-azonosítók használatával konfigurálhatja az egyszeri bejelentkezést az alkalmazásokhoz. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Az ismeretlen alkalmazásokból és a Safari böngészőből való bejelentkezés engedélyezése a felhasználók számára

Alapértelmezés szerint a Microsoft Enterprise SSO beépülő modulja csak akkor biztosít egyszeri bejelentkezést a hitelesített alkalmazásokhoz, ha egy felhasználó olyan alkalmazásból jelentkezett be, amely Microsoft Identity platform-függvénytárat használ, például MSAL vagy Azure Active Directory Authentication Library (ADAL). A Microsoft Enterprise SSO beépülő modulja megosztott hitelesítő adatokat is megadhat, ha egy másik alkalmazás hívja meg a Microsoft Identity platform-függvénytárat egy új jogkivonat beszerzése során.

Ha engedélyezi a `browser_sso_interaction_enabled` jelzőt, a Microsoft Identity platform-függvénytárat nem használó alkalmazások megtehetik a kezdeti rendszerindítást, és megosztott hitelesítő adatokat kapnak. A Safari böngésző a kezdeti indítást is elvégezheti, és megosztott hitelesítő adatokat is kap. 

Ha a Microsoft vállalati egyszeri bejelentkezés beépülő modulja még nem rendelkezik megosztott hitelesítő adatokkal, akkor a rendszer a Safari böngészőben, a ASWebAuthenticationSession, a SafariViewController vagy más engedélyezett natív alkalmazáson belül egy Azure AD-URL-címről érkező bejelentkezést próbál meg kérni. 

A következő paraméterek használatával engedélyezheti a jelzőt: 

- **Kulcs**: `browser_sso_interaction_enabled`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

a macOS megköveteli ezt a beállítást, így egységes felhasználói élményt biztosíthat az összes alkalmazásban. az iOS és a iPadOS nem igényli ezt a beállítást, mert a legtöbb alkalmazás a hitelesítő alkalmazást használja a bejelentkezéshez. Azt javasoljuk azonban, hogy engedélyezze ezt a beállítást, mert ha néhány alkalmazás nem használja a hitelesítő alkalmazást iOS-vagy iPadOS, ez a jelző javítja majd a felhasználói élményt. A beállítás alapértelmezés szerint le van tiltva.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Az MFA kérésének letiltása a kezdeti rendszerindítás során

Alapértelmezés szerint a Microsoft vállalati egyszeri bejelentkezés beépülő modulja a kezdeti rendszerindítás során mindig felszólítja a felhasználót az MFA-ra, miközben megosztott hitelesítő adatokat kap. A rendszer akkor is kéri a felhasználót, ha nem szükséges a felhasználó által megnyitott alkalmazáshoz. Ez a viselkedés lehetővé teszi, hogy a megosztott hitelesítő adatok könnyen használhatók legyenek az összes többi alkalmazásban anélkül, hogy a felhasználót fel kellene kérni, ha később szükség van az MFA használatára. Mivel a felhasználó kevesebb kérést kap, ez a beállítás általában jó döntés.

`browser_sso_disable_mfa`Az engedélyezés kikapcsolja az MFA-t a kezdeti rendszerindítás során, és a megosztott hitelesítő adatok beolvasását. Ebben az esetben a rendszer csak akkor kéri a felhasználót, ha az MFA-t egy alkalmazás vagy erőforrás igényli. 

A jelző engedélyezéséhez használja a következő paramétereket:

- **Kulcs**: `browser_sso_disable_mfa`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Azt javasoljuk, hogy ezt a jelzőt ne tiltsa le, mert csökkenti annak a számnak a számát, amikor a rendszer a felhasználótól kéri a bejelentkezést. Ha a szervezete ritkán használ MFA-t, érdemes lehet engedélyezni a jelzőt. Javasoljuk azonban, hogy az MFA-t gyakrabban használja helyette. Emiatt a jelző alapértelmezés szerint le van tiltva.

#### <a name="disable-oauth-2-application-prompts"></a>A 2. OAuth-alkalmazás letiltása

A Microsoft Enterprise SSO beépülő modulja egyszeri bejelentkezést biztosít a megosztott hitelesítő adatoknak az engedélyezett alkalmazásokból származó hálózati kérelmekhez való hozzáfűzésével. Előfordulhat azonban, hogy egyes OAuth 2 alkalmazás helytelenül kényszeríti a végfelhasználói kéréseket a protokoll rétegében. Ha ezt a problémát látja, azt is láthatja, hogy a megosztott hitelesítő adatokat a rendszer figyelmen kívül hagyja az alkalmazások esetében. A rendszer arra kéri a felhasználót, hogy jelentkezzen be annak ellenére, hogy a Microsoft Enterprise SSO beépülő modul más alkalmazásokhoz is működik.  

A jelző engedélyezése lehetővé teszi a `disable_explicit_app_prompt` natív alkalmazások és a webalkalmazások számára a végfelhasználói kérések kényszerítését a protokoll rétegén és az egyszeri bejelentkezés mellőzését. A jelző engedélyezéséhez használja a következő paramétereket:

- **Kulcs**: `disable_explicit_app_prompt`
- **Írja be a következőt**: `Integer`
- **Érték**: 1 vagy 0

Javasoljuk, hogy ezzel a jelzővel egységes felhasználói élményt kapjon az összes alkalmazáson belül. Alapértelmezés szerint le van tiltva. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Egyszeri bejelentkezés engedélyezése a cookie-kkal egy adott alkalmazáshoz

Előfordulhat, hogy néhány alkalmazás nem kompatibilis az SSO-bővítménnyel. Konkrétan a speciális hálózati beállításokkal rendelkező alkalmazások váratlan problémákba ütközhet, amikor engedélyezve vannak az egyszeri bejelentkezéshez. Előfordulhat például, hogy egy hibaüzenet jelenik meg, amely azt jelzi, hogy a hálózati kérést megszakították vagy megszakították. 

Ha a [MSAL nem használó alkalmazásokban](#applications-that-dont-use-msal) leírt módszer használatával jelentkezik be a bejelentkezésbe, próbálkozzon egy másik konfigurációval. A beépülő modul konfigurálásához használja ezeket a paramétereket:

- **Kulcs**: `AppCookieSSOAllowList`
- **Írja be a következőt**: `String`
- **Érték**: az ALKALMAZÁSCSOMAG-azonosító előtagjainak vesszővel tagolt listája azon alkalmazások esetében, amelyek jogosultak az SSO-ban való részvételre. A felsorolt előtagokkal kezdődő alkalmazások is részt vehetnek az SSO-ban.
- **Példa**: `com.contoso.myapp1, com.fabrikam.myapp2`

Az egyszeri bejelentkezéshez a telepítő használatával engedélyezett alkalmazásokat is hozzá kell adni a és a rendszerhez `AppCookieSSOAllowList` `AppPrefixAllowList` .

Ezt a konfigurációt csak váratlan bejelentkezési hibákkal rendelkező alkalmazások esetében próbálja meg. 

#### <a name="use-intune-for-simplified-configuration"></a>Az Intune használata egyszerűsített konfigurációhoz

Az Intune-t használhatja MDM szolgáltatásként a Microsoft vállalati egyszeri bejelentkezés beépülő moduljának konfigurálásához. Az Intune-nal például engedélyezheti a beépülő modult, és hozzáadhat régi alkalmazásokat egy engedélyezési, így azok egyszeri bejelentkezést kapnak. 

További információt az [Intune konfigurációs dokumentációjában](/intune/configuration/ios-device-features-settings)talál.

## <a name="use-the-sso-plug-in-in-your-application"></a>Az alkalmazásban található SSO beépülő modul használata

A [MSAL for Apple Devices](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0-es és újabb verziói támogatják az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult. Ez az ajánlott módszer a Microsoft Enterprise SSO beépülő modul támogatásának hozzáadására. Gondoskodik arról, hogy a Microsoft Identity platform összes funkciója elérhető legyen.

Ha a kiépítési feldolgozói forgatókönyvekhez hoz létre alkalmazást, tekintse meg a telepítési információkat az [iOS-eszközök megosztott eszköz üzemmódjában](msal-ios-shared-devices.md) .

## <a name="understand-how-the-sso-plug-in-works"></a>Az SSO beépülő modul működésének megismerése

A Microsoft Enterprise SSO beépülő modul az [Apple Enterprise SSO keretrendszerre](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)támaszkodik. A keretrendszerhez csatlakozó identitás-szolgáltatók képesek a tartományok hálózati forgalmának elfogására és a kérelmek kezelésének javítására vagy módosítására. Például az egyszeri bejelentkezés beépülő modul több felhasználói felületet jeleníthet meg a végfelhasználói hitelesítő adatok biztonságos összegyűjtéséhez, a többtényezős hitelesítés megkövetelése vagy a tokenek csendes megadása az alkalmazás számára.

A natív alkalmazások is végrehajthatják az egyéni műveleteket, és közvetlenül kommunikálhatnak az egyszeri bejelentkezés beépülő modullal. További információ: az [Apple-től az 2019-es Worldwide fejlesztői konferencia videója](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>MSAL használó alkalmazások

A [MSAL for Apple-eszközök](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0-es és újabb verziói támogatják a Microsoft Enterprise SSO beépülő modult az Apple-eszközökön natív módon munkahelyi és iskolai fiókokhoz. 

Ha követte az [összes javasolt lépést](./quickstart-v2-ios.md) , és az alapértelmezett [átirányítási URI-formátumot](./redirect-uris-ios.md)használta, nincs szükség speciális konfigurációra. Az SSO beépülő modullal rendelkező eszközökön a MSAL automatikusan meghívja az összes interaktív és csendes jogkivonat-kérelemre. Emellett a fiókok enumerálása és a fiókok eltávolítására szolgáló műveletek is meghívja őket. Mivel a MSAL olyan natív egyszeri bejelentkezéses beépülő modult valósít meg, amely egyéni műveletekre támaszkodik, ez a telepítő a legsimább natív élményt biztosítja a végfelhasználók számára. 

Ha az egyszeri bejelentkezés beépülő modul nincs engedélyezve a MDM-ben, de a Microsoft Authenticator alkalmazás megtalálható az eszközön, a MSAL Ehelyett a hitelesítő alkalmazást használja az interaktív jogkivonat-kérelmekhez. Az SSO beépülő modul az SSO-t a hitelesítő alkalmazással osztja meg.

### <a name="applications-that-dont-use-msal"></a>MSAL nem használó alkalmazások

Azok az alkalmazások, amelyek nem használnak Microsoft Identity platform-függvénytárat (például MSAL), továbbra is kaphatnak SSO-t, ha a rendszergazda hozzáadja ezeket az alkalmazásokat a engedélyezési. 

Ezeket az alkalmazásokat nem kell módosítania, amíg teljesülnek a következő feltételek:

- Az alkalmazás Apple frameworks használatával futtatja a hálózati kérelmeket. Ezek a keretrendszerek például a [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) és a [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)is tartalmazzák. 
- Az alkalmazás szabványos protokollokat használ az Azure AD-vel való kommunikációhoz. A protokollok közé tartoznak például a OAuth 2, az SAML és a WS-Federation.
- Az alkalmazás nem gyűjt szöveges felhasználóneveket és jelszavakat a natív felhasználói felületen.

Ebben az esetben az SSO akkor van megadva, amikor az alkalmazás létrehoz egy hálózati kérelmet, és megnyit egy webböngészőt a felhasználó aláírásához. Amikor egy felhasználó átirányítja az Azure AD bejelentkezési URL-címére, az SSO beépülő modul ellenőrzi az URL-címet, és ellenőrzi az adott URL-címhez tartozó egyszeri bejelentkezéshez használt hitelesítő adatokat. Ha megtalálta a hitelesítő adatokat, az SSO beépülő modul átadja az Azure AD-nek, amely engedélyezi az alkalmazásnak a hálózati kérelem elvégzését anélkül, hogy a felhasználónak meg kellene adnia a hitelesítő adatokat. Továbbá, ha az eszköz ismert az Azure AD-ben, az SSO beépülő modul továbbítja az eszköz tanúsítványát az eszköz alapú feltételes hozzáférés-ellenőrzések teljesítéséhez. 

A nem MSAL alkalmazások egyszeri bejelentkezésének támogatásához az SSO beépülő modul olyan protokollt valósít meg, amely a [Mi az elsődleges frissítési jogkivonat?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)című témakörben ismertetett Windows-böngésző beépülő modulhoz hasonló. 

A MSAL-alapú alkalmazásokhoz képest az SSO beépülő modul transzparens módon működik a nem MSAL alkalmazások esetében. Integrálható az alkalmazások által biztosított meglévő böngészőbeli bejelentkezési felülettel. 

A végfelhasználó látja a megszokott élményt, és nem kell újra bejelentkeznie az egyes alkalmazásokban. A natív fiók választójának megjelenítése helyett például az SSO beépülő modul SSO-munkameneteket biztosít a webalapú fiók-választói élményhez. 

## <a name="next-steps"></a>Következő lépések

[Az iOS-eszközök megosztott eszköz üzemmódjának](msal-ios-shared-devices.md)ismertetése.
