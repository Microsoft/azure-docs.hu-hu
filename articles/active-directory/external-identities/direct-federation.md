---
title: Közvetlen összevonás egy identitásszolgáltatóval B2B-hez – Azure AD
description: Közvetlenül összevont egy SAML- vagy WS-Fed-identitásszolgáltatóval, így a vendégek bejelentkeznek az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830119a5b3a7781e8b12e3d4df870f539a2cd63a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364906"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Közvetlen összevonás vendégfelhasználók AD FS külső szolgáltatókkal (előzetes verzió)

> [!NOTE]
>  A közvetlen összevonás a nyilvános előzetes verzió egyik Azure Active Directory. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk bemutatja, hogyan állíthat be közvetlen összevonást egy másik szervezettel a B2B-együttműködéshez. Közvetlen összevonást állíthat be bármely olyan szervezettel, amelynek identitásszolgáltatója (IdP) támogatja az SAML 2.0 vagy WS-Fed protokollt.
Amikor közvetlen összevonást hoz létre egy partner idP-jával, az új vendégfelhasználók az ebben a tartományban a saját, idP által felügyelt szervezeti fiókjukkal jelentkezhetnek be az Azure AD-bérlőbe, és együttműködhet velük. A vendégfelhasználónak nem kell külön Azure AD-fiókot létrehoznia.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Mikor hitelesíti a vendégfelhasználót közvetlen összevonással?
A szervezettel való közvetlen összevonás beállítása után a meghívott új vendégfelhasználók közvetlen összevonással lesznek hitelesítve. Fontos megjegyezni, hogy a közvetlen összevonás beállítása nem módosítja a hitelesítési módszert az olyan vendégfelhasználóknál, akik már beváltják az Ön meghívását. Íme néhány példa:
 - Ha a vendégfelhasználók már beváltják az Ön meghívását, és ezt követően közvetlen összevonást hoz létre a szervezettel, ezek a vendégfelhasználók továbbra is ugyanazt a hitelesítési módszert fogják használni, mint amelyet a közvetlen összevonás beállítása előtt használtak.
 - Ha közvetlen összevonást hoz létre egy partnerszervezettel, és vendégfelhasználókat hív meg, majd a partnerszervezet később az Azure AD-be költözik, a meghívókat már beváltó vendégfelhasználók továbbra is a közvetlen összevonást fogják használni, ha a bérlőben létezik a közvetlen összevonási szabályzat.
 - Ha töröl közvetlen összevonást egy partnerszervezettel, a jelenleg közvetlen összevonást használó vendégfelhasználók nem fognak tudni bejelentkezni.

Ezen forgatókönyvek bármelyikében frissítheti a vendégfelhasználó hitelesítési módszerét az érvényesítési [állapot visszaállításával.](reset-redemption-status.md)

A közvetlen összevonás tartománynévterekkel van összekötve, például a contoso.com és fabrikam.com. Amikor közvetlen összevonási konfigurációt hoznak létre AD FS vagy egy külső internetszolgáltatóval, a szervezetek egy vagy több tartománynévteret társítanának ezekhez az azonosítókhoz. 

## <a name="end-user-experience"></a>Végfelhasználói élmény 
Közvetlen összevonás esetén a vendégfelhasználók a saját szervezeti fiókjukkal jelentkeznek be az Azure AD-bérlőbe. Amikor megosztott erőforrásokhoz férnek hozzá, és a rendszer bejelentkezést kér, a rendszer átirányítja a közvetlen összevonási felhasználókat az internetszolgáltatójukhoz. A sikeres bejelentkezés után a rendszer visszaküldi őket az Azure AD-nek az erőforrások eléréséhez. A közvetlen összevonási felhasználók frissítési jogkivonatai 12 óráig érvényesek, az Azure AD-ben az áttért frissítési [jogkivonatok](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) alapértelmezett hossza. Ha az összevont idP-n engedélyezve van az SSO, a felhasználó SSO-t fog tapasztalni, és a kezdeti hitelesítés után nem fog bejelentkezési kérést látni.

## <a name="sign-in-endpoints"></a>Bejelentkezési végpontok

A közvetlen összevonási vendégfelhasználók mostantól bejelentkeznek [a](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) több-bérlős vagy a Microsoft saját alkalmazásaiba egy közös végpont használatával (vagyis egy általános alkalmazás URL-címével, amely nem tartalmazza a bérlői környezetet). A bejelentkezési folyamat során a vendégfelhasználó a Bejelentkezési lehetőségeket választja, majd a Bejelentkezés egy **szervezetbe lehetőséget.** A felhasználó ezután be írja a szervezet nevét, és folytatja a bejelentkezést a saját hitelesítő adataival.

A közvetlen összevonási vendégfelhasználók olyan alkalmazásvégpontokat is használhatnak, amelyek tartalmazzák a bérlő adatait, például:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Közvetlen összevonási vendégfelhasználóknak is adhat közvetlen hivatkozást egy alkalmazásra vagy erőforrásra a bérlői adatok (például ) megszava. `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`

## <a name="limitations"></a>Korlátozások

### <a name="dns-verified-domains-in-azure-ad"></a>DNS által ellenőrzött tartományok az Azure AD-ben
Az a tartomány, amelybe az összevont tartományt szeretné, nem ***lehet*** DNS-ellenőrzéssel ellenőrizni az Azure AD-ben. Közvetlen összevonást állíthat be nem hitelesített (e-mailben ellenőrzött vagy "vírusos") Azure AD-bérlőkhöz, mert azok nem DNS-ellenőrzés alatt áll.

### <a name="authentication-url"></a>Hitelesítési URL-cím
Közvetlen összevonás csak olyan szabályzatok esetében engedélyezett, amelyek hitelesítési URL-tartománya megegyezik a céltartománysal, vagy amelyek hitelesítési URL-címe ezen engedélyezett identitásszolgáltatók egyike (ez a lista változhat):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ha például közvetlen összevonást ad meg a **fabrikam.com,** a hitelesítési `https://fabrikam.com/adfs` URL-cím át fogja menni az ellenőrzésen. Egy azonos tartományban található gazdagép is át lesz adhatja például a következőt: `https://sts.fabrikam.com/adfs` . A hitelesítési URL-cím vagy ugyanannak a tartománynak `https://fabrikamconglomerate.com/adfs` azonban nem fog `https://fabrikam.com.uk/adfs` átmenni.

### <a name="signing-certificate-renewal"></a>Aláíró tanúsítvány megújítása
Ha megadja a metaadatok URL-címét az identitásszolgáltató beállításaiban, az Azure AD automatikusan megújítja az aláíró tanúsítványt, amikor az lejár. Ha azonban a tanúsítványt bármilyen okból a lejárati idő előtt váltják le, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem tudja megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

### <a name="limit-on-federation-relationships"></a>Összevonási kapcsolatok korlátozása
Jelenleg legfeljebb 1000 összevonási kapcsolat támogatott. Ez a korlát magában foglalja a [belső és](/powershell/module/msonline/set-msoldomainfederationsettings) a közvetlen összevonásokat is.

### <a name="limit-on-multiple-domains"></a>Több tartomány korlátozása
Jelenleg nem támogatjuk a közvetlen összevonást egy bérlő több tartományával.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Be tudok állítani közvetlen összevonást egy olyan tartománnyal, amelyhez nem hitelesített (e-mailben ellenőrzött) bérlő található? 
Igen. Ha a tartomány nem lett ellenőrizve, és [a](../enterprise-users/domains-admin-takeover.md)bérlő nem került rendszergazdai feladatátvételre, közvetlen összevonást állíthat be ezzel a tartománnyal. A nem vagy e-mailben ellenőrzött bérlők akkor vannak létrehozva, amikor egy felhasználó bevált egy B2B-meghívót, vagy önkiszolgáló regisztrációt hajt végre az Azure AD-re egy olyan tartomány használatával, amely jelenleg nem létezik. Ezekkel a tartományokkal közvetlen összevonást állíthat be. Ha egy DNS által ellenőrzött tartománnyal próbál közvetlen összevonást beállítani a Azure Portal vagy a PowerShell használatával, hibaüzenet jelenik meg.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Ha a közvetlen összevonás és az e-mailes egyszeres PIN-kódos hitelesítés is engedélyezve van, melyik módszer élvez elsőbbséget?
Ha közvetlen összevonást hoznak létre egy partnerszervezettel, az elsőbbséget élvez az e-mailes, egyszer történő PIN-kódos hitelesítéssel az adott szervezet új vendégfelhasználói számára. Ha egy vendégfelhasználó egyszeres PIN-kódos hitelesítéssel beváltott egy meghívót a közvetlen összevonás beállítása előtt, továbbra is az egyszeres PIN-kódos hitelesítést fogja használni. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A közvetlen összevonás a részlegesen szinkronizált élettartam miatti bejelentkezési problémákat is megoldja?
Nem, ebben a forgatókönyvben az e-mailes egyszer használt [PIN-kódot](one-time-passcode.md) kell használni. A "részlegesen szinkronizált bérlő" egy partner Azure AD-bérlőre vonatkozik, ahol a helyszíni felhasználói identitások nincsenek teljesen szinkronizálva a felhővel. Az olyan vendég, akinek az identitása még nem létezik a felhőben, de megpróbálja beváltani a B2B-meghívót, nem fog tudni bejelentkezni. Az egyszer használható PIN-kód funkció lehetővé teszi a vendég számára a bejelentkezést. A közvetlen összevonási funkció olyan helyzeteket szolgál ki, ahol a vendég saját, idP által felügyelt szervezeti fiókkal rendelkezik, de a szervezet egyáltalán nem rendelkezik Azure AD-jelenléttel.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Miután konfigurálta a közvetlen összevonást egy szervezettel, minden vendéget el kell küldeni, és egyéni meghívót kell beváltani?
A közvetlen összevonás beállítása nem módosítja az olyan vendégfelhasználók hitelesítési módszerét, akik már beváltottak egy meghívót Öntől. A vendégfelhasználó hitelesítési módszerét úgy frissítheti, hogy [visszaállítja az érvényesítési állapotukat.](reset-redemption-status.md)
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1. lépés: A partnerszervezet identitásszolgáltatójának konfigurálása
Először is a partnerszervezetnek konfigurálnia kell az identitásszolgáltatóját a szükséges jogcímekkel és a függő fél megbízhatóságával. 

> [!NOTE]
> Annak szemléltetésére, hogyan konfigurálható egy identitásszolgáltató a közvetlen összevonáshoz, Active Directory összevonási szolgáltatások (AD FS) (AD FS) használjuk. Tekintse meg a Közvetlen összevonás konfigurálása a [AD FS-val](direct-federation-adfs.md)cikket, amely példákat tartalmaz arra, hogyan konfigurálható az AD FS SAML 2.0-s vagy WS-Fed-identitásszolgáltatóként a közvetlen összevonás előkészítése érdekében.

### <a name="saml-20-configuration"></a>SAML 2.0-konfiguráció

Az Azure AD B2B konfigurálható úgy, hogy az SAML protokollt használják identitásszolgáltatókkal egyesítődve az alább felsorolt konkrét követelményekkel. További információ az SAML-identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat beállításával kapcsolatban:  [SAML 2.0](../hybrid/how-to-connect-fed-saml-idp.md)identitásszolgáltató használata egyszeri bejelentkezéshez.  

> [!NOTE]
> A közvetlen összevonás céltartományát nem lehet DNS-ellenőrzésen átesni az Azure AD-ben. A hitelesítési URL-tartománynak egyeznie kell a céltartománysal, vagy egy engedélyezett identitásszolgáltató tartományának kell lennie. További [részleteket a Korlátozások](#limitations) szakaszban talál. 

#### <a name="required-saml-20-attributes-and-claims"></a>Szükséges SAML 2.0-attribútumok és jogcímek
Az alábbi táblázatok a külső identitásszolgáltatónál konfigurált attribútumok és jogcímek követelményeit mutatják be. A közvetlen összevonás beállításához a következő attribútumokat kell megkapni az identitásszolgáltatóTÓL kapott SAML 2.0-válaszban. Ezek az attribútumok az online biztonsági jogkivonat-szolgáltatás XML-fájlját összekapcsolva vagy manuálisan megadásával konfigurálhatóak.

Az idP SAML 2.0-válaszához szükséges attribútumok:

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partnerazonosító kiállítói URI-ja, például `http://www.example.com/exk10l6w90DHM0yi...`         |


Az idP által kiadott SAML 2.0-jogkivonathoz szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|NameID formátum     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress (e-mail-cím)     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfigurálása 
Az Azure AD B2B konfigurálható úgy, hogy az WS-Fed protokollt WS-Fed identitásszolgáltatókkal egyes, az alábbiakban felsorolt követelményekkel egyes követelményeket összeváljon. Jelenleg a két WS-Fed tesztelve lett az Azure AD-val való kompatibilitásra, többek között a AD FS és a Shibboleth. Az Azure AD-val kompatibilis WS-Fed-szolgáltató közötti megbízható függő fél létrehozásával kapcsolatos további információkért lásd az Azure AD identity [provider compatibility docs](https://www.microsoft.com/download/details.aspx?id=56843)(Az Azure AD identitásszolgáltatói kompatibilitási dokumentumokban található STS integration paper using WS Protocols (STS-integráció a WS-protokollok használatával) dokumentumban található.

> [!NOTE]
> A közvetlen összevonás céltartományát nem lehet DNS-ellenőrzésen átesni az Azure AD-ben. A hitelesítési URL-cím tartományának egyeznie kell egy engedélyezett identitásszolgáltató céltartományával vagy tartományával. További [részleteket a Korlátozások](#limitations) szakaszban talál. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Szükséges WS-Fed attribútumok és jogcímek

Az alábbi táblázatok a külső féltől származó identitásszolgáltatónál konfigurált konkrét attribútumok és jogcímek követelményeit WS-Fed mutatják be. A közvetlen összevonás beállításához a következő attribútumokat kell megkapni az WS-Fed az identitásszolgáltatótól. Ezek az attribútumok az online biztonsági jogkivonat-szolgáltatás XML-fájlra mutató hivatkozásával vagy manuális megadásával konfigurálhatóak.

Az üzenetben kötelező WS-Fed az azonosítóból:
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partnerazonosító kiállítói URI-ja, például `http://www.example.com/exk10l6w90DHM0yi...`         |

Az internetszolgáltató által WS-Fed jogkivonathoz szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|ImmutableID (Nem módosíthatóid)     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|e-mail-cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2. lépés: Közvetlen összevonás konfigurálása az Azure AD-ben 
Ezután konfigurálja az összevonást az Azure AD 1. lépésében konfigurált identitásszolgáltatóval. Az Azure AD portált vagy a PowerShellt is használhatja. A közvetlen összevonási szabályzat 5–10 percet is igénybe vehet. Ez idő alatt ne próbálja meg beváltani a közvetlen összevonási tartományra vonatkozó meghívót. A következő attribútumok szükségesek:
- A partnerazonosító kiállítói URI-ja
- A partnerazonosító passzív hitelesítési végpontja (csak a https támogatott)
- Tanúsítvány

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Közvetlen összevonás konfigurálása az Azure AD portálon

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza **a External Identities** Minden  >  **identitásszolgáltató lehetőséget.**
3. Válassza **az Új SAML/WS-Fed idP lehetőséget.**

    ![Új SAML- vagy WS-Fed gomb képernyőképe](media/direct-federation/new-saml-wsfed-idp.png)

4. Az Új **SAML/WS-Fed identitásszolgáltató** lap Identitásszolgáltató protokollja alatt válassza az **SAML** vagy **a WS-FED lehetőséget.**

    ![Képernyőkép az SAML- vagy WS-Fed elemzési gombról](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Adja meg a partnerszervezet tartománynevét, amely a közvetlen összevonás céltartományneve lesz
6. Feltölthet egy metaadatfájlt a metaadatok részleteinek feltöltéséhez. Ha manuálisan adja meg a metaadatokat, adja meg a következő adatokat:
   - A partnerazonosító tartományneve
   - A partnerazonosító entitásazonosítója
   - A partnerazonosító passzív kérelmezői végpontja
   - Tanúsítvány
   > [!NOTE]
   > A metaadatok URL-címe nem kötelező, de erősen ajánlott. Ha a metaadatok URL-címét adja meg, az Azure AD automatikusan megújíthatja az aláíró tanúsítványt, amikor az lejár. Ha a tanúsítványt bármilyen okból a lejárati idő előtt váltják fel, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem fogja tudni megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

7. Kattintson a **Mentés** gombra. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Közvetlen összevonás konfigurálása az Azure AD-ban a PowerShell használatával

1. Telepítse az Azure AD PowerShell for Graph modul legújabb verzióját ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Ha részletes lépésekre van szüksége, a vendégfelhasználó hozzáadásának rövid útmutatója tartalmazza a legújabb [AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)modul telepítése című szakaszt.) 
2. Futtassa az alábbi parancsot: 
   ```powershell
   Connect-AzureAD
   ```
1. A bejelentkezési kérésnél jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
2. Futtassa az alábbi parancsokat, és cserélje le az összevonási metaadat-fájlban található értékeket. A AD FS Server és az Okta számára az összevonási fájl federationmetadata.xml, például: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3. lépés: Közvetlen összevonás tesztelése az Azure AD-ben
Most tesztelje a közvetlen összevonás beállítását egy új B2B-vendégfelhasználó meghívása által. Részletekért lásd: [Azure AD B2B együttműködési felhasználók hozzáadása a Azure Portal.](add-users-administrator.md)
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hogyan a közvetlen összevonási kapcsolatot?

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **External Identities** lehetőséget.
3. Válassza a **Minden identitásszolgáltató lehetőséget**
4. Az **SAML/WS-Fed identitásszolgáltatók alatt** válassza ki a szolgáltatót.
5. Az identitásszolgáltató részleteit tartalmazó panelen frissítse az értékeket.
6. Kattintson a **Mentés** gombra.


## <a name="how-do-i-remove-direct-federation"></a>Hogyan a közvetlen összevonást?
Eltávolíthatja a közvetlen összevonási beállítást. Ha így van, a meghívókat már beváltó közvetlen összevonási vendégfelhasználók nem fognak tudni bejelentkezni. Azonban ismét hozzáférést adhat nekik az erőforrásokhoz az érvényesítési [állapot alaphelyzetbe állításával.](reset-redemption-status.md) Identitásszolgáltatóval való közvetlen összevonás eltávolítása az Azure AD portálon:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **External Identities** lehetőséget.
3. Válassza a **Minden identitásszolgáltató lehetőséget.**
4. Válassza ki az identitásszolgáltatót, majd válassza a **Törlés lehetőséget.** 
5. Válassza az **Igen lehetőséget** a törlés megerősítéséhez. 

Az identitásszolgáltatóval való közvetlen összevonás eltávolítása a PowerShell használatával:
1. Telepítse az Azure AD PowerShell for Graph modul legújabb verzióját ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Futtassa az alábbi parancsot: 
   ```powershell
   Connect-AzureAD
   ```
3. A bejelentkezési kérésnél jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
4. Írja be a következő parancsot:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Következő lépések

További információ a meghívó [beváltási élményről,](redemption-experience.md) amikor külső felhasználók jelentkeznek be különböző identitásszolgáltatókkal.
