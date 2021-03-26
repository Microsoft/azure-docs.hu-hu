---
title: Egyéni szabályzatok fejlesztői megjegyzései
titleSuffix: Azure AD B2C
description: Megjegyzések a Azure AD B2C egyéni szabályzatokkal történő konfigurálásához és karbantartásához szükséges fejlesztőknek.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c175a6d225be268f27854b9ab63886892cf029fb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557275"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Fejlesztői megjegyzések a Azure Active Directory B2C egyéni házirendjeihez

A Azure Active Directory B2C egyéni házirend-konfigurációja már általánosan elérhető. Ez a konfigurációs módszer speciális identitás-fejlesztőknek készült komplex Identity-fejlesztők számára van megcélozva. Az egyéni szabályzatok a Azure AD B2C bérlők számára elérhető Identity Experience keretrendszer hatékonyságát teszik hatékonyabbá.
Az egyéni szabályzatokat használó speciális identitás-fejlesztőknek meg kell tervezniük egy kis idő betöltését és a tájékoztató dokumentumok olvasását.

Habár a rendelkezésre álló egyéni házirend-lehetőségek többsége már általánosan elérhető, olyan mögöttes képességekkel rendelkezik, mint például a technikai profilok típusai és a tartalmi definíciós API-k, amelyek a szoftver életciklusának különböző szakaszaiban találhatók. Számos további újdonság. Az alábbi táblázat a rendelkezésre állás szintjét határozza meg részletesebb szinten.

## <a name="features-that-are-generally-available"></a>Általánosan elérhető funkciók

- Egyéni hitelesítési felhasználói utazások létrehozása és feltöltése egyéni szabályzatok használatával.
    - A felhasználói utazások részletes leírását a jogcímek szolgáltatói közötti váltással.
    - Feltételes elágazás definiálása felhasználói útvonalakon.
- A REST API-kompatibilis szolgáltatásokkal való együttműködés az egyéni hitelesítési felhasználói útvonalakon.
- A összevonása az OpenIDConnect protokollnak megfelelő identitás-szolgáltatókkal.
- Összevonása az SAML 2,0 protokollnak megfelelő identitás-szolgáltatókkal.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Az egyéni szabályzat funkcióinak beállítása – fejlesztők

A manuális házirend-konfiguráció alacsonyabb szintű hozzáférést biztosít a Azure AD B2C mögöttes platformhoz, és egy egyedi, megbízhatósági keretrendszer létrehozását eredményezi. Az egyéni identitás-szolgáltatók, a megbízhatósági kapcsolatok, a külső szolgáltatásokkal való integrációk és a lépésenkénti munkafolyamatok számos lehetséges variációja szükséges a tervezéshez és a konfiguráláshoz.

Az egyéni házirend-készletet használó fejlesztőknek be kell tartaniuk a következő irányelveket:

- Ismerkedjen meg az egyéni szabályzatok és a kulcs/titkok kezelésének konfigurációs nyelvével. További információ: [TrustFrameworkPolicy](trustframeworkpolicy.md).
- A forgatókönyvek és az egyéni integrációk saját tulajdonba vételét. Dokumentálja a munkáját, és tájékoztassa az élő hely szervezetét.
- Végezzen tesztelési módszert.
- Kövesse a szoftverfejlesztés és az előkészítéssel kapcsolatos ajánlott eljárásokat. Legalább egy fejlesztési és tesztelési környezetet ajánlott használni.
- Tájékozódjon arról, hogy az Ön által integrált identitás-szolgáltatók és szolgáltatások milyen új fejleményeket mutatnak be. Például nyomon követheti a titkok és a szolgáltatás ütemezett és nem ütemezett változásainak változásait.
- Állítsa be az aktív figyelést, és figyelje az éles környezetek érzékenységét. További információ a Application Insights integrálásáról [: Azure Active Directory B2C: naplók gyűjtése](analytics-with-application-insights.md).
- Lépjen kapcsolatba az Azure-előfizetésben aktuálisan használt e-mail címekkel, és válaszoljon a Microsoft Live-site Team-e-mailekre.
- Ha a Microsoft Live-site csapata ezt javasolja, végezze el a szükséges lépéseket.

## <a name="terms-for-features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók használati feltételei

- Javasoljuk, hogy csak kiértékelési célokra használja a nyilvános előzetes verzió funkcióit.
- A szolgáltatói szerződések (SLA-kat) nem vonatkoznak a nyilvános előzetes verzió szolgáltatásaira.
- A nyilvános előzetes verziójú funkciókra vonatkozó támogatási kérelmeket rendszeres támogatási csatornákon keresztül lehet benyújtani.

## <a name="features-by-stage-and-known-issues"></a>Funkciók szakasz és ismert problémák alapján

Az egyéni házirend-képességek állandó fejlesztés alatt állnak. A következő táblázat a szolgáltatások és az összetevők rendelkezésre állásának indexét tartalmazza.


### <a name="protocols-and-authorization-flows"></a>Protokollok és engedélyezési folyamatok

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2-engedélyezési kód](authorization-code-flow.md) |  |  | X |  |
| OAuth2 engedélyezési kód a PKCE |  |  | X | [Nyilvános ügyfelek és egyoldalas alkalmazások](authorization-code-flow.md)  |
| [OAuth2 implicit folyamat](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 erőforrás-tulajdonos jelszavas hitelesítő adatai](add-ropc-policy.md) |  | X |  |  |
| [OIDC-kapcsolat](openid-connect.md) |  |  | X |  |
| [EGY SAML2](saml-service-provider.md)  |  |  |X  | Kötések közzététele és átirányítása. |
| OAuth1 |  |  |  | Nem támogatott. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Szolgáltatók összevonásának azonosítása 

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Például: Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Például: Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Például: Twitter. |
| [EGY SAML2](identity-provider-generic-saml.md) |  |   | X | Például: Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API integráció

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API egyszerű hitelesítéssel](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API ügyféltanúsítvány-alapú hitelesítéssel](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API OAuth2 tulajdonosi hitelesítéssel](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Összetevő-támogatás

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Telefonos faktoros hitelesítés](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure AD MFA-hitelesítés](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Egyszeri jelszó](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) helyi könyvtárként |  |  | X |  |
| E-mail-ellenőrzésre szolgáló Azure e-mail alrendszer |  |  | X |  |
| [Harmadik féltől származó e-mail szolgáltatók](custom-email-mailjet.md) |  |X  |  |  |
| [Többnyelvű támogatás](localization.md)|  |  | X |  |
| [Predikátumok érvényessége](predicates.md) |  |  | X | Például a jelszó bonyolultsága. |
| [Vezérlőelemek megjelenítése](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>IEF-integráció

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
| ------- | :-----------: | :-------: | :--: | ----- |
| Lekérdezési karakterlánc paraméter `domain_hint` |  |  | X | A jogcímként elérhető lehet a IDENTITÁSSZOLGÁLTATÓ. |
| Lekérdezési karakterlánc paraméter `login_hint` |  |  | X | A jogcímként elérhető lehet a IDENTITÁSSZOLGÁLTATÓ. |
| JSON beszúrása felhasználói útra a használatával `client_assertion` | X |  |  | Elavulttá válik. |
| JSON beszúrása a felhasználói útra `id_token_hint` |  | X |  | Továbbítási megközelítés a JSON továbbítására. |
| [Identitás-szolgáltatói token átadása az alkalmazásnak](idp-pass-through-user-flow.md) |  | X |  | Például a Facebookról az alkalmazásra. |


### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Alapértelmezett egyszeri bejelentkezés munkamenet-szolgáltatója](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Külső bejelentkezési munkamenet-szolgáltató](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-munkamenet-szolgáltató](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Egyszeri kijelentkezés](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Biztonság

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
|-------- | :-----------: | :-------: | :--: | ----- |
| Házirend-kulcsok – generál, manuális, feltöltés |  |  | X |  |
| Szabályzat kulcsai – RSA/CERT, Secrets |  |  | X |  |


### <a name="developer-interface"></a>Fejlesztői felület

| Szolgáltatás | Fejlesztés | Előnézet | FE | Jegyzetek |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal – IEF UX |  |  | X |  |
| Szabályzat feltöltése |  |  | X |  |
| [Felhasználói Application Insightsi naplók](troubleshoot-with-application-insights.md) |  | X |  | A fejlesztés során hibaelhárításra szolgál.  |
| [Eseménynaplók Application Insights](analytics-with-application-insights.md) |  | X |  | Az élesben használt felhasználói folyamatok figyelésére szolgál. |


## <a name="next-steps"></a>Következő lépések

- Győződjön [meg arról, hogy elérhetők-e a Microsoft Graph műveletek Azure ad B2C](microsoft-graph-operations.md)
- További információ [az egyéni házirendekről és a felhasználói folyamatokkal kapcsolatos különbségekről](custom-policy-overview.md).
