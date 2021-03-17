---
title: Azure AD-hitelesítés & engedélyezési hibakódok
description: Ismerje meg az Azure AD biztonságijogkivonat-szolgáltatás (STS) által visszaadott AADSTS hibakódokat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 02/01/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 606704621a4904dd0fb7b6f55e753dbe77e39cb5
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601015"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD-hitelesítési és -engedélyezési hibakódok

Az Azure Active Directory (Azure AD) biztonsági jogkivonat-szolgáltatás (STS) által visszaadott AADSTS hibakódok információit keresi? Olvassa el ezt a dokumentumot a AADSTS hibák leírásának, javításának és egyes javasolt megkerülő megoldásoknak a megkereséséhez.

> [!NOTE]
> Ezek az információk előzetesek, és változhat a tartalmuk. Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg a [fejlesztők támogatási és támogatási lehetőségeit](./developer-support-help-options.md) , amelyekkel megismerheti a Súgó és támogatás egyéb lehetőségeit.
>
> Ez a dokumentáció a fejlesztői és a rendszergazdai útmutatóhoz készült, de az ügyfél soha nem használhatja. A hibakódok bármikor megváltozhatnak, hogy részletesebb hibaüzeneteket szolgáltasson, amelyek célja, hogy az alkalmazás kiépítése során segítsenek a fejlesztőnek. A szöveg-vagy hibakódokat használó alkalmazások az idő múlásával lesznek megszakítva.

## <a name="handling-error-codes-in-your-application"></a>Hibakódok feldolgozása az alkalmazásban

A [OAuth 2.0 specifikációja](https://tools.ietf.org/html/rfc6749#section-5.2) útmutatást nyújt arról, hogyan kezelheti a hibákat a hitelesítési művelet során a `error` hiba részeként. 

Itt látható egy példa a hibaüzenetre:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Paraméter         | Leírás    |
|-------------------|----------------|
| `error`       | Hibakód-karakterlánc, amely a hibák típusának besorolására szolgál, és a hibákra való reagálásra szolgál. |
| `error_description` | Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. Soha ne használja ezt a mezőt, ha a kódban szereplő hibára reagál. |
| `error_codes` | Az STS-specifikus hibakódok listája, amelyek segíthetnek a diagnosztikaben.  |
| `timestamp`   | Az az idő, amikor a hiba bekövetkezett. |
| `trace_id`    | Az a kérelem egyedi azonosítója, amely segíthet a diagnosztikaben. |
| `correlation_id` | A kérelem egyedi azonosítója, amely segíthet az összetevők diagnosztizálásában. |
| `error_uri` |  A hiba-keresési lapra mutató hivatkozás, amely a hibával kapcsolatos további információkat ismerteti.  Ez csak a fejlesztői használatra vonatkozik, nem jelennek meg a felhasználók számára.  Csak akkor jelennek meg, ha a hiba-keresési rendszeren további információ található a hibával kapcsolatban – nem minden hiba esetén további információ van megadva.|

A `error` mezőnek több lehetséges értéke van – tekintse át a protokoll dokumentációs hivatkozásait és a OAuth 2,0 specifikációkat, hogy többet tudjon meg az adott hibákról (például `authorization_pending` az [eszköz kódjának folyamatában](v2-oauth2-device-code.md)), és hogyan reagáljon rájuk.  Néhány gyakori érték itt látható:

| Hibakód         | Description        | Ügyfél művelete    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollhiba, például hiányzó kötelező paraméter. | Javítsa ki és küldje el újra a kérelmet.|
| `invalid_grant`    | Bizonyos hitelesítési anyagok (Auth-kód, frissítési token, hozzáférési jogkivonat, PKCE Challenge) érvénytelenek voltak, nem elemezhető, hiányzik vagy egyéb módon használhatatlan | Új engedélyezési kód beszerzéséhez próbáljon ki egy új kérelmet a `/authorize` végponthoz.  Érdemes áttekinteni és érvényesíteni az alkalmazás által használt protokollokat. |
| `unauthorized_client` | A hitelesített ügyfél nem jogosult az engedélyezési támogatás típusának használatára. | Ez általában akkor fordul elő, ha az ügyfélalkalmazás nincs regisztrálva az Azure AD-ben, vagy nem kerül be a felhasználó Azure AD-bérlőbe. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez. |
| `invalid_client` | Nem sikerült az ügyfél-hitelesítés.  | Az ügyfél hitelesítő adatai nem érvényesek. A javításhoz az alkalmazás rendszergazdája frissíti a hitelesítő adatokat.   |
| `unsupported_grant_type` | Az engedélyezési kiszolgáló nem támogatja az engedélyezési engedély típusát. | Módosítsa a kérelemben szereplő Grant típust. Ez a típusú hiba csak a fejlesztés során fordul elő, és a rendszer a kezdeti tesztelés során észleli. |
| `invalid_resource` | A célként megadott erőforrás érvénytelen, mert nem létezik, az Azure AD nem találja, vagy helytelenül van konfigurálva. | Ez azt jelzi, hogy az erőforrás (ha létezik) nem lett konfigurálva a bérlőben. Az alkalmazás arra kéri a felhasználót, hogy telepítse az alkalmazást, és hozzáadja azt az Azure AD-hez.  A fejlesztés során ez általában egy helytelenül beállított tesztelési bérlőt vagy egy elírást jelez a kért hatókör nevében. |
| `interaction_required` | A kérés felhasználói beavatkozást igényel. Például további hitelesítési lépésre van szükség. | Próbálja megismételni a kérést ugyanazzal az erőforrással, interaktív módon, hogy a felhasználó minden szükséges kihívással elvégezhető legyen.  |
| `temporarily_unavailable` | A kiszolgáló átmenetileg túl elfoglalt a kérelem kezeléséhez. | Próbálja megismételni a kérelmet. Előfordulhat, hogy az ügyfélalkalmazás megmagyarázza a felhasználót, hogy a válasza egy ideiglenes feltétel miatt késleltetve van. |

## <a name="lookup-current-error-code-information"></a>Aktuális hibakód információinak keresése
A hibakódok és az üzenetek változhatnak.  A legfrissebb információkért tekintse meg az [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) oldalt a AADSTS-hibák leírásának, javításának és néhány javasolt megkerülő megoldás megkereséséhez.  

Ha például a "AADSTS50058" hibakódot kapta, keressen rá a [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) "50058" kifejezésre.  Közvetlenül egy adott hibához is kapcsolódhat, ha hozzáadja a hibakódot az URL-címhez: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>AADSTS-hibakódok

| Hiba | Leírás |
|---|---|
| AADSTS16000 | SelectUserAccount – ez az Azure AD által kiváltott megszakítás, ami olyan felhasználói felületet eredményez, amely lehetővé teszi a felhasználó számára, hogy több érvényes SSO-munkamenet közül válasszon. Ez a hiba meglehetősen gyakori, és ha meg van adva, visszatérhet az alkalmazáshoz `prompt=none` . |
| AADSTS16001 | UserAccountSelectionInvalid – ez a hiba akkor jelenik meg, ha a felhasználó egy olyan csempére kattint, amelyet a munkamenetben a logika elutasította. Ha aktiválódik, ez a hiba lehetővé teszi a felhasználó számára a helyreállítást a csempék/munkamenetek frissített listájából, vagy egy másik fiók kiválasztásával. Ez a hiba a kód hibája vagy a versenyhelyzet miatt fordulhat elő. |
| AADSTS16002 | AppSessionSelectionInvalid – az alkalmazás által megadott SID-követelmény nem teljesült.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant – azt jelzi, hogy a felhasználó nem lett explicit módon hozzáadva a bérlőhöz. |
| AADSTS17003 | CredentialKeyProvisioningFailed – az Azure AD nem tudja kiépíteni a felhasználói kulcsot. |
| AADSTS20001 | WsFedSignInResponseError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20012 | WsFedMessageInvalid – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20033 | FedMetadataInvalidTenantName – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40008 | OAuth2IdPUnretryableServerError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40010 | OAuth2IdPRetryableServerError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – probléma van az összevont identitás-szolgáltatóval. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS50000 | TokenIssuanceError – probléma van a bejelentkezési szolgáltatással. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához. |
| AADSTS50001 | InvalidResource – az erőforrás le van tiltva vagy nem létezik. Ellenőrizze az alkalmazás kódjában, hogy megadta-e az elérni kívánt erőforrás URL-címét.  |
| AADSTS50002 | NotAllowedTenant – a bejelentkezés sikertelen volt, mert a bérlőn korlátozott a proxy hozzáférése. Saját bérlői szabályzat esetén módosíthatja a korlátozott bérlői beállításokat a probléma megoldásához. |
| AADSTS500021 | A (z) {bérlő} bérlő hozzáférése megtagadva. A AADSTS500021 azt jelzi, hogy a bérlői korlátozási szolgáltatás konfigurálva van, és a felhasználó olyan bérlőhöz próbál hozzáférni, amely nem szerepel a fejlécben megadott engedélyezett bérlők listáján `Restrict-Access-To-Tenant` . További információ: a [bérlői korlátozások használata a SaaS-Felhőbeli alkalmazásokhoz való hozzáférés kezeléséhez](../manage-apps/tenant-restrictions.md).|
| AADSTS50003 | MissingSigningKey – a bejelentkezés nem sikerült, mert hiányzik az aláíró kulcs vagy tanúsítvány. Ennek az lehet az oka, hogy az alkalmazásban nem volt konfigurálva aláíró kulcs. További információt a hiba [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured)kapcsolatos hibaelhárítási cikkben talál. Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az alkalmazás tulajdonosával vagy az alkalmazás rendszergazdájával. |
| AADSTS50005 | DevicePolicyError – a felhasználó olyan platformról próbált meg bejelentkezni egy eszközre, amelyet jelenleg nem támogat a feltételes hozzáférési házirend. |
| AADSTS50006 | Az InvalidSignature-aláírás ellenőrzése sikertelen volt, mert érvénytelen az aláírás. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – a partner titkosítási tanúsítványa nem található ehhez az alkalmazáshoz. [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal a rögzített megoldás eléréséhez. |
| AADSTS50008 | InvalidSamlToken – az SAML-állítás hiányzik vagy helytelenül van konfigurálva a jogkivonatban. Forduljon az összevonási szolgáltatójához. |
| AADSTS50010 | AudienceUriValidationFailed – a célközönség URI-ellenőrzése nem sikerült az alkalmazáshoz, mert nem lettek konfigurálva jogkivonat-célközönségek. |
| AADSTS50011 | InvalidReplyTo – a válasz címe hiányzik, helytelenül van konfigurálva, vagy nem felel meg az alkalmazáshoz konfigurált válaszoknak.  A probléma megoldásához adja hozzá ezt a hiányzó választ a Azure Active Directory alkalmazáshoz, vagy ha valakinek van engedélye az alkalmazás kezeléséhez Active Directory tegye ezt meg. További információt a hiba [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch)kapcsolatos hibaelhárítási cikkben talál.|
| AADSTS50012 | AuthenticationFailed – a következő okok egyike miatt sikertelen volt a hitelesítés:<ul><li>Az aláíró tanúsítvány tulajdonosának neve nincs engedélyezve</li><li>Nem található megfelelő megbízható szolgáltatói szabályzat a meghatalmazott tulajdonos nevéhez</li><li>A tanúsítványlánc érvénytelen.</li><li>Az aláíró tanúsítvány érvénytelen.</li><li>A házirend nincs konfigurálva a bérlőn</li><li>Az aláíró tanúsítvány ujjlenyomata nincs engedélyezve</li><li>Az ügyfél-érvényesítés érvénytelen aláírást tartalmaz</li></ul> |
| AADSTS50013 | A InvalidAssertion-állítás különböző okok miatt érvénytelen – a jogkivonat kiállítója nem felel meg az API-verziónak az érvényes időtartományon belül – a lejárt – hibásan formázott – frissítési jogkivonat az állításban nem elsődleges frissítési jogkivonat. |
| AADSTS50014 | GuestUserInPendingState – a felhasználó beváltási állapota függőben van. A vendég felhasználói fiók még nincs teljesen létrehozva. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState – a felhasználónak jogi korhatárt kell adnia. |
| AADSTS50017 | CertificateValidationFailed – a minősítés ellenőrzése nem sikerült, a következő okok miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>Időtúllépési probléma miatt nem sikerült beolvasni az érvényes CRL-szegmenseket</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50020 | UserUnauthorized – a felhasználók nem jogosultak a végpont meghívására. |
| AADSTS50027 | InvalidJwtToken – érvénytelen JWT-jogkivonat a következő okok miatt:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul> |
| AADSTS50029 | Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50032 | WeakRsaKey – azt jelzi, hogy a hibás felhasználó megpróbál-e gyenge RSA-kulcsot használni. |
| AADSTS50033 | RetryableError – az adatbázis-műveletekhez nem kapcsolódó átmeneti hibát jelez. |
| AADSTS50034 | UserAccountNotFound – az alkalmazásba való bejelentkezéshez a fiókot hozzá kell adni a címtárhoz. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt – a páros-azonosító létrehozásához szükséges só hiányzik az alapelve. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer – a tulajdonosi jogcímek nem egyeznek a kiállítói jogcímek nevével. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50049 | NoSuchInstanceForDiscovery – ismeretlen vagy érvénytelen példány. |
| AADSTS50050 | MalformedDiscoveryRequest – a kérelem helytelen formátumú. |
| AADSTS50053 | IdsLocked – a fiók zárolva van, mert a felhasználó túl sokszor próbált meg bejelentkezni egy helytelen felhasználói AZONOSÍTÓval vagy jelszóval. |
| AADSTS50055 | InvalidPasswordExpiredPassword – a jelszó lejárt. |
| AADSTS50056 | Érvénytelen vagy Null jelszó-a jelszó nem létezik a tárolóban ehhez a felhasználóhoz. |
| AADSTS50057 | UserDisabled – a felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot. |
| AADSTS50058 | UserInformationNotProvided – ez azt jelenti, hogy a felhasználó nincs bejelentkezve. Ez egy gyakori hiba, amely akkor várható, ha egy felhasználó nem hitelesített, és még nincs bejelentkezve.</br>Ha ezt a hibát egy olyan SSO-környezet javasolja, amelyben a felhasználó korábban bejelentkezett, ez azt jelenti, hogy az SSO-munkamenet nem található vagy érvénytelen.</br>Ezt a hibát akkor lehet visszaadni az alkalmazásnak, ha a prompt = none érték van megadva. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided – a bérlők azonosítására szolgáló információ nem található a kérelemben vagy a megadott hitelesítő adatokban. A felhasználó felveheti a kapcsolatot a bérlő rendszergazdájával a probléma megoldásához. |
| AADSTS50061 | SignoutInvalidRequest – a kijelentkezési kérelem érvénytelen. |
| AADSTS50064 | CredentialAuthenticationError – a Felhasználónév vagy jelszó hitelesítő adatainak érvényesítése sikertelen volt. |
| AADSTS50068 | SignoutInitiatorNotParticipant – a kijelentkezés sikertelen volt. A kijelentkezést kezdeményező alkalmazás nem résztvevő az aktuális munkamenetben. |
| AADSTS50070 | SignoutUnknownSessionIdentifier – a kijelentkezés sikertelen volt. A kijelentkezési kérelem olyan nevet adott meg, amely nem felel meg a meglévő munkamenet (ek) nek. |
| AADSTS50071 | SignoutMessageExpired – a kijelentkezési kérelem lejárt. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt – a felhasználónak regisztrálnia kell a második tényezős hitelesítéshez (interaktív). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt – erős hitelesítésre van szükség, és a felhasználó nem adta át az MFA-feladatot. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired – a rendszergazda által végrehajtott konfigurációs változás miatt, vagy ha új helyre költözött, a felhasználónak többtényezős hitelesítést kell használnia az erőforrás eléréséhez. Próbálkozzon újra egy új engedélyezési kéréssel az erőforráshoz. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – a rendszergazda által végrehajtott konfigurációs változás miatt, vagy mert a felhasználó új helyre költözött, a felhasználónak a többtényezős hitelesítést kell használnia. |
| AADSTS50085 | A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError – a szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra. |
| AADSTS50089 | A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. Próbálkozzon újra a felhasználóval a Felhasználónév-jelszó megadásával. |
| AADSTS50097 | DeviceAuthenticationRequired-eszköz hitelesítésre van szükség. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized – a JWT aláírása érvénytelen. |
| AADSTS50105 | EntitlementGrantsNotFound – a bejelentkezett felhasználó nincs hozzárendelve szerepkörhöz a bejelentkezett alkalmazáshoz. Rendelje hozzá a felhasználót az alkalmazáshoz. További információt a hiba [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS50107 | InvalidRealmUri – a kért összevonási tartomány objektum nem létezik. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat – probléma a JWT fejléctel. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter – a jogcím-átalakítás érvénytelen bemeneti paramétert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében. |
| AADSTS50125 | A PasswordResetRegistrationRequiredInterrupt-bejelentkezés a jelszó-visszaállítási vagy a jelszó-regisztrációs bejegyzés miatt megszakadt. |
| AADSTS50126 | InvalidUserNameOrPassword – hiba történt a hitelesítő adatok érvényesítése esetén, mert érvénytelen Felhasználónév vagy jelszó van. |
| AADSTS50127 | BrokerAppNotInstalled – a felhasználónak telepítenie kell egy közvetítő alkalmazást, hogy hozzáférjen ehhez a tartalomhoz. |
| AADSTS50128 | Érvénytelen tartománynév – a kérelemben vagy a megadott hitelesítő adatokban nem szereplő bérlő által azonosított információk nem találhatók. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined – a munkahelyi csatlakoztatás szükséges az eszköz regisztrálásához. |
| AADSTS50131 | ConditionalAccessFailed – különböző feltételes hozzáférési hibákat jelez, például a hibás Windows-eszköz állapotát, a gyanús tevékenység, a hozzáférési szabályzat vagy a biztonsági házirend határozatai miatt blokkolt kéréseket. |
| AADSTS50132 | SsoArtifactInvalidOrExpired – a munkamenet a jelszó lejárata vagy a legutóbbi jelszó módosítása miatt nem érvényes. |
| AADSTS50133 | SsoArtifactRevoked – a munkamenet a jelszó lejárata vagy a legutóbbi jelszó módosítása miatt nem érvényes. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – nem megfelelő adatközpont. Az OAuth 2,0-es eszköz folyamatában egy alkalmazás által kezdeményezett kérelem engedélyezéséhez az engedélyező félnek ugyanabban az adatközpontban kell lennie, ahol az eredeti kérelem található. |
| AADSTS50135 | PasswordChangeCompromisedPassword – a jelszó módosítása a fiók kockázata miatt szükséges. |
| AADSTS50136 | RedirectMsaSessionToApp – egy MSA-munkamenetet észlelt a rendszer. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken – a munkamenet a hiányzó külső frissítési jogkivonat miatt érvénytelen. |
| AADSTS50140 | KmsiInterrupt – ez a hiba a felhasználó bejelentkezésekor a "Bejelentkezés megtartása" megszakítás miatt történt. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50143 | Munkamenet-eltérés – A munkamenet érvénytelen, mert a felhasználó bérlője nem egyezik meg a tartománymutatóval eltérő erőforrások miatt. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword – a felhasználó Active Directory jelszavának érvényessége lejárt. Hozzon létre egy új jelszót a felhasználó számára, vagy használja a felhasználó az önkiszolgáló visszaállítási eszközt a jelszavának alaphelyzetbe állításához. |
| AADSTS50146 | MissingCustomSigningKey – az alkalmazásnak alkalmazásspecifikus aláíró kulccsal kell konfigurálnia. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. |
| AADSTS50147 | MissingCodeChallenge – a Code Challenge paraméter mérete érvénytelen. |
| AADSTS50155 | DeviceAuthenticationFailed – az eszköz hitelesítése nem sikerült ehhez a felhasználóhoz. |
| AADSTS50158 | ExternalSecurityChallenge – a külső biztonsági probléma nem teljesült. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration – a külső szolgáltató által eljuttatott jogcímek nem elegendőek vagy hiányoznak a külső szolgáltatóhoz kért jogcímek. |
| AADSTS50166 | ExternalClaimsProviderThrottled – nem sikerült elküldeni a kérést a jogcím-szolgáltatónak. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – az ügyfél képes egyszeri bejelentkezéses jogkivonat beszerzésére a Windows 10-es fiókok bővítményen keresztül, de a jogkivonat nem található a kérelemben, vagy lejárt a megadott jogkivonat. |
| AADSTS50169 | InvalidRequestBadRealm – a tartomány nem az aktuális szolgáltatási névtér konfigurált tartománya. |
| AADSTS50170 | MissingExternalClaimsProviderMapping – a külső vezérlők leképezése hiányzik. |
| AADSTS50173 | FreshTokenNeeded – a megadott támogatás lejárt a visszavonás miatt, és szükség van egy új hitelesítési jogkivonatra. Egy rendszergazda vagy egy felhasználó visszavonta a felhasználó jogkivonatait, ami azt eredményezi, hogy a következő jogkivonat frissítése sikertelen lesz, és újrahitelesítés szükséges. Jelentkezzen be újra a felhasználóval. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers – az átadó felhasználók nem támogatják a külső kihívásokat. |
| AADSTS50178 | A SessionControlNotSupportedForPassthroughUsers nem támogatja az áteresztő felhasználók számára a munkamenet-vezérlést. |
| AADSTS50180 | WindowsIntegratedAuthMissing – integrált Windows-hitelesítésre van szükség. Engedélyezze a Seamless SSO-t a bérlőn. |
| AADSTS50187 | DeviceInformationNotProvided – a szolgáltatás nem tudta végrehajtani az eszköz hitelesítését. |
| AADSTS50196 | LoopDetected – A rendszer egy ügyfél-hurkot észlelt. Ellenőrizze az alkalmazás logikáját, és győződjön meg arról, hogy a jogkivonat-gyorsítótárazás implementálva van, és hogy a hibákra vonatkozó feltételek megfelelően vannak kezelve.  Az alkalmazás túl sok olyan kérést hajtott végre túl rövid ideig, amely azt jelzi, hogy hibás állapotban van, vagy a visszaélésszerűen kéri a jogkivonatokat. |
| AADSTS50197 | ConflictingIdentities – a felhasználó nem található. Próbálkozzon újra a bejelentkezéssel. |
| AADSTS50199 | CmsiInterrupt – biztonsági okokból a kérelemhez felhasználói megerősítés szükséges.  Mivel ez egy "interaction_required" hiba, az ügyfélnek interaktív hitelesítést kell végeznie.  Ennek az az oka, hogy a rendszer webnézete egy natív alkalmazás jogkivonatának igénylésére lett felhasználva – a felhasználónak meg kell kérnie, hogy megkérdezze, valóban volt-e az alkalmazás, amelyet be kellett jelentkeznie. A kérdés elkerüléséhez az átirányítási URI-nak a következő biztonságos lista részét kell képeznie: <br />http://<br />https://<br />msauth://(csak iOS esetén)<br />msauthv2://(csak iOS esetén)<br />Chrome-Extension://(csak asztali Chrome böngésző) |
| AADSTS51000 | RequiredFeatureNotEnabled – a szolgáltatás le van tiltva. |
| AADSTS51001 | A DomainHintMustbePresent-tartományhoz a helyszíni biztonsági azonosítóval vagy a helyszíni egyszerű felhasználónévvel kell rendelkeznie. |
| AADSTS51004 | UserAccountNotInDirectory – a felhasználói fiók nem létezik a címtárban. |
| AADSTS51005 | TemporaryRedirect – egyenértékű a 307-as HTTP-állapottal, amely azt jelzi, hogy a kért információk a Location fejlécben megadott URI-n találhatók. Ha ezt az állapotot kapja, kövesse a válaszhoz tartozó Location fejlécet. Az eredeti kérelmezési módszer után az átirányított kérelem is a POST metódust fogja használni. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth – integrált Windows-hitelesítésre van szükség. A felhasználó olyan munkamenet-token használatával jelentkezett be, amelyből hiányzik az integrált Windows-hitelesítési jogcím. Kérje meg a felhasználót, hogy jelentkezzen be újra. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn – a felhasználó nem kapott engedélyt a LinkedIn-erőforrásokhoz való hozzáférésre. |
| AADSTS53000 | DeviceNotCompliant – a feltételes hozzáférési szabályzatnak megfelelő eszközt kell megadnia, és az eszköz nem megfelelő. A felhasználónak regisztrálnia kell az eszközét egy jóváhagyott MDM-szolgáltatóval, például az Intune-nal. |
| AADSTS53001 | DeviceNotDomainJoined – a feltételes hozzáférési szabályzathoz tartományhoz csatlakoztatott eszköz szükséges, és az eszköz nincs tartományhoz csatlakoztatva. A felhasználó egy tartományhoz csatlakoztatott eszközt használ. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp – a használt alkalmazás nem jóváhagyott alkalmazás a feltételes hozzáféréshez. A felhasználónak az egyik alkalmazást kell használnia a jóváhagyott alkalmazások listájáról, hogy hozzáférjen a szolgáltatáshoz. |
| AADSTS53003 | A BlockedByConditionalAccess a feltételes hozzáférési házirendek letiltották a hozzáférést. A hozzáférési házirend nem engedélyezi a jogkivonatok kiadását. |
| AADSTS53004 | ProofUpBlockedDueToRisk – a tartalom elérése előtt a felhasználónak be kell fejeznie a multi-Factor Authentication regisztrációs folyamatát. A felhasználónak regisztrálnia kell többtényezős hitelesítésre. |
| AADSTS53011 | A felhasználó a Kezdőlap bérlői kockázat miatt blokkolva van. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X AZONOSÍTÓval való használatára. küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. |
| AADSTS65004 | UserDeclinedConsent – a felhasználó beleegyezett az alkalmazás elérésére. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
| AADSTS65005 | MisconfiguredApplication – az alkalmazáshoz szükséges erőforrás-hozzáférési lista nem tartalmazza az erőforrás által észlelt alkalmazásokat, vagy az ügyfélalkalmazás hozzáférést kért az erőforráshoz, amely nem lett megadva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás helytelen kérelmet adott vissza, vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, előfordulhat, hogy az alkalmazást nem megfelelő azonosítóval (entitással) konfigurálta. További információt a hiba [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS650052 | Az alkalmazásnak hozzá kell férnie egy olyan szolgáltatáshoz, `(\"{name}\")` amelyet a szervezet `\"{organization}\"` nem fizetett elő vagy engedélyezett. Kérje meg a rendszergazdát, hogy tekintse át a szolgáltatás-előfizetések konfigurációját. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant – a hitelesítés sikertelen volt. A frissítési jogkivonat érvénytelen. A hiba oka a következő lehet:<ul><li>A jogkivonat-kötési fejléc üres</li><li>A jogkivonat kötési kivonata nem egyezik</li></ul> |
| AADSTS70001 | UnauthorizedClient – az alkalmazás le van tiltva. További információt a hiba [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS70002 | InvalidClient – hiba történt a hitelesítő adatok érvényesítése során. A megadott client_secret nem felel meg az ügyfél várt értékének. Javítsa ki a client_secret, és próbálkozzon újra. További információ: [hozzáférési jogkivonat igénylése az engedélyezési kód használatával](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – az alkalmazás nem támogatott engedélyezési típust adott vissza. |
| AADSTS70004 | InvalidRedirectUri – az alkalmazás érvénytelen átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. |
| AADSTS70005 | UnsupportedResponseType – az alkalmazás a következő okok miatt nem támogatott választ adott vissza:<ul><li>a "token" típusú válasz nem engedélyezett az alkalmazáshoz</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – az alkalmazás a jogkivonat kérésekor nem támogatott értéket adott vissza `response_mode` .  |
| AADSTS70008 | ExpiredOrRevokedGrant – a frissítési token inaktivitás miatt lejárt. A tokent XXX-ben adták ki, és bizonyos ideig inaktív volt. |
| AADSTS70011 | InvalidScope – az alkalmazás által igényelt hatókör érvénytelen. |
| AADSTS70012 | MsaServerError – kiszolgálóhiba történt egy MSA (fogyasztói) felhasználó hitelesítése közben. Próbálkozzon újra. Ha a hiba továbbra is fennáll, [küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending – OAuth 2,0 – eszköz folyamatának hibája. Az engedélyezés függőben van. Az eszköz újra megpróbálja lekérdezni a kérést. |
| AADSTS70018 | BadVerificationCode – érvénytelen ellenőrző kód, mert a felhasználó helytelen felhasználói kódot adott meg az eszköz kódjának adatforgalmához. Az engedélyezés nincs jóváhagyva. |
| AADSTS70019 | CodeExpired – az ellenőrző kód lejárt. A felhasználó próbálja meg újra a bejelentkezést. |
| AADSTS75001 | BindingSerializationError – hiba történt az SAML-üzenetek kötése közben. |
| AADSTS75003 | UnsupportedBindingError – az alkalmazás a nem támogatott kötéshez kapcsolódó hibát adott vissza (az SAML protokoll válasza nem küldhető el a HTTP-POSTon kívüli kötéseken keresztül). |
| AADSTS75005 | Saml2MessageInvalid – az Azure AD nem támogatja az alkalmazás által az SSO-hoz továbbított SAML-kérelmet. További információt a hiba [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS7500514 | Nem található egy támogatott típusú SAML-válasz. A támogatott válaszok a következők: "Response" (XML-névtér "urn: Oasis: Names: TC: SAML: 2.0: protokoll") vagy "Érvényesítés" (XML-névtérben: "urn: Oasis: nevek: TC: SAML: 2.0: Érvényesítés"). Alkalmazáshiba – a fejlesztő ezt a hibát fogja kezelni.|
| AADSTS750054 | A SAMLRequest vagy a SAMLResponse szolgáltatásnak lekérdezési karakterlánc paraméterként kell szerepelnie a HTTP-kérelemben az SAML-átirányítási kötéshez. További információt a hiba [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS75008 | RequestDeniedError – az alkalmazástól érkező kérést a rendszer megtagadta, mert az SAML-kérelem nem várt céllal volt. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – az a hitelesítési módszer, amellyel a felhasználó által hitelesített hitelesítés nem felel meg a kért hitelesítési módszernek. További információt a hiba [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch)kapcsolatos hibaelhárítási cikkben talál. |
| AADSTS75016 | A Saml2AuthenticationRequestInvalidNameIDPolicy-egy SAML2 hitelesítési kérelem érvénytelen NameIdPolicy rendelkezik. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – a hitelesítési ügynök nem tud kapcsolódni a Active Directoryhoz. Győződjön meg arról, hogy az ügynök-kiszolgálók ugyanahhoz az AD-erdőhöz tartoznak, mint azok a felhasználók, akiknek a jelszavát érvényesíteni kell, és képesek csatlakozni a Active Directoryhoz. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – a jelszó-érvényesítési kérelem időkorlátja lejárt. Győződjön meg arról, hogy a Active Directory elérhető, és válaszol az ügynökök kéréseire. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException – ismeretlen hiba történt a hitelesítési ügynök válaszának feldolgozása során. Próbálja megismételni a kérelmet. Ha a művelet továbbra is sikertelen, [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) , hogy további részleteket kapjon a hibáról. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem – a hitelesítési ügynök nem tudja érvényesíteni a felhasználó jelszavát. További információért tekintse meg az ügynök naplóit, és ellenőrizze, hogy a Active Directory a várt módon működik-e. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException – a hitelesítési ügynök nem tudja visszafejteni a jelszót. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – a felhasználók az engedélyezett órákon kívül próbáltak bejelentkezni (ez az AD-ben van megadva). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – a hitelesítési kísérletet nem lehetett befejezni, mert a hitelesítési ügynököt és az AD-t futtató gép közötti idő elferdíti az időeltérést. Javítsa az idő szinkronizálási problémáit. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated – a Kerberos-hitelesítés kísérlete sikertelen volt. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – a hitelesítési csomag nem támogatott. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – nem található engedélyezési fejléc. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn – a bérlő nincs engedélyezve a zökkenőmentes egyszeri bejelentkezéshez. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – nem lehet érvényesíteni a felhasználó Kerberos-jegyét. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid – a zökkenőmentes egyszeri bejelentkezés sikertelen volt, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – a felhasználói objektum nem található a felhasználó Kerberos-jegyében található információk alapján. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – az Azure AD-be bejelentkezni próbáló felhasználó különbözik az eszközbe bejelentkezett felhasználótól. |
| AADSTS90002 | InvalidTenantName – a bérlő neve nem található az adattárban. Győződjön meg arról, hogy megfelelő bérlői AZONOSÍTÓval rendelkezik. |
| AADSTS90004 | InvalidRequestFormat – a kérelem formátuma nem megfelelő. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – nem lehet befejezni a kérést. A kérelem érvénytelen, mert az azonosító és a bejelentkezési mutató nem használható együtt.  |
| AADSTS90006 | ExternalServerRetryableError – a szolgáltatás átmenetileg nem érhető el.|
| AADSTS90007 | InvalidSessionId – hibás kérés. Az átadott munkamenet-azonosító nem elemezhető. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission – a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás használatára. Legalább az alkalmazásnak az Azure AD-hez való hozzáférésre van szüksége a bejelentkezés és a felhasználói profil olvasása engedély megadásával. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – az alkalmazás jogkivonatot kér magához. Ez a forgatókönyv csak akkor támogatott, ha a megadott erőforrás a GUID-alapú alkalmazás AZONOSÍTÓját használja. |
| AADSTS90010 | NotSupported – nem sikerült létrehozni az algoritmust. |
| AADSTS90012 | RequestTimeout – a kért időkorlát túllépte az időkorlátot. |
| AADSTS90013 | InvalidUserInput – a felhasználó által megadott bemenet érvénytelen. |
| AADSTS90014 | MissingRequiredField – ez a hibakód különböző esetekben jelenhet meg, ha egy várt mező nem szerepel a hitelesítő adatokban. |
| AADSTS90015 | QueryStringTooLong – a lekérdezési karakterlánc túl hosszú. |
| AADSTS90016 | MissingRequiredClaim – a hozzáférési jogkivonat érvénytelen. A szükséges jogcím hiányzik. |
| AADSTS90019 | MissingTenantRealm – az Azure AD nem tudta megállapítani a bérlő azonosítóját a kérelemből. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – a egyszerű név formátuma érvénytelen, vagy nem felel meg a várt `name[/host][@realm]` formátumnak. Az egyszerű név megadása kötelező, a gazdagép és a tartomány nem kötelező, és lehet null értékű. |
| AADSTS90023 | InvalidRequest – a hitelesítési szolgáltatás kérése érvénytelen. |
| AADSTS9002313 | InvalidRequest – a kérelem helytelen formátumú vagy érvénytelen. – A probléma oka az, hogy hiba történt egy adott végpontra vonatkozó kérelemben. A probléma előfeltétele, hogy beszerezze a hiba Hegedűs-nyomkövetését, és megtekinti, hogy a kérelem formátuma megfelelő-e. |
| AADSTS90024 | RequestBudgetExceededError – átmeneti hiba történt. Próbálkozzon újra. |
| AADSTS90033 | MsodsServiceUnavailable – a Microsoft Online Directory szolgáltatás (MSODS) nem érhető el. |
| AADSTS90036 | MsodsServiceUnretryableFailure – váratlan, nem újrapróbálkozást lehetővé tevő hiba történt a MSODS által üzemeltetett WCF szolgáltatásban. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez |
| AADSTS90038 | NationalCloudTenantRedirection – a megadott "Y" bérlő az "X" nemzeti felhőhöz tartozik. A jelenlegi "Z" felhőalapú példány nem összevonása az X-szel. A rendszer Felhőbeli átirányítási hibát adott vissza. |
| AADSTS90043 | NationalCloudAuthCodeRedirection – a szolgáltatás le van tiltva. |
| AADSTS90051 | InvalidNationalCloudId – a nemzeti felhő azonosítója érvénytelen Felhőbeli azonosítót tartalmaz. |
| AADSTS90055 | TenantThrottlingError – túl sok bejövő kérelem van. Ez a kivétel a letiltott bérlők esetében fordul elő. |
| AADSTS90056 | BadResourceRequest – egy hozzáférési jogkivonat kódjának beváltásához az alkalmazásnak POST-kérést kell küldenie a `/token` végpontnak. Ezt megelőzően meg kell adnia egy engedélyezési kódot, és elküldeni a POST kérelemben a `/token` végpontnak. Tekintse át ezt a cikket az OAuth 2,0 engedélyezési kód folyamatának áttekintéséhez: [.. /azuread-dev/v1-Protocols-OAuth-code.MD](../azuread-dev/v1-protocols-oauth-code.md). Irányítsa a felhasználót a `/authorize` végpontra, amely egy authorization_code ad vissza. A kérelemnek a végponthoz való elküldésével `/token` a felhasználó megkapja a hozzáférési jogkivonatot. Jelentkezzen be a Azure Portalba, és ellenőrizze **Alkalmazásregisztrációk > végpontokat** , és győződjön meg arról, hogy a két végpont megfelelően lett konfigurálva. |
| AADSTS90072 | PassThroughUserMfaError – az a külső fiók, amelyre a felhasználó bejelentkezik, nem létezik azon a bérlőn, amelyre bejelentkezett. így a felhasználó nem tudja kielégíteni a bérlő MFA-követelményeit. Ez a hiba akkor is előfordulhat, ha a felhasználók szinkronizálása megtörtént, de a Active Directory és az Azure AD között eltérés van a ImmutableID (sourceAnchor) attribútumban. Először hozzá kell adni a fiókot külső felhasználóként a bérlőhöz. Jelentkezzen ki, és jelentkezzen be egy másik Azure AD-felhasználói fiókkal. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – hiba történt, amikor a szolgáltatás megpróbált feldolgozni egy WS-Federation üzenetet. Az üzenet érvénytelen. |
| AADSTS90082 | OrgIdWsFederationNotSupported – a kérelemhez megadott hitelesítési házirend jelenleg nem támogatott. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed – a vendég fiókjai nem engedélyezettek ehhez a helyhez. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed – a szolgáltatás nem tudja kiállítani a jogkivonatot, mert a vállalati objektum még nincs kiépítve. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired – a felhasználó DA jogkivonata lejárt. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – hiba történt a WS-Federation üzenetnek az URI-ból való létrehozásakor. |
| AADSTS90090 | GraphRetryableError – a szolgáltatás átmenetileg nem érhető el. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized – a kérelemhez tiltott hibakódtal visszaadott gráf. |
| AADSTS90094 | AdminConsentRequired – rendszergazdai engedély szükséges. |
| AADSTS900382 | A bizalmas ügyfél nem támogatott a felhőalapú kérelemben. |
| AADSTS90099 | A (z) {appId} alkalmazás ({appName}) nincs engedélyezve a (z) {bérlő} bérlőn. Az alkalmazásoknak jogosultsággal kell rendelkezniük ahhoz, hogy hozzáférjenek az ügyfél-bérlőhöz, mielőtt a meghatalmazott rendszergazdák is használhassák azokat. Az alkalmazás engedélyezéséhez adja meg az előfeltételt, vagy hajtsa végre a megfelelő partner Center API-t. |
| AADSTS90100 | InvalidRequestParameter – a paraméter üres vagy érvénytelen. |
| AADSTS901002 | AADSTS901002: a "Resource" kérési paraméter nem támogatott. |
| AADSTS90101 | InvalidEmailAddress – a megadott érték nem érvényes e-mail-cím. Az e-mail-címnek formátumúnak kell lennie `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter – az értéknek érvényes abszolút URI-nak kell lennie. |
| AADSTS90107 | InvalidXml – a kérelem érvénytelen. Győződjön meg arról, hogy az adatai nem rendelkeznek érvénytelen karakterekkel.|
| AADSTS90114 | InvalidExpiryDate – a tömeges jogkivonat lejárati időbélyegzője lejárt jogkivonat kiosztását eredményezi. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode – a felhasználói kód null értékű vagy üres.|
| AADSTS90120 | InvalidDeviceFlowRequest – a kérést már jóváhagyták vagy elutasították. |
| AADSTS90121 | InvalidEmptyRequest – érvénytelen üres kérelem.|
| AADSTS90123 | IdentityProviderAccessDenied – a jogkivonat nem állítható ki, mert az Identity vagy a jogcím-kiállítási szolgáltató megtagadta a kérést. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – az erőforrás nem támogatott a (z `/common` `/consumers` ) vagy végpontokon keresztül. Ehelyett használja a `/organizations` vagy a bérlő-specifikus végpontot. |
| AADSTS90125 | DebugModeEnrollTenantNotFound – a felhasználó nincs a rendszeren. Győződjön meg arról, hogy helyesen adta meg a felhasználónevet. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred – a felhasználó típusa nem támogatott ezen a végponton. A rendszer nem következtetheti ki a felhasználó bérlőjét a felhasználónévből. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – az alkalmazás nem támogatott a `/common` vagy `/consumers` végpontokon. Ehelyett használja a `/organizations` vagy a bérlő-specifikus végpontot. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated – újrapróbálkozást lehetővé nem tevő hiba történt.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound – a felhasználó rendszerbiztonsági tag nem rendelkezik a konfigurált NGC azonosító kulccsal. |
| AADSTS130005 | NgcInvalidSignature – az NGC kulcs aláírásának ellenőrzése sikertelen volt.|
| AADSTS130006 | NgcTransportKeyNotFound – az NGC szállítási kulcs nincs konfigurálva az eszközön. |
| AADSTS130007 | NgcDeviceIsDisabled – az eszköz le van tiltva. |
| AADSTS130008 | NgcDeviceIsNotFound – az NGC-kulcs által hivatkozott eszköz nem található. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce – a kérelem nem lett megadva. |
| AADSTS140001 | InvalidSessionKey – a munkamenetkulcs érvénytelen.|
| AADSTS165900 | InvalidApiRequest – érvénytelen kérés. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion – a Chrome Webview verziója nem támogatott. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource – az erőforrás nincs konfigurálva csak eszköz-tokenek fogadására. |
| AADSTS240001 | BulkAADJTokenUnauthorized – a felhasználónak nincs engedélye az eszközök regisztrálására az Azure AD-ben. |
| AADSTS240002 | RequiredClaimIsMissing – a id_token nem használható `urn:ietf:params:oauth:grant-type:jwt-bearer` támogatásként.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy – a bérlői rendszergazda olyan biztonsági szabályzatot állított be, amely blokkolja ezt a kérelmet. Ellenőrizze a bérlő szintjén definiált biztonsági szabályzatokat annak megállapításához, hogy a kérés megfelel-e a szabályzatkövetelményeknek. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – az alkalmazás nem található a címtárban/bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Előfordulhat, hogy helytelenül konfigurálta az alkalmazás azonosítóját, vagy rossz bérlőre küldte a hitelesítési kérést. |
| AADSTS700020 | InteractionRequired – a hozzáférési engedélyhez interakció szükséges. |
| AADSTS700022 | InvalidMultipleResourcesScope – a bemeneti paraméter hatóköréhez megadott érték érvénytelen, mert egynél több erőforrást tartalmaz. |
| AADSTS700023 | InvalidResourcelessScope – a bemeneti paraméter hatóköréhez megadott érték érvénytelen, ha hozzáférési jogkivonatot kér. |
| AADSTS7000215 | Érvénytelen ügyfél-titkos kulcs van megadva. Fejlesztői hiba – az alkalmazás a szükséges vagy helyes hitelesítési paraméterek nélkül próbál bejelentkezni.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided – a megadott ügyfél titkos kulcsainak érvényessége lejárt. Látogasson el a Azure Portal új kulcsok létrehozásához az alkalmazáshoz, vagy vegye fontolóra a tanúsítvány hitelesítő adatainak használatát a további biztonság érdekében: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | A InvalidGrantRedeemAgainstWrongTenant által biztosított engedélyezési kód más Bérlővel való használatra készült, ezért el lett utasítva. A OAuth2-engedélyezési kódot a (/gyakori hibák vagy/{tenant-ID}) megszerzett Bérlővel azonos Bérlővel kell beváltani. |
| AADSTS1000000 | UserNotBoundError – a kötési API megköveteli, hogy az Azure AD-felhasználó egy külső IDENTITÁSSZOLGÁLTATÓ is hitelesítse magát, ami még nem történt meg. |
| AADSTS1000002 | BindCompleteInterruptError – a kötés sikeresen befejeződött, de a felhasználót tájékoztatni kell. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled – az alkalmazás le van tiltva. |
| AADSTS7000114| A (z) "appIdentifier" alkalmazás nem jogosult az alkalmazáshoz tartozó hívásokra.|
| AADSTS7500529 | A "SAMLId-GUID" érték nem érvényes SAML-azonosító – az Azure AD ezt az attribútumot használja a visszaadott válasz InResponseTo attribútumának feltöltéséhez. Az azonosító nem kezdődhet számmal, ezért a közös stratégia egy olyan karakterláncot, mint az "id", egy GUID karakterlánc-ábrázolására. Például a id6c1c178c166d486687be4aaf5e482730 érvényes azonosító. |

## <a name="next-steps"></a>Következő lépések

* Kérdése van, vagy nem találja, amit keres? Hozzon létre egy GitHub-problémát, vagy tekintse meg a [fejlesztők támogatási és támogatási lehetőségeit](./developer-support-help-options.md) , amelyekkel megismerheti a Súgó és támogatás egyéb lehetőségeit.
