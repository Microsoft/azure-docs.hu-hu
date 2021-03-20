---
title: Azure AD-bérlői alkalmazás jogcímeinek testreszabása (PowerShell)
titleSuffix: Microsoft identity platform
description: Ez az oldal ismerteti Azure Active Directory jogcímek leképezését.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 2d65889a841655fe27994d3855f30f7a7e20e1ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647596"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Útmutató: a jogkivonatokban kibocsátott jogcímek testreszabása egy adott alkalmazáshoz a bérlőben (előzetes verzió)

> [!NOTE]
> Ez a szolgáltatás lecseréli és felülírja a portálon jelenleg kínált [jogcímek testreszabását](active-directory-saml-claims-customization.md) . Ha ugyanezen az alkalmazáson a jogcímeket a portálon is testreszabja a jelen dokumentumban részletezett gráf/PowerShell-módszer mellett, akkor az adott alkalmazáshoz kiadott tokenek figyelmen kívül hagyják a portálon megadott konfigurációt. Az ebben a dokumentumban részletezett módszerekkel végrehajtott konfigurációk nem jelennek meg a portálon.

Ezt a szolgáltatást a bérlői rendszergazdák használják a jogkivonatokban kibocsátott jogcímek testre szabására a bérlőn lévő adott alkalmazás esetében. A jogcímek leképezésére szolgáló házirendek a következőhöz használhatók:

- Válassza ki, hogy mely jogcímek szerepeljenek a jogkivonatokban.
- Még nem létező jogcím-típusok létrehozása.
- Adja meg vagy módosítsa az adott jogcímeken kibocsátott adatforrást.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. Készüljön fel a módosítások visszavonására vagy eltávolítására. A szolgáltatás a nyilvános előzetes verzióban bármilyen Azure Active Directory (Azure AD-) előfizetésben elérhető. Ha azonban a funkció általánosan elérhetővé válik, a funkció egyes szempontjaihoz szükség lehet egy prémium szintű Azure AD-előfizetésre. Ez a funkció támogatja a jogcím-leképezési házirendek konfigurálását a WS-fed, az SAML, a OAuth és az OpenID Connect protokollok esetében.

## <a name="claims-mapping-policy-type"></a>Jogcím-hozzárendelési házirend típusa

Az Azure AD-ben a **házirend** -objektum az egyes alkalmazásokra vagy a szervezet összes alkalmazására vonatkozóan kényszerített szabályok halmazát jelöli. A szabályzatok mindegyike egyedi szerkezettel rendelkezik, és olyan tulajdonságokkal rendelkezik, amelyek a hozzájuk rendelt objektumokra érvényesek.

A jogcím-hozzárendelési házirend olyan **házirend** -objektum, amely módosítja az adott alkalmazások számára kiállított jogkivonatokban kibocsátott jogcímeket.

## <a name="claim-sets"></a>Jogcímkészletek

Léteznek bizonyos jogcímek, amelyek meghatározzák, hogyan és mikor használják a jogkivonatokban.

| Jogcím-készlet | Description |
|---|---|
| Alapszintű jogcímek készlete | Minden jogkivonatban jelen vannak, a szabályzattól függetlenül. Ezek a jogcímek is korlátozottnak minősülnek, és nem módosíthatók. |
| Alapszintű jogcímek készlete | Tartalmazza azokat a jogcímeket, amelyeket a rendszer alapértelmezés szerint a jogkivonatok számára bocsát ki (az alapszintű jogcímek készletén kívül). Az alapszintű jogcímeket kihagyhatja vagy módosíthatja a jogcím-hozzárendelési szabályzatok használatával. |
| Korlátozott jogcímek készlete | Nem módosítható házirend használatával. Az adatforrás nem módosítható, és a jogcímek létrehozásakor nem alkalmaz átalakítást. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1. táblázat: JSON Web Token (JWT) korlátozott jogcímek készlete

| Jogcím típusa (név) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| színész |
| actortoken |
| AIO |
| altsecid |
| AMR |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| AppID |
| appidacr |
| állítás |
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
| vezérlők |
| credential_keys |
| CSR |
| csr_type |
| DeviceID |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| Graph |
| group_sids |
| csoportok |
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
| IAT |
| identityprovider |
| identitásszolgáltató |
| in_corp |
| például |
| ipaddr |
| isbrowserhostedapp |
| ISS |
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
| NBF |
| netbios_name |
| egyszeri |
| OID |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| jelszó |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| PUID |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| erőforrás |
| szerepkör |
| szerepkörök |
| scope |
| SCP |
| SID |
| aláírás |
| signin_state |
| src1 |
| src2 |
| Sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| TID |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| UPN |
| user_setting_sync_url |
| username |
| UTI |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>2. táblázat: SAML-korlátozott jogcímek készlete

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

## <a name="claims-mapping-policy-properties"></a>Jogcím-hozzárendelési házirend tulajdonságai

A jogcímek kibocsátásának szabályozásához, illetve az adatok forrásainak ellenőrzéséhez használja a jogcím-hozzárendelési szabályzat tulajdonságait. Ha a házirend nincs beállítva, a rendszer kiállít egy jogkivonatot, amely tartalmazza az alapszintű jogcímek készletét, az alapszintű jogcímek készletét, valamint az alkalmazás által fogadott [választható jogcímeket](active-directory-optional-claims.md) .

> [!NOTE]
> Az alapszintű jogcímek készletében lévő jogcímek minden jogkivonatban szerepelnek, függetlenül attól, hogy ez a tulajdonság milyen értékre van beállítva.

### <a name="include-basic-claim-set"></a>Alapszintű jogcím-készlet belefoglalása

**Karakterlánc:** IncludeBasicClaimSet

**Adattípus:** Logikai érték (igaz vagy hamis)

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy az alapszintű jogcímek készlete szerepel-e a szabályzat által érintett jogkivonatokban.

- Ha igaz értékre van állítva, a rendszer az alapszintű jogcímek készletében lévő összes jogcímet a szabályzat által érintett jogkivonatokban bocsátja ki.
- Ha hamis értékre van állítva, akkor az alapszintű jogcímek készletében lévő jogcímek nincsenek a jogkivonatokban, kivéve, ha azokat egyedileg adtak hozzá ugyanahhoz a Szabályzathoz tartozó jogcím-séma tulajdonsághoz.



### <a name="claims-schema"></a>Jogcím-séma

**Karakterlánc:** ClaimsSchema

**Adattípus:** JSON-blob egy vagy több jogcím-séma bejegyzéseivel

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy mely jogcímek jelennek meg a szabályzat által érintett jogkivonatokban, az alapszintű jogcímek készletén és az alapszintű jogcím-készleten kívül.
Az ebben a tulajdonságban definiált minden jogcím-séma bejegyzéshez bizonyos információk szükségesek. Adja meg, hogy az adatok honnan származnak (**érték**, **forrás/azonosító pár**, **forrás/ExtensionID pár**), és hogy az adatok hogyan legyenek kibocsátva (**jogcím típusa**).

### <a name="claim-schema-entry-elements"></a>Jogcím-séma bejegyzéseinek elemei

**Érték:** Az Value (érték) elem statikus értéket határoz meg a jogcímek által kibocsátott adatmennyiségként.

**Forrás/azonosító pár:** A forrás-és azonosító elemek határozzák meg, hogy a jogcímben szereplő adatok honnan származnak.

**Forrás-/ExtensionID pár:** A forrás-és ExtensionID elemek határozzák meg a címtár-séma kiterjesztése attribútumot, ahol a jogcímben lévő adatok forrása származik. További információ: a [címtár-séma bővítmény attribútumainak használata a jogcímekben](active-directory-schema-extensions.md).

Állítsa a forrásoldali elemet a következő értékek egyikére:

- "user" (felhasználó): a jogcímben szereplő adattulajdonság a felhasználói objektumon található.
- "Application" (alkalmazás): a jogcímben szereplő adattulajdonság az alkalmazás (ügyfél) egyszerű szolgáltatása.
- "erőforrás": a jogcímben szereplő, az erőforrás-szolgáltatásnév egyik tulajdonsága.
- "hallgatóság": a jogcímben szereplő adattulajdonság a jogkivonat célközönségét képező egyszerű szolgáltatásnév (az ügyfél vagy az erőforrás-szolgáltatás résztvevője).
- "vállalat": a jogcímben szereplő adatforrások az erőforrás-bérlő vállalati objektumának egyik tulajdonsága.
- "átalakítás": a jogcím-átalakításból származó adatok (lásd a cikk későbbi, "jogcím-átalakítás" szakaszát).

Ha a forrás transzformációs, a **TransformationID** elemet is bele kell foglalni a jogcím-definícióba.

Az ID elem azonosítja, hogy a forrás melyik tulajdonsága biztosítja a jogcím értékét. A következő táblázat felsorolja a forrás minden egyes értékének érvényes azonosító értékeit.

#### <a name="table-3-valid-id-values-per-source"></a>3. táblázat: érvényes azonosító értékek/forrás

| Forrás | ID (Azonosító) | Description |
|-----|-----|-----|
| User | surname | Család neve |
| User | givenname | utónév; |
| User | DisplayName | Megjelenítendő név |
| User | ObjectId | ObjectID |
| User | Levelezés | E-mail-cím |
| User | userPrincipalName | Felhasználó egyszerű neve |
| User | Részleg|Részleg|
| User | onpremisessamaccountname | Helyszíni SAM-fiók neve |
| User | netbiosname| NetBios-név |
| User | dnsdomainname | DNS-tartománynév |
| User | onpremisesecurityidentifier | Helyszíni biztonsági azonosító |
| User | CompanyName| Szervezetnév |
| User | streetAddress | Utca, házszám |
| User | Irányítószám | Irányítószám |
| User | preferredlanguage | Előnyben részesített nyelv |
| User | onpremisesuserprincipalname | Helyszíni UPN |*
| User | mailnickname | Levelezési Felhasználónév |
| User | extensionattribute1 | 1. bővítmény-attribútum |
| User | extensionattribute2 | 2. bővítmény-attribútum |
| User | extensionattribute3 | 3. kiterjesztési attribútum |
| User | extensionattribute4 | 4. bővítmény-attribútum |
| User | extensionattribute5 | 5. bővítmény-attribútum |
| User | extensionattribute6 | 6. bővítmény-attribútum |
| User | extensionattribute7 | 7-es kiterjesztési attribútum |
| User | extensionattribute8 | 8. bővítmény-attribútum |
| User | extensionattribute9 | 9-es kiterjesztésű attribútum |
| User | extensionattribute10 | Kiterjesztési attribútum 10 |
| User | extensionattribute11 | 11. bővítmény-attribútum |
| User | extensionattribute12 | 12. bővítmény-attribútum |
| User | extensionattribute13 | 13. kiterjesztési attribútum |
| User | extensionattribute14 | Kiterjesztési attribútum 14 |
| User | extensionAttribute15 | 15. bővítmény-attribútum |
| User | othermail | Egyéb E-mail |
| User | ország | Ország/régió |
| User | city | City |
| User | állapot | Állapot |
| User | beosztás | Beosztás |
| User | Alkalmazottkód | Alkalmazott azonosítója |
| User | érték facsimiletelephonenumber | Fax telefonszáma |
| User | assignedroles | a felhasználóhoz rendelt alkalmazás-szerepkörök listája|
| alkalmazás, erőforrás, célközönség | DisplayName | Megjelenítendő név |
| alkalmazás, erőforrás, célközönség | ObjectId | ObjectID |
| alkalmazás, erőforrás, célközönség | tags | Egyszerű szolgáltatásnév címkéje |
| Vállalat | tenantcountry | Bérlő országa/régiója |

**TransformationID:** A TransformationID elemet csak akkor kell megadni, ha a forrásoldali elem "átalakítás" értékre van állítva.

- Ennek az elemnek meg kell egyeznie a **ClaimsTransformation** tulajdonságban található átalakítási bejegyzés azonosító elemével, amely meghatározza, hogy a rendszer hogyan hozza létre a jogcímhez tartozó adatmennyiséget.

**Jogcím típusa:** A **JwtClaimType** és a **SamlClaimType** elemek határozzák meg, hogy a jogcím-séma bejegyzései melyik jogcímre hivatkoznak.

- A JwtClaimType tartalmaznia kell a JWTs kibocsátott jogcím nevét.
- A SamlClaimType tartalmaznia kell az SAML-jogkivonatokban kibocsátott jogcím URI-JÁT.

* **onPremisesUserPrincipalName attribútum:** Alternatív azonosító használata esetén a userPrincipalName helyszíni attribútuma szinkronizálva lesz az Azure AD attribútum onPremisesUserPrincipalName. Ez az attribútum csak akkor érhető el, ha az alternatív azonosító konfigurálva van, de az MS Graph Beta használatával is elérhető: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> A korlátozott jogcímek készletében lévő jogcímek nevei és URI-azonosítói nem használhatók a jogcím típusú elemekhez. További információt a cikk későbbi, "kivételek és korlátozások" című szakaszában talál.

### <a name="claims-transformation"></a>Jogcím-átalakítás

**Karakterlánc:** ClaimsTransformation

**Adattípus:** JSON-blob, egy vagy több átalakítási bejegyzéssel

**Összefoglalás:** Ezzel a tulajdonsággal általános átalakításokat alkalmazhat a forrásadatok számára, és előállíthatja a jogcímek sémájában megadott jogcímek kimeneti értékeit.

**Azonosító:** Az ID elemmel hivatkozhat erre az átalakítási bejegyzésre az TransformationID jogcím-sémájának bejegyzéseiben. Ennek az értéknek egyedinek kell lennie a házirendben szereplő minden átalakítási bejegyzésnél.

**TransformationMethod:** A TransformationMethod elem azonosítja, hogy a rendszer melyik műveletet hajtja végre a jogcímhez tartozó adat létrehozásához.

A választott módszer alapján a rendszer bemenetek és kimenetek készletét várta. Adja meg a bemeneteket és kimeneteket a **szabályzattípushoz**, a **InputParameters** és a **OutputClaims** elemek használatával.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4. táblázat: transzformációs módszerek és várt bemenetek és kimenetek

|TransformationMethod|Várt bemenet|Várt kimenet|Description|
|-----|-----|-----|-----|
|Csatlakozás|karakterlánc1, karakterlánc2, elválasztó|outputClaim|Összekapcsolja a bemeneti karakterláncokat a között elválasztó használatával. Például: karakterlánc1: " foo@bar.com ", karakterlánc2: "homokozó", elválasztó: "." eredmény a következő outputClaim: " foo@bar.com.sandbox "|
|ExtractMailPrefix|E-mail vagy egyszerű Felhasználónév|kinyert karakterlánc|A ExtensionAttributes 1-15 vagy bármely más olyan séma-bővítmény, amely UPN-vagy e-mail-cím értéket tárol a felhasználó számára, például: johndoe@contoso.com . Egy e-mail-cím helyi részének kibontása. Például: mail: " foo@bar.com " eredmény a outputClaim: "foo". Ha nincs \@ jel, akkor a rendszer az eredeti bemeneti karakterláncot adja vissza.|

**Szabályzattípushoz:** Egy Szabályzattípushoz elem használatával továbbíthatja az adatok átadását a jogcím-séma bejegyzéseiből egy átalakításba. Két attribútummal rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- A **ClaimTypeReferenceId** a jogcím-séma bejegyzés azonosító elemével van csatlakoztatva, hogy megtalálja a megfelelő bemeneti jogcímet.
- A **TransformationClaimType** a bemenet egyedi nevének megadására szolgál. Ennek a névnek meg kell egyeznie az átalakítási módszer várt bemenetével.

**InputParameters:** Egy InputParameters elem használatával egy állandó értéket adhat át egy átalakításnak. Két attribútummal rendelkezik: **érték** és **azonosító**.

- Az **érték** az átadandó tényleges konstans érték.
- Az **azonosító** a bemenet egyedi nevének megadására szolgál. A névnek meg kell egyeznie az átalakítási módszer várt bemenetének egyikével.

**OutputClaims:** Egy OutputClaims elem használatával megtarthatja az átalakítás által generált és a jogcím-séma bejegyzéseihez való kötést. Két attribútummal rendelkezik: **ClaimTypeReferenceId** és **TransformationClaimType**.

- A **ClaimTypeReferenceId** a jogcím-séma bejegyzésének azonosítójával van csatlakoztatva, hogy megtalálja a megfelelő kimeneti jogcímet.
- A **TransformationClaimType** a kimenet egyedi nevének megadására szolgál. A névnek meg kell egyeznie az átalakítási módszer várt kimenetével.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**SAML-NameID és UPN:** Azok az attribútumok, amelyekből a NameID és az UPN-értékeket, valamint a jogcímek átalakítását engedélyezték, korlátozottak. Az engedélyezett értékek megtekintéséhez tekintse meg az 5. táblázatot és a 6. táblázatot.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5. táblázat: az SAML-NameID adatforrásként engedélyezett attribútumai

|Forrás|ID (Azonosító)|Description|
|-----|-----|-----|
| User | Levelezés|E-mail-cím|
| User | userPrincipalName|Felhasználó egyszerű neve|
| User | onpremisessamaccountname|Helyszíni Sam-fiók neve|
| User | Alkalmazottkód|Alkalmazott azonosítója|
| User | extensionattribute1 | 1. bővítmény-attribútum |
| User | extensionattribute2 | 2. bővítmény-attribútum |
| User | extensionattribute3 | 3. kiterjesztési attribútum |
| User | extensionattribute4 | 4. bővítmény-attribútum |
| User | extensionattribute5 | 5. bővítmény-attribútum |
| User | extensionattribute6 | 6. bővítmény-attribútum |
| User | extensionattribute7 | 7-es kiterjesztési attribútum |
| User | extensionattribute8 | 8. bővítmény-attribútum |
| User | extensionattribute9 | 9-es kiterjesztésű attribútum |
| User | extensionattribute10 | Kiterjesztési attribútum 10 |
| User | extensionattribute11 | 11. bővítmény-attribútum |
| User | extensionattribute12 | 12. bővítmény-attribútum |
| User | extensionattribute13 | 13. kiterjesztési attribútum |
| User | extensionattribute14 | Kiterjesztési attribútum 14 |
| User | extensionAttribute15 | 15. bővítmény-attribútum |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6. táblázat: az SAML-NameID engedélyezett átalakítási módszerek

| TransformationMethod | Korlátozások |
| ----- | ----- |
| ExtractMailPrefix | Nincsenek |
| Csatlakozás | A csatlakoztatott utótagnak az erőforrás-bérlő ellenőrzött tartományának kell lennie. |

### <a name="custom-signing-key"></a>Egyéni aláíró kulcs

A jogcím-hozzárendelési szabályzat érvénybe léptetéséhez egyéni aláíró kulcsot kell rendelni az egyszerű szolgáltatásnév objektumhoz. Ez biztosítja, hogy a jogkivonatokat a jogcím-hozzárendelési házirend létrehozója módosította, és megvédi az alkalmazásokat a kártékony szereplőkkel létrehozott jogcímek leképezési házirendjeitől. Egyéni aláíró kulcs hozzáadásához a Azure PowerShell parancsmaggal [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) hozzon létre egy tanúsítvány-kulcs hitelesítő adatait az alkalmazás objektumához.

Azok az alkalmazások, amelyeken engedélyezve van a jogcímek leképezése, a jogkivonat-aláíró kulcsokat az `appid={client_id}` [OpenID Connect metaadat-kéréseinek](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)hozzáfűzésével kell ellenőrizni. Alább látható az OpenID Connect metaadat-dokumentum formátuma, amelyet használni kell:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>Több-bérlős forgatókönyvek

A jogcím-hozzárendelési házirendek nem vonatkoznak a vendég felhasználókra. Ha egy vendég felhasználó egy, az egyszerű szolgáltatáshoz hozzárendelt jogcím-leképezési házirenddel rendelkező alkalmazáshoz próbál hozzáférni, az alapértelmezett jogkivonat ki lesz állítva (a házirendnek nincs hatása).

## <a name="claims-mapping-policy-assignment"></a>Jogcím-hozzárendelési szabályzat-hozzárendelés

A jogcím-hozzárendelési szabályzatok csak egyszerű szolgáltatásnév-objektumokhoz rendelhetők hozzá.

### <a name="example-claims-mapping-policies"></a>Példa jogcím-hozzárendelési házirendekre

Az Azure AD-ben számos forgatókönyv lehetséges, ha testre szabhatja a jogkivonatokban kibocsátott jogcímeket az adott egyszerű szolgáltatásokhoz. Ebben a szakaszban néhány olyan gyakori forgatókönyvet ismertetünk, amelyek segítségével megtudhatja, hogyan használhatja a jogcím-hozzárendelési házirend típusát.

Jogcím-hozzárendelési házirend létrehozásakor jogcímeket is kibocsáthat a tokenekben található Directory sémakezelő bővítmény attribútumból. A *ExtensionID* használja a bővítmény attribútumhoz a elemben lévő *azonosító* helyett `ClaimsSchema` .  További információ a bővítmény attribútumairól: a [címtár-séma bővítmény attribútumainak használata](active-directory-schema-extensions.md).

#### <a name="prerequisites"></a>Előfeltételek

Az alábbi példákban létrehozhat, frissíthet, csatolhat és törölhet házirendeket az egyszerű szolgáltatásokhoz. Ha még nem ismeri az Azure AD-t, javasoljuk, hogy Ismerje meg, [hogyan szerezhet be Azure ad-bérlőt](quickstart-create-new-tenant.md) , mielőtt folytatja ezeket a példákat.

A kezdéshez hajtsa végre a következő lépéseket:

1. Töltse le a legújabb [Azure ad PowerShell-modul nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview).
1. A kapcsolódás parancs futtatásával jelentkezzen be az Azure AD-rendszergazdai fiókjába. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a következő parancsot. Azt javasoljuk, hogy a következő helyzetekben a legtöbb művelet után futtassa ezt a parancsot annak a megadásához, hogy a szabályzatok a várt módon jöjjenek létre.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Példa: hozzon létre és rendeljen hozzá egy szabályzatot, amely kihagyja a szolgáltatás számára kiállított jogkivonatok alapszintű jogcímeit

Ebben a példában egy olyan házirendet hoz létre, amely eltávolítja az alapszintű jogcímek készletét a társított egyszerű szolgáltatások számára kiállított jogkivonatokból.

1. Hozzon létre egy jogcím-hozzárendelési szabályzatot. Ez a szabályzat meghatározott egyszerű szolgáltatásokhoz kapcsolódik, és eltávolítja a jogkivonatok alapszintű jogcímeit.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Az új szabályzat megtekintéséhez és a szabályzat ObjectId beszerzéséhez futtassa a következő parancsot:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév ObjectId is le kell kérnie.
   1. A szervezet összes szolgáltatásának megtekintéséhez [lekérdezheti a Microsoft Graph API](/graph/traverse-the-graph)-t. Vagy [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure ad-fiókjába.
   2. Ha rendelkezik az egyszerű szolgáltatásnév ObjectId, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Példa: hozzon létre és rendeljen hozzá egy szabályzatot, amely tartalmazza az Alkalmazottkód és a TenantCountry az egyszerű szolgáltatás számára kiállított jogkivonatokban lévő jogcímeket.

Ebben a példában egy olyan házirendet hoz létre, amely hozzáadja az Alkalmazottkód és a TenantCountry elemet a társított egyszerű szolgáltatások számára kiállított jogkivonatokhoz. Az Alkalmazottkód az SAML-jogkivonatokban és a JWTs a név jogcím típusaként van kibocsátva. A TenantCountry az SAML-jogkivonatokban és az JWTs-ben az ország/régió jogcímek típusaként van kibocsátva. Ebben a példában továbbra is a jogkivonatokban beállított alapszintű jogcímeket fogjuk használni.

1. Hozzon létre egy jogcím-hozzárendelési szabályzatot. Ez a szabályzat meghatározott egyszerű szolgáltatásokhoz kapcsolódik, és hozzáadja az Alkalmazottkód és a TenantCountry jogcímeket a jogkivonatokhoz.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Az új szabályzat megtekintéséhez és a szabályzat ObjectId beszerzéséhez futtassa a következő parancsot:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév ObjectId is le kell kérnie.
   1. A szervezet összes szolgáltatásának megtekintéséhez [lekérdezheti a Microsoft Graph API](/graph/traverse-the-graph)-t. Vagy [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure ad-fiókjába.
   2. Ha rendelkezik az egyszerű szolgáltatásnév ObjectId, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Példa: hozzon létre és rendeljen hozzá egy olyan szabályzatot, amely jogcímek átalakítását használja egy egyszerű szolgáltatásnév számára kiállított jogkivonatokban.

Ebben a példában egy olyan házirendet hoz létre, amely egy "JoinedData" egyéni jogcímet bocsát ki a társított JWTs számára. Ez a jogcím olyan értéket tartalmaz, amelyet a extensionAttribute1 attribútumban tárolt, a ". homokozó" nevű felhasználói objektumban tárolt adatelemek létrehozásával hoztak létre. Ebben a példában kizárjuk a jogkivonatokban beállított alapszintű jogcímeket.

1. Hozzon létre egy jogcím-hozzárendelési szabályzatot. Ez a szabályzat meghatározott egyszerű szolgáltatásokhoz kapcsolódik, és hozzáadja az Alkalmazottkód és a TenantCountry jogcímeket a jogkivonatokhoz.
   1. A szabályzat létrehozásához futtassa a következő parancsot:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Az új szabályzat megtekintéséhez és a szabályzat ObjectId beszerzéséhez futtassa a következő parancsot:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév ObjectId is le kell kérnie.
   1. A szervezet összes szolgáltatásának megtekintéséhez [lekérdezheti a Microsoft Graph API](/graph/traverse-the-graph)-t. Vagy [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)jelentkezzen be az Azure ad-fiókjába.
   2. Ha rendelkezik az egyszerű szolgáltatásnév ObjectId, futtassa a következő parancsot:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Lásd még

- Ha szeretné megtudni, hogyan szabhatja testre az SAML-jogkivonatban kiállított jogcímeket a Azure Portalon keresztül, tekintse meg a következő témakört [: útmutató: az SAML-jogkivonatban kiállított](active-directory-saml-claims-customization.md)
- További információ a bővítmény attribútumairól: a [jogcímek címtár-séma bővítmény attribútumainak használata](active-directory-schema-extensions.md).
