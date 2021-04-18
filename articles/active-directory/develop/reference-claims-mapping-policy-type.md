---
title: Jogcím-leképezési szabályzat
titleSuffix: Microsoft identity platform
description: Ismerje meg a jogcím-leképezési szabályzattípust, amely az adott alkalmazásokhoz kiadott jogkivonatok által kibocsátott jogcímek módosítására használható.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 04/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 56e855bafa70360711f3e30a7c4527091af7b34c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601240"
---
# <a name="claims-mapping-policy-type"></a>Jogcím-leképezési szabályzat típusa

Az Azure AD-ban a **Policy** objektum egy olyan szabálykészletet képvisel, amely az egyes alkalmazásokon vagy a szervezet összes alkalmazásán érvényesítve van. Minden szabályzattípus egyedi struktúrával rendelkezik, és tulajdonságok halmazát alkalmazza a rendszer az objektumokra, amelyekhez hozzá vannak rendelve.

A jogcím-leképezési szabályzat a **Policy** objektum egy típusa, amely módosítja az adott alkalmazásokhoz kiadott [jogkivonatok](active-directory-claims-mapping.md) által kibocsátott jogcímeket.

## <a name="claim-sets"></a>Jogcímkészletek

Bizonyos jogcímkészletek határozzák meg, hogy a jogkivonatok hogyan és mikor használják őket.

| Jogcímkészlet | Description |
|---|---|
| Alap jogcímkészlet | A szabályzattól függetlenül minden jogkivonatban jelen vannak. Ezek a jogcímek korlátozottnak minősülnek, és nem módosíthatók. |
| Alapszintű jogcímkészlet | Tartalmazza a jogkivonatok alapértelmezés szerint kibocsátott jogcímeit (az alapvető jogcímkészlet mellett). Az alapszintű jogcímeket a jogcím-leképezési házirendek használatával hagyhatja ki vagy módosíthatja. |
| Korlátozott jogcímkészlet | Szabályzat használatával nem módosítható. Az adatforrás nem módosítható, és nem lesz átalakítás alkalmazva a jogcímek generálása során. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>1. táblázat: JSON-webtoken (JWT) korlátozott jogcímkészlete

| Jogcím típusa (név) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| Színész |
| actortoken (aktortoken) |
| Aio |
| altsecid (altsecid) |
| Amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| Appid |
| appcr |
| Állítás |
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
| Csr |
| csr_type |
| Deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| e-mail |
| endpoint |
| enfpolids |
| Exp |
| expires_on |
| grant_type |
| Grafikon |
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
| iat (iat) |
| identityprovider |
| Idp |
| in_corp |
| Példány |
| ipaddr |
| isbrowserhostedapp |
| Iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid (névazonosító) |
| nbf |
| netbios_name |
| nonce |
| Oid |
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
| puid (puid) |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken (frissítéstoken) |
| request_nonce |
| Erőforrás |
| Szerepet |
| szerepkörök |
| scope |
| Scp |
| Sid |
| Aláírás |
| signin_state |
| src1 |
| src2 |
| Al |
| tbid (tiltás) |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| Tid |
| tokenAutologonEnabled |
| trustedfordelegation (megbízható és megbízható) |
| unique_name |
| Upn |
| user_setting_sync_url |
| username |
| Uti |
| Ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>2. táblázat: Korlátozott SAML-jogcímkészlet

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

## <a name="claims-mapping-policy-properties"></a>Jogcím-hozzárendelési szabályzat tulajdonságai

Egy jogcím-leképezési szabályzat tulajdonságaival szabályozhatja, hogy a rendszer milyen jogcímeket bocsát ki, és honnan származiknak az adatok. Ha nincs beállítva szabályzat, a rendszer kiállít olyan jogkivonatokat, amelyek [](active-directory-optional-claims.md) tartalmazzák az alapvető jogcímkészletet, az alapszintű jogcímkészletet és az alkalmazás által megkapott választható jogcímeket.

> [!NOTE]
> Az alap jogcímkészletben található jogcímek minden jogkivonatban jelen vannak, függetlenül attól, hogy milyen tulajdonságra van beállítva.

### <a name="include-basic-claim-set"></a>Alapszintű jogcímkészlet beigénylése

**Sztring:** IncludeBasicClaimSet (IncludeBasicClaimSet)

**Adattípus:** Logikai (igaz vagy hamis)

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy az alapszintű jogcímkészlet szerepel-e a szabályzat által érintett jogkivonatok között.

- Ha True (Igaz) értékűre van állítva, az alapszintű jogcímkészlet összes jogcíme a szabályzat által érintett jogkivonatok között lesz kiadva.
- Ha False (Hamis) értékű, az alapszintű jogcímkészlet jogcímei nincsenek a jogkivonatok között, kivéve, ha külön-külön hozzá vannak adva ugyanannak a szabályzatnak a jogcímsémában.



### <a name="claims-schema"></a>Jogcímsémák

**Sztring:** ClaimsSchema

**Adattípus:** JSON-blob egy vagy több jogcímsémával

**Összefoglalás:** Ez a tulajdonság határozza meg, hogy a házirend által érintett jogkivonatok mely jogcímeket, valamint az alapszintű jogcímkészletet és az alapvető jogcímkészletet is meghatározzák.
A tulajdonságban meghatározott minden egyes jogcímsémát megadva bizonyos információkra van szükség. Adja meg, hogy az adatok honnan jönnek (**Érték,** **Forrás/azonosító** pár , vagy **Forrás/Bővítményazonosító pár**), és melyik jogcím alapján bocsátja ki az adatokat (**Jogcímtípus).**

### <a name="claim-schema-entry-elements"></a>Jogcím sémabeviteli elemei

**Érték:** A Value elem egy statikus értéket határoz meg a jogcímben kivetni szükséges adatokként.

**Forrás-/azonosítópár:** A Forrás és azonosító elemek határozzák meg, hogy a jogcím adatai honnan származiknak.

**Source/ExtensionID pár:** A Source és az ExtensionID elemek azt a könyvtársémabővítmény-attribútumot határozzák meg, amelyből a jogcímben lévő adatok forrása. További információ: [Using directory schema extension attributes in claims (Címtárséma bővítményattribútumok használata jogcímek között).](active-directory-schema-extensions.md)

Állítsa a Source (Forrás) elemet a következő értékek egyikére:

- "user": A jogcímben található adatok a User objektum tulajdonsága.
- "application": A jogcímben található adatok az alkalmazás (ügyfél) szolgáltatásnév tulajdonsága.
- "resource": A jogcímben található adatok az erőforrás-szolgáltatásnév tulajdonsága.
- "audience" (célközönség): A jogcímben található adatok a szolgáltatásnév olyan tulajdonsága, amely a jogkivonat célközönsége (az ügyfél vagy az erőforrás-szolgáltatásnév).
- "company": A jogcímben található adatok az erőforrás-bérlő Vállalati objektumának tulajdonsága.
- "transformation" (átalakítás): A jogcímben található adatok jogcím-átalakításból származóak (lásd a jogcímek átalakítását a cikk későbbi részében).

Ha a forrás átalakítás, akkor a **TransformationID** elemet is bele kell foglalni a jogcím-definícióba.

Az Azonosító elem azonosítja, hogy a forrás melyik tulajdonsága biztosítja a jogcím értékét. Az alábbi táblázat a Source (Forrás) egyes értékeihez érvényes id értékeket sorolja fel.

#### <a name="table-3-valid-id-values-per-source"></a>3. táblázat: Érvényes azonosítóértékek forrásonként

| Forrás | ID (Azonosító) | Description |
|-----|-----|-----|
| Felhasználó | surname | Családnév |
| Felhasználó | givenname | utónév; |
| Felhasználó | Displayname | Megjelenítendő név |
| Felhasználó | objectid (objektumazonosító) | ObjectID |
| Felhasználó | Levelezés | E-mail-cím |
| Felhasználó | userprincipalname (felhasználónév) | Felhasználó egyszerű neve |
| Felhasználó | Részleg|Részleg|
| Felhasználó | onpremisessamaccountname | Helyszíni SAM-fiók neve |
| Felhasználó | netbiosname| NetBios-név |
| Felhasználó | dnsdomainname | DNS-tartománynév |
| Felhasználó | onpremisesecurityidentifier | Helyszíni biztonsági azonosító |
| Felhasználó | companyname (vállalat neve)| Szervezetnév |
| Felhasználó | streetaddress (utcacím) | Utca, házszám |
| Felhasználó | irányítószám | Irányítószám |
| Felhasználó | preferredlanguage (előnyben részesített nyelv) | Előnyben részesített nyelv |
| Felhasználó | onpremisesuserprincipalname | Helyszíni UPN |
| Felhasználó | mailnickname (levelezési név) | E-mail becenév |
| Felhasználó | extensionattribute1 | 1. bővítményattribútum |
| Felhasználó | extensionattribute2 | 2. bővítményattribútum |
| Felhasználó | extensionattribute3 | 3. bővítményattribútum |
| Felhasználó | extensionattribute4 | 4. bővítményattribútum |
| Felhasználó | extensionattribute5 | 5. bővítményattribútum |
| Felhasználó | extensionattribute6 | 6. bővítményattribútum |
| Felhasználó | extensionattribute7 | 7. bővítményattribútum |
| Felhasználó | extensionattribute8 | 8. bővítményattribútum |
| Felhasználó | extensionattribute9 | 9. bővítményattribútum |
| Felhasználó | extensionattribute10 | 10. bővítményattribútum |
| Felhasználó | extensionattribute11 | 11. bővítményattribútum |
| Felhasználó | extensionattribute12 | 12. bővítményattribútum |
| Felhasználó | extensionattribute13 | 13. bővítményattribútum |
| Felhasználó | extensionattribute14 | 14. bővítményattribútum |
| Felhasználó | extensionattribute15 | 15. bővítményattribútum |
| Felhasználó | egyéb e-mail | Egyéb e-mail |
| Felhasználó | ország | Ország/régió |
| Felhasználó | city | City |
| Felhasználó | állapot | Állapot |
| Felhasználó | jobtitle | Beosztás |
| Felhasználó | employeeid (alkalmazottazonosító) | Alkalmazott azonosítója |
| Felhasználó | facsimiletelephonenumber | Facsimile Telefonszám |
| Felhasználó | assignedroles (hozzárendelt szerepkör) | felhasználóhoz rendelt alkalmazás-szerepkörök listája|
| alkalmazás, erőforrás, célközönség | Displayname | Megjelenítendő név |
| alkalmazás, erőforrás, célközönség | objectid (objektumazonosító) | ObjectID |
| alkalmazás, erőforrás, célközönség | tags | Egyszerű szolgáltatáscímke |
| Vállalat | tenantcountry (bérlő országa) | Bérlő országa/régiója |

**TransformationID (Transzformációsazonosító):** A TransformationID elemet csak akkor kell meg adni, ha a Forrás elem "átalakítás" beállításra van beállítva.

- Ennek az elemnek meg kell egyeznie a **ClaimsTransformation** tulajdonság átalakítási bejegyzésének id (Azonosító) elemének, amely meghatározza a jogcím adatainak generálását.

**Jogcím típusa:** A **JwtClaimType és** a **SamlClaimType** elemek határozzák meg, hogy melyik jogcímre vonatkozik ez a jogcímséma-bejegyzés.

- A JwtClaimType típusnak tartalmaznia kell a JWT-kben kibocsátott jogcím nevét.
- A SamlClaimType típusnak tartalmaznia kell az SAML-jogkivonatokban kibocsátott jogcím URI-ját.

* **onPremisesUserPrincipalName attribútum:** Alternatív azonosító használata esetén a userPrincipalName helyszíni attribútum szinkronizálva lesz az Azure AD attribútummal az onPremisesUserPrincipalName attribútummal. Ez az attribútum csak akkor érhető el, ha a másodlagos azonosító konfigurálva van, de az MS Graph Beta-ban is elérhető: https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> A korlátozott jogcímkészletben szereplő jogcímek nevei és URI-i nem használhatók a jogcímtípus elemeihez. További információt a cikk későbbi, "Kivételek és korlátozások" című szakaszában talál.

### <a name="claims-transformation"></a>Jogcím-átalakítás

**Sztring:** ClaimsTransformation (Jogcímek átformációja)

**Adattípus:** JSON-blob egy vagy több átalakítási bejegyzéssel

**Összefoglalás:** Ezzel a tulajdonságkal gyakori átalakításokat alkalmazhat a forrásadatokra a jogcímsémában megadott jogcímek kimeneti adatainak létrehozásához.

**Azonosító:** Az ID elem használatával hivatkozhat erre az átalakítási bejegyzésre a TransformationID jogcímséma bejegyzésben. Ennek az értéknek egyedinek kell lennie a szabályzat minden egyes átalakítási bejegyzése számára.

**TransformationMethod:** A TransformationMethod elem azonosítja, hogy a rendszer melyik műveletet végzi el a jogcím adatainak létrehozásához.

A választott metódus alapján bemenetek és kimenetek egy halmaza várható. Határozza meg a bemeneteket és kimeneteket az **InputClaims,** **az InputParameters és** az **OutputClaims elemek** használatával.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>4. táblázat: Átalakítási módszerek, várt bemenetek és kimenetek

|TransformationMethod|Várt bemenet|Várt kimenet|Description|
|-----|-----|-----|-----|
|Csatlakozás|string1, string2, elválasztó|outputClaim|A bemeneti sztringeket egy elválasztó használatával illeszti össze a két érték között. Például: string1:" foo@bar.com " , string2:"sandbox" , elválasztó:"." eredménye outputClaim:" foo@bar.com.sandbox|
|ExtractMailPrefix|E-mail vagy UPN|kinyert sztring|Az ExtensionAttributes 1–15 vagy bármely más sémabővítmény, amely a felhasználó UPN- vagy e-mail-címértékét tárolja johndoe@contoso.com (például). Kinyeri egy e-mail-cím helyi részét. Például: mail:" foo@bar.com " eredménye outputClaim:"foo". Ha nincs előjel, a visszaadott érték az eredeti \@ bemeneti sztring lesz.|

**InputClaims:** Az InputClaims elemmel adatokat lehet átadni egy jogcímséma-bejegyzésből egy átalakításnak. Két attribútummal rendelkezik: **ClaimTypeReferenceId és** **TransformationClaimType.**

- **A ClaimTypeReferenceId** a jogcímséma-bejegyzés id eleméhez csatlakozik a megfelelő bemeneti jogcím megkereséséhez.
- **A TransformationClaimType egy** egyedi nevet ad a bemenetnek. Ennek a névnek meg kell egyeznie az átalakítási metódus egyik várt bemenetével.

**InputParameters (Bemenetiparaméterek):** Az InputParameters elem használatával konstans értéket ad át egy átalakításnak. Két attribútummal rendelkezik: **Érték** és **Azonosító.**

- **Az érték** a tényleges állandó érték, amit át kell adni.
- **Az azonosítóval** egyedi nevet adhat a bemenetnek. A névnek meg kell egyeznie az átalakítási metódus egyik várt bemenetével.

**OutputClaims:** Az OutputClaims elemmel egy átalakítás által létrehozott adatokat tartat meg, és egy jogcímsémában található bejegyzéshez köti őket. Két attribútummal rendelkezik: **ClaimTypeReferenceId és** **TransformationClaimType.**

- **A ClaimTypeReferenceId** a jogcímséma-bejegyzés azonosítójával van összekapcsolva a megfelelő kimeneti jogcím megkeresését.
- **A TransformationClaimType egy** egyedi nevet ad a kimenetnek. A névnek egyeznie kell az átalakítási metódushoz várt kimenetek egyikével.

### <a name="exceptions-and-restrictions"></a>Kivételek és korlátozások

**SAML NameID és UPN:** Az attribútumok, amelyekből a NameID és az UPN értékeket ön származik, valamint az engedélyezett jogcím-átalakítások korlátozottak. Az engedélyezett értékekért lásd az 5. és a 6. táblázatot.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>5. táblázat: Az SAML NameID adatforrásként engedélyezett attribútumai

|Forrás|ID (Azonosító)|Description|
|-----|-----|-----|
| Felhasználó | Levelezés|E-mail-cím|
| Felhasználó | userprincipalname (felhasználónév)|Felhasználó egyszerű neve|
| Felhasználó | onpremisessamaccountname|Helyszíni Sam-fiók neve|
| Felhasználó | employeeid (alkalmazottazonosító)|Alkalmazott azonosítója|
| Felhasználó | extensionattribute1 | 1. bővítményattribútum |
| Felhasználó | extensionattribute2 | 2. bővítményattribútum |
| Felhasználó | extensionattribute3 | 3. bővítményattribútum |
| Felhasználó | extensionattribute4 | 4. bővítményattribútum |
| Felhasználó | extensionattribute5 | 5. bővítményattribútum |
| Felhasználó | extensionattribute6 | 6. bővítményattribútum |
| Felhasználó | extensionattribute7 | 7. bővítményattribútum |
| Felhasználó | extensionattribute8 | 8. bővítményattribútum |
| Felhasználó | extensionattribute9 | 9. bővítményattribútum |
| Felhasználó | extensionattribute10 | 10. bővítményattribútum |
| Felhasználó | extensionattribute11 | 11. bővítményattribútum |
| Felhasználó | extensionattribute12 | 12. bővítményattribútum |
| Felhasználó | extensionattribute13 | 13. bővítményattribútum |
| Felhasználó | extensionattribute14 | 14. bővítményattribútum |
| Felhasználó | extensionattribute15 | 15. bővítményattribútum |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>6. táblázat: Az SAML NameID-hez engedélyezett átalakítási módszerek

| TransformationMethod (TransformationMethod) | Korlátozások |
| ----- | ----- |
| ExtractMailPrefix | Nincsenek |
| Csatlakozás | A csatlakozott utótagnak az erőforrás-bérlő ellenőrzött tartományának kell lennie. |

### <a name="cross-tenant-scenarios"></a>Bérlők közötti forgatókönyvek

A jogcím-leképezési szabályzatok nem vonatkoznak a vendégfelhasználókra. Ha egy vendégfelhasználó egy olyan alkalmazást próbál elérni, amely a szolgáltatásnévhez hozzárendelt jogcím-leképezési szabályzattal rendelkezik, az alapértelmezett jogkivonat lesz kibocsátva (a szabályzatnak nincs hatása).


## <a name="next-steps"></a>Következő lépések

- Ha meg szeretne ismerkedni a bérlőben egy adott alkalmazás jogkivonatai által kibocsátott jogcímek testreszabásával a PowerShell használatával, olvassa el a következőt: How to: Customize claimsmitted in a tokens for a specific [app in a tenant (Preview) (Útmutató:](active-directory-claims-mapping.md) A jogkivonatok által kibocsátott jogcímek testreszabása egy bérlő adott alkalmazásához (előzetes verzió)
- Az SAML-jogkivonatban kiadott jogcímek testreszabásának Azure Portal: [How to: Customize](active-directory-saml-claims-customization.md) claims issued in the SAML token for enterprise applications (Útmutató: Az SAML-jogkivonatban kibocsátott jogcímek testreszabása vállalati alkalmazásokhoz)
- A bővítményattribútumokkal kapcsolatos további információkért lásd: [Using directory schema extension attributes in claims (Címtársémák bővítményattribútumainak használata jogcímek között).](active-directory-schema-extensions.md)
