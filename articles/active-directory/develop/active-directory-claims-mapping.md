---
title: Az Azure AD-bérlő (nyilvános előzetes verzió) egy adott alkalmazáshoz tartozó jogkivonatok a kibocsátott jogcímek testreszabása
description: Ezen a lapon jogcímtársítások az Azure Active Directory ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b770ee476fc5c1c334f53904539cc34cf962c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546202"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Útmutató: A bérlőben (előzetes verzió) egy adott alkalmazáshoz tartozó jogkivonatok kibocsátott jogcímek testreszabása

> [!NOTE]
> Ez a szolgáltatás váltja fel, és hatályon kívül helyez a [jogcímek testreszabása](active-directory-saml-claims-customization.md) jelenleg elérhető a portálon keresztül. A ugyanazt az alkalmazást Ha testre szabhatja a jogcímek, az ebben a dokumentumban részletes Graph vagy PowerShell-módszer mellett a portál használatával jogkivonatok ki, hogy alkalmazás figyelmen kívül hagyja a konfiguráció a portálon. Ebben a dokumentumban ismertetett módszerek használatával végzett konfigurációk nem tükröződnek a portálon.

Ez a funkció segítségével bérlői rendszergazdák által a jogkivonatokban bérlőben egy adott alkalmazáshoz kibocsátott jogcímek testreszabása. A jogcím-hozzárendelési szabályzatokat használhatja:

- Válassza ki, melyik jogcímeket jogkivonatok szerepelnek.
- Hozzon létre a jogcímtípusok, amely még nem léteznek.
- Válassza ki, vagy módosítsa az adott jogcímeket bocsátja ki az adatforrást.

> [!NOTE]
> Ez a lehetőség jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A szolgáltatás bármely Azure Active Directory (Azure AD) az előfizetésben a nyilvános előzetes verzióban érhető el. Amikor a szolgáltatás általánosan elérhetővé válik, azonban a szolgáltatást bizonyos aspektusainak szükség lehet egy Azure AD premium előfizetéssel. Ez a funkció konfigurálását jogcím a szabályzat-hozzárendelés támogatja a WS-Fed, SAML, OAuth és OpenID Connect protokollok.

## <a name="claims-mapping-policy-type"></a>Jogcímek társítása a házirend típusa

Az Azure AD-ben egy **házirend** objektum képviseli, az egyes alkalmazásokra, illetve a szervezeten belüli összes alkalmazás által kényszerített szabályai. Minden házirend rendelkezik egy egyedi szerkezetűek, és a egy sor tulajdonságból, majd alkalmazott objektumok, amelyek hozzá vannak rendelve.

A jogcím-szabályzat hozzárendelése egy olyan típusú **házirend** objektum, amely módosítja az egyes alkalmazásoknak kiállított jogkivonatokban bocsásson ki jogcímeket.

## <a name="claim-sets"></a>Jogcímszabály-készletek

Vannak bizonyos csoportok, amelyek meghatározzák, hogy mikor és hogyan használhatók a jogkivonatok jogcímek.

| Jogcímek készletéhez | Leírás |
|---|---|
| Core jogcímek készletéhez | Függetlenül a szabályzat minden jogkivonatban jelen. Ezeket a jogcímeket is számítanak korlátozott, és nem módosítható. |
| Alapszintű jogcímek készletéhez | A jogkivonatok (mellett a core jogcímkészlethez) alapértelmezés szerint vannak kibocsátott jogcímeket tartalmaz. Nincs megadva, vagy alapszintű jogcímek módosítása a jogcímtársítások szabályzatok használatával. |
| Korlátozott jogcímek készletéhez | Nem módosítható a csoportházirend használatával. Az adatforrás nem lehet módosítani, és átalakítás nélkül alkalmazza ezeket a jogcímeket készítésekor. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1\. táblázat: JSON webes jogkivonat (JWT) korlátozott jogcímek készletéhez

| Jogcím típusa (név) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>2\. táblázat: SAML korlátozott jogcímek készletéhez

| Jogcím típusa (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Jogcímek társítása a házirend tulajdonságai

Szabályozhatja a rendszer kibocsátott jogcímek és az adatok forrását, használja a jogcímek társítása a házirend tulajdonságait. Ha egy szabályzat nincs beállítva, a rendszer problémák jogkivonatok, amelyek tartalmazzák az alapvető jogcím beállított, az egyszerű jogcímek készletéhez és bármilyen [választható jogcímek](active-directory-optional-claims.md) , amely az alkalmazás úgy döntött, hogy fogadni.

### <a name="include-basic-claim-set"></a>Alapszintű jogcímkészlethez belefoglalása

**Karakterlánc:** IncludeBasicClaimSet

**Adattípus:** Logikai érték (IGAZ vagy hamis)

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy az egyszerű jogcímek készletében szerepel-e a jogkivonatokban Ez a szabályzat által érintett.

- Ha igaz értékű, az egyszerű jogcímek készletében lévő összes jogcímek a jogkivonatokban, a szabályzat által érintett kibocsátása megtörténik. 
- Ha az értéke HAMIS, az egyszerű jogcímek készletében lévő jogcímek nem szerepelnek a jogkivonatokat, kivéve, ha ugyanaz a szabályzat jogcímek séma tulajdonságában külön-külön kerül.

> [!NOTE] 
> A core jogcímek készletében lévő jogcímek szerepelnek minden jogkivonatot, függetlenül attól, milyen Ez a tulajdonság értéke. 

### <a name="claims-schema"></a>Jogcímek séma

**Karakterlánc:** ClaimsSchema

**Adattípus:** Egy vagy több jogcím séma tételeket tartalmazó JSON-blobját

**Összefoglalás:** Ez a tulajdonság határozza meg, milyen jogcímek szerepelnek a jogkivonatok érvényesek a szabályzatokat, az egyszerű jogcímek készletéhez és a core jogcímkészlethez mellett.
Minden egyes jogcím séma bejegyzés meghatározott ezt a tulajdonságot bizonyos információra szükség. Adja meg az adatok forrását (**érték** vagy **adatforrás és az azonosító pár**), és amely az adatok jogcím többszöröseként (**jogcím típusa**).

### <a name="claim-schema-entry-elements"></a>Jogcím-sémájában található elemek bejegyzés

**Érték:** Az érték elem a jogcímet bocsátja ki az adatokat állandó érték határozza meg.

**A forrás párt:** A forrás- és azonosító elemek határozza meg, ahol az adatokat a jogcímek forrása. 

Állítsa be a forrás elem a következő értékek egyikére: 

- "user": Az igényt adatai egy tulajdonságot a User objektum. 
- "alkalmazás": Az igényt adatai egyszerű alkalmazás (ügyfél) szolgáltatás tulajdonsága. 
- "erőforrás": Az igényt adatai egyszerű szolgáltatás erőforrás-tulajdonságok.
- "célközönség": Az igényt adatai a szolgáltatásnevet, amely a közönség a jogkivonat (az ügyfél vagy az erőforrás egyszerű szolgáltatásnevének) tulajdonsága.
- "Vállalati": Az igényt adatai egy tulajdonság az erőforrás-bérlői vállalati objektumon.
- "átalakítás": A jogcím adatai a jogcímek átalakításáról (lásd a "Jogcím-átalakítás" szakaszt a cikk későbbi részében).

Ha a forrás átalakítási a **TransformationID** elemének szerepelnie kell a jogcím-definíció is.

Az azonosító elem azonosítja, mely tulajdonság a forrás az értéket ad meg a jogcímet. Az alábbi táblázat a forrás minden egyes érték érvényes azonosító értékét.

#### <a name="table-3-valid-id-values-per-source"></a>3\. táblázat: Forrás azonosítója érvényes értéket

| Source | azonosító | Leírás |
|-----|-----|-----|
| Felhasználó | Vezetéknév | Család neve |
| Felhasználó | givenName | Utónév |
| Felhasználó | DisplayName | Megjelenített név |
| Felhasználó | oid | ObjectID |
| Felhasználó | levelezés | E-mail-cím |
| Felhasználó | userprincipalname | Egyszerű felhasználónév |
| Felhasználó | Szervezeti egység|Részleg|
| Felhasználó | onpremisessamaccountname | A helyi SAM-fiók neve |
| Felhasználó | netbiosname| NetBios-név |
| Felhasználó | dnsdomainname | DNS-tartománynév |
| Felhasználó | onpremisesecurityidentifier | a helyszíni biztonsági azonosítója |
| Felhasználó | Cégnév| Szervezet neve |
| Felhasználó | streetAddress | Utca, házszám |
| Felhasználó | Irányítószám | Postai irányítószám |
| Felhasználó | preferredlanguange | Elsődleges nyelv |
| Felhasználó | onpremisesuserprincipalname | a helyszíni egyszerű Felhasználónévvel |
| Felhasználó | mailnickname | Levelezési becenév |
| Felhasználó | extensionattribute1 | Mellék attribútum 1 |
| Felhasználó | extensionattribute2 | Mellék attribútum 2 |
| Felhasználó | extensionattribute3 | Mellék attribútum 3 |
| Felhasználó | extensionattribute4 | Mellék attribútum 4 |
| Felhasználó | extensionattribute5 | Mellék attribútum 5 |
| Felhasználó | extensionattribute6 | Mellék attribútum 6 |
| Felhasználó | extensionattribute7 | Mellék attribútum 7 |
| Felhasználó | extensionattribute8 | Mellék attribútum 8 |
| Felhasználó | extensionattribute9 | Mellék attribútum 9 |
| Felhasználó | extensionattribute10 | Mellék attribútum 10 |
| Felhasználó | extensionattribute11 | Mellék attribútum 11 |
| Felhasználó | extensionattribute12 | Mellék attribútum 12 |
| Felhasználó | extensionattribute13 | Mellék attribútum 13 |
| Felhasználó | extensionattribute14 | Mellék attribútum 14 |
| Felhasználó | extensionattribute15 | Mellék attribútum 15 |
| Felhasználó | othermail | Egyéb E-mail |
| Felhasználó | Ország | Ország |
| Felhasználó | city | Város |
| Felhasználó | állapot | Állapot |
| Felhasználó | Beosztás | Beosztás |
| Felhasználó | EmployeeID | Alkalmazott azonosítója |
| Felhasználó | facsimiletelephonenumber | Fax telefonszáma |
| alkalmazás, erőforrás, célközönség | DisplayName | Megjelenített név |
| alkalmazás, erőforrás, célközönség | hozzáadást | ObjectID |
| alkalmazás, erőforrás, célközönség | címkék | Egyszerű szolgáltatás címke |
| Cég | tenantcountry | Bérlő országában |

**TransformationID:** A TransformationID elemhez kötelező megadni, csak akkor, ha a forrás elem "átalakítás" értékre van állítva.

- Ez az elem átalakítása bejegyzését azonosító eleme, meg kell egyeznie a **ClaimsTransformation** tulajdonság, amely meghatározza, hogyan generáljon a rendszer az adatokat a jogcímet.

**Jogcím típusa:** A **JwtClaimType** és **SamlClaimType** elemek határozza meg, amely jogcímet a jogcím-séma bejegyzés hivatkozik.

- A JwtClaimType JWTs bocsátjuk rendelkezésre a jogcím nevét kell tartalmaznia.
- A SamlClaimType tartalmaznia kell az URI azonosító a jogcím az SAML-jogkivonatok bocsátjuk rendelkezésre.

> [!NOTE]
> A jogcím típusa elemek nevét és a korlátozott jogcímek készletében lévő jogcímek, az URI-k nem használható. További információkért lásd a "Kivételek és korlátozások" szakaszt a cikk későbbi részében.

### <a name="claims-transformation"></a>Jogcím-átalakítás

**Karakterlánc:** ClaimsTransformation

**Adattípus:** JSON-blobját, és egy vagy több átalakítása 

**Összefoglalás:** Ez a tulajdonság használatával olyan gyakori átalakítást alkalmazni a forrásadatokat, a jogcímek sémában megadott jogcímek a kimeneti adatok előállításához.

**AZONOSÍTÓ:** Az azonosító elem használatával hivatkozhat az átalakítást a tételben szereplő TransformationID jogcímek séma tétel. Ez az érték belül a házirend minden átalakítási bejegyzés esetében egyedinek kell lennie.

**TransformationMethod:** A TransformationMethod elem azonosítja, hogy melyik műveletet hajtja végre a jogcím az adatok létrehozásához.

A kiválasztott módszert alapján, bemenetek és kimenetek várható. A bemenetek és kimenetek megadása segítségével a **InputClaims**, **InputParameters** és **OutputClaims** elemeket.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4\. táblázat: Átalakítás módszerek és a várt bemenetek és kimenetek

|TransformationMethod|Várt bemenet|Várt kimenet|Leírás|
|-----|-----|-----|-----|
|Csatlakozás|Szöveg1, karakterlánc2, elválasztó|outputClaim|Illesztések karakterláncok adjon meg a kettő között az elválasztó használatával. Például: szöveg1: "foo@bar.com", karakterlánc2: "védőfal mögött", elválasztó: "." kimeneti jogcím eredményezi: "foo@bar.com.sandbox"|
|ExtractMailPrefix|levelezés|outputClaim|Kinyeri egy e-mail-címet a helyi részét. Például: mail: "foo@bar.com" kimeneti jogcím eredményezi: "foo". Ha nincs \@ bejelentkezési jelen, akkor az eredeti a bemeneti karakterláncot ad vissza, mert.|

**InputClaims:** InputClaims elem használatával az adatokat átadni egy jogcím-séma bejegyzést az átalakítást. Két attribútum rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** a jogcím séma bejegyzés található a megfelelő bemeneti jogcím azonosítója elemmel csatlakozik. 
- **TransformationClaimType** adjon meg egy egyedi nevet a bemeneti szolgál. Ezt a nevet meg kell egyeznie az átalakítási metódus a várt bemenetek egyike.

**InputParameters:** InputParameters elem használatával egy állandó értéket adnak át egy átalakítás. Két attribútum rendelkezik: **Érték** és **azonosító**.

- **Érték** átadni a tényleges konstans érték.
- **ID** adjon meg egy egyedi nevet a bemeneti szolgál. A névnek egyeznie kell az átalakítási metódus a várt bemenetek egyike.

**OutputClaims:** OutputClaims elem használja egy átalakítás által létrehozott adatok tárolására, és hozzákötését egy jogcím-séma bejegyzést. Két attribútum rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- **ClaimTypeReferenceId** azonosítójú, a jogcím-séma bejegyzés található a megfelelő kimeneti jogcímek tartományhoz csatlakozik.
- **TransformationClaimType** adjon meg egy egyedi nevet a kimenetre szolgál. A névnek egyeznie kell a várt kimeneti az átalakítási mód közül.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**Az SAML NameID és egyszerű felhasználónév:** Az attribútumok, amelyről a forrás, és a jogcímek átalakítása, amelyeknél engedélyezve van, a NameID és UPN értékek korlátozva. Tekintse meg az 5 és 6 engedélyezett értékeit tábla.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5\. táblázat: Az SAML NameID adatforrásként engedélyezett attribútumok

|Source|azonosító|Leírás|
|-----|-----|-----|
| Felhasználó | levelezés|E-mail-cím|
| Felhasználó | userprincipalname|Egyszerű felhasználónév|
| Felhasználó | onpremisessamaccountname|A helyi Sam-fiók neve|
| Felhasználó | EmployeeID|Alkalmazott azonosítója|
| Felhasználó | extensionattribute1 | Mellék attribútum 1 |
| Felhasználó | extensionattribute2 | Mellék attribútum 2 |
| Felhasználó | extensionattribute3 | Mellék attribútum 3 |
| Felhasználó | extensionattribute4 | Mellék attribútum 4 |
| Felhasználó | extensionattribute5 | Mellék attribútum 5 |
| Felhasználó | extensionattribute6 | Mellék attribútum 6 |
| Felhasználó | extensionattribute7 | Mellék attribútum 7 |
| Felhasználó | extensionattribute8 | Mellék attribútum 8 |
| Felhasználó | extensionattribute9 | Mellék attribútum 9 |
| Felhasználó | extensionattribute10 | Mellék attribútum 10 |
| Felhasználó | extensionattribute11 | Mellék attribútum 11 |
| Felhasználó | extensionattribute12 | Mellék attribútum 12 |
| Felhasználó | extensionattribute13 | Mellék attribútum 13 |
| Felhasználó | extensionattribute14 | Mellék attribútum 14 |
| Felhasználó | extensionattribute15 | Mellék attribútum 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6\. táblázat: Az SAML NameID engedélyezett átalakítási módszerek

| TransformationMethod | Korlátozások |
| ----- | ----- |
| ExtractMailPrefix | Egyik sem |
| Csatlakozás | Az utótag csatlakoztatott folyamatban van az erőforrás-bérlőkulcs ellenőrzött tartományt kell lennie. |

### <a name="custom-signing-key"></a>Egyéni kulcs aláírása

Egyéni aláíró kulcs hozzá kell rendelni egy jogcímtársítások házirend érvénybe a szolgáltatásnév-objektum. Ez biztosítja, hogy a jogkivonatok létrehozója a jogcímek társítása a házirend által módosítva lett, és úgy védi az alkalmazásokat kártékony szándékú felhasználók által létrehozott házirendek hozzárendelése jogcímekből származó nyugtázása.  Alkalmazások, amelyekre engedélyezve leképezés ellenőriznie kell a jogkivonat-aláíró a kulcsok hozzáfűzésével speciális URI jogcímek `appid={client_id}` , azok [metaadat-kérelmek OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document).  

### <a name="cross-tenant-scenarios"></a>Több-bérlős forgatókönyvek

Vendégfelhasználók jogcímtársítások szabályzatok nem vonatkoznak. Amennyiben a Vendég felhasználó egy jogcímtársítások a szolgáltatásnévhez hozzárendelt szabályzat az alkalmazás eléréséhez, az alapértelmezett jogkivonat kiadott (a házirend nem lesz hatása).

## <a name="claims-mapping-policy-assignment"></a>Jogcímtársítások szabályzat-hozzárendelés

Szabályzatok hozzárendelése jogcímek csak egyszerű szolgáltatási objektumok lehet hozzárendelni.

### <a name="example-claims-mapping-policies"></a>A példában a szabályzat-hozzárendelés jogcímek

Az Azure AD-ben számos forgatókönyv akkor lehet, ha testre szabhatja a tokenek adott szolgáltatásnevek bocsátja ki jogcímeket. Ez a szakasz ismerteti azokat a lépéseket néhány olyan gyakori helyzetet, amelyek segítségével hogyan használhatja a jogcímek társítása a házirend típusát bonyolultnak keresztül.

#### <a name="prerequisites"></a>Előfeltételek

A következő példákban, létrehozása, frissítése, hivatkozás és házirendek a szolgáltatásnevek törlése. Ha most ismerkedik az Azure ad-ben, azt javasoljuk, hogy Ön [ismerje meg az Azure AD-bérlő beszerzése](quickstart-create-new-tenant.md) ezekben a példákban a folytatás előtt.

A kezdéshez kövesse az alábbi lépéseket:

1. Töltse le a legújabb [Azure AD PowerShell-modul nyilvános előzetes kiadás](https://www.powershellgallery.com/packages/AzureADPreview).
1. A Connect paranccsal jelentkezzen be az Azure AD rendszergazdai fiókot. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenet indításához.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. A szervezetben létrehozott összes szabályzat megtekintéséhez futtassa a következő parancsot. Azt javasoljuk, hogy a parancs futtatása után a következő esetekben, ellenőrizze, hogy a szabályzatok a várt módon létrehozott legtöbb művelet.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Példa: Az alapszintű jogcímeket egy egyszerű szolgáltatás a kiállított jogkivonatokban üresen hagyni a szabályzat létrehozása és hozzárendelése

Ebben a példában létrehozott egy szabályzatot, amely az alapvető jogcímek készletében távolít el a társított szolgáltatás rendszerbiztonsági tagok kiállított jogkivonatokban.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a házirend-specifikus társított szolgáltatás egyszerű, az alapszintű jogcímek a jogkivonatok eltávolítja.
   1. A szabályzat létrehozásához a következő parancs futtatásával: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása.
   1. A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
   2. Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Példa: Az EmployeeID és TenantCountry jogcímként szerepeljenek a szolgáltatásnévvel való kiállított jogkivonatokban, a szabályzat létrehozása és hozzárendelése

Ebben a példában létrehozott egy szabályzatot, amely az EmployeeID és TenantCountry ad hozzá a társított szolgáltatás rendszerbiztonsági tagok kiállított jogkivonatokban. Az EmployeeID kibocsátott SAML-jogkivonatok és JWTs neve jogcím típusaként. Az ország jogcímtípus, a SAML-jogkivonatok és JWTs a TenantCountry áll rendelkezésre. Ebben a példában továbbra is az alapszintű, állítsa be a jogkivonatokat a jogcímeket tartalmaznak.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a szabályzat adott szolgáltatásnevek, kapcsolódó jogkivonatok az EmployeeID és TenantCountry jogcímeket hozzáadja.
   1. A szabályzat létrehozásához futtassa a következő parancsot:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása. 
   1. A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
   2. Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Példa: A tokenek egy egyszerű szolgáltatásnak egy jogcím-átalakítás használó-szabályzat létrehozása és hozzárendelése

Ebben a példában létrehozott egy szabályzatot, amely egy egyéni jogcím "JoinedData" társított szolgáltatás rendszerbiztonsági tagok kiadott JWTs bocsát ki. Ez a jogcím a extensionattribute1 attribútum a user objektum ".sandbox" a tárolt adatok lett létrehozva értéket tartalmaz. Ebben a példában Elzárkózunk beállítása a jogkivonatok az alapszintű jogcímeket.

1. Hozzon létre egy jogcímek társítása a házirend. Ez a szabályzat adott szolgáltatásnevek, kapcsolódó jogkivonatok az EmployeeID és TenantCountry jogcímeket hozzáadja.
   1. A szabályzat létrehozásához futtassa a következő parancsot:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Az új házirend megtekintéséhez, és hogy lekérje a házirendet ObjectId, futtassa a következő parancsot: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. A szabályzat hozzárendelése az egyszerű szolgáltatást. Is kell objectid azonosítóját, a szolgáltatás egyszerű beolvasása. 
   1. A szervezet szolgáltatásnevek megtekintéséhez lekérdezheti a Microsoft Graph. Másik lehetőségként az Azure AD Graph Explorer, jelentkezzen be az Azure AD-fiókot.
   2. Ha objectid azonosítóját, az egyszerű szolgáltatás rendelkezik, futtassa a következő parancsot: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Lásd még

Az Azure Portalon keresztül SAML-jogkivonatban kiadott jogcímek testreszabása kapcsolatban lásd: [hogyan: Vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](active-directory-saml-claims-customization.md)