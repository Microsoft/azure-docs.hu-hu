---
title: A Azure Active Directory alkalmazás jegyzékfájljának ismertetése
titleSuffix: Microsoft identity platform
description: A Azure Active Directory alkalmazás jegyzékének részletes lefedettsége, amely egy Azure AD-bérlőben az alkalmazás identitás-konfigurációját jelöli, és a OAuth engedélyezésének, a hozzájárulási élménynek és egyéb megoldásoknak a megkönnyítése érdekében használatos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 0291d2e6f0cee07bd7164b63dfd4ac8b02c42a01
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583043"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory-alkalmazás jegyzékfájlja

Az alkalmazás jegyzékfájlja a Microsoft Identity platform alkalmazás-objektumának összes attribútumának definícióját tartalmazza. Az alkalmazás-objektum frissítésére szolgáló mechanizmusként is szolgál. Az alkalmazás entitásával és annak sémájával kapcsolatos további információkért tekintse meg a [Graph API alkalmazás-entitás dokumentációját](/graph/api/resources/application).

Az alkalmazások attribútumai a Azure Portalon vagy programozott módon konfigurálhatók [REST API](/graph/api/resources/application) vagy a [PowerShell](/powershell/module/azuread#applications)használatával. Vannak azonban olyan helyzetek, amikor szerkesztenie kell az alkalmazás jegyzékfájlját az alkalmazás attribútumának konfigurálásához. Ezek a forgatókönyvek a következőket biztosítják:

* Ha az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókkal regisztrálta, a felhasználói felületen nem módosíthatja a támogatott Microsoft-fiókokat. Ehelyett az Application manifest Editort kell használnia a támogatott fióktípus módosításához.
* Az alkalmazás által támogatott engedélyek és szerepkörök definiálásához módosítania kell az alkalmazás-jegyzékfájlt.

## <a name="configure-the-app-manifest"></a>Az alkalmazás jegyzékfájljának konfigurálása

Az alkalmazás jegyzékfájljának konfigurálása:

1. Lépjen a <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Keresse meg és válassza ki a **Azure Active Directory** szolgáltatást.
1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki a konfigurálni kívánt alkalmazást.
1. Az alkalmazás **Áttekintés** lapján válassza az **Alkalmazásjegyzék** szakaszt. Megnyílik egy webalapú jegyzékfájl-szerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését a portálon belül. Ha szeretné, a **Letöltés** lehetőségre kattintva helyileg szerkesztheti a jegyzékfájlt, majd a **feltöltés** funkcióval újra alkalmazhatja azt az alkalmazásra.

## <a name="manifest-reference"></a>Jegyzékfájl-hivatkozás

Ez a szakasz az alkalmazás jegyzékfájljában található attribútumokat ismerteti.

### <a name="id-attribute"></a>azonosító attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| id | Sztring |

Az alkalmazás egyedi azonosítója a címtárban. Ez az azonosító nem az alkalmazás azonosítására szolgáló azonosító a protokoll tranzakciójában. Ez az objektum címtárbeli lekérdezésekben való hivatkozására szolgál.

Példa:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| accessTokenAcceptedVersion | Üres Int32 |

Megadja az erőforrás által várt hozzáférési jogkivonat verzióját. Ezzel a paraméterrel megváltoztatható a végpont vagy az ügyfél által a hozzáférési jogkivonat igényléséhez használt JWT-verzió és-formátum.

A használt végpontot (v 1.0 vagy v 2.0) az ügyfél választja ki, és csak a id_tokens verzióját érinti. Az erőforrásoknak explicit módon be kell állítaniuk `accesstokenAcceptedVersion` a hozzáférést a támogatott hozzáférési jogkivonat formátumának jelzéséhez.

A lehetséges értékei: `accesstokenAcceptedVersion` 1, 2 vagy NULL. Ha az érték null, a paraméter alapértelmezett értéke 1, amely a v 1.0 végpontnak felel meg.

Ha a értéke `signInAudience` `AzureADandPersonalMicrosoftAccount` , akkor az értéknek kell lennie `2` .

Példa:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>Bővítmények attribútuma

| Kulcs | Érték típusa |
| :--- | :--- |
| Bővítmények | Gyűjtemény |

Meghatározza azt az egyéni viselkedést, amelyet a felhasználó szolgáltatás használhat az alkalmazások adott környezetben való meghívásához. Előfordulhat például, hogy a file streameket megjelenítő alkalmazások a `addIns` "FileHandler" funkció tulajdonságát is megadhatják. Ez a paraméter lehetővé teszi olyan szolgáltatások használatát, mint a Microsoft 365 az alkalmazás hívása egy olyan dokumentum kontextusában, amelyben a felhasználó dolgozik.

Példa:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| allowPublicClient | Logikai |

Megadja a tartalék alkalmazás típusát. Az Azure AD alapértelmezés szerint kikövetkezteti az alkalmazás típusát a replyUrlsWithType. Vannak olyan helyzetek, amikor az Azure AD nem tudja megállapítani az ügyfél-alkalmazás típusát. Például az egyik ilyen forgatókönyv az a [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) folyamat, amelyben a HTTP-kérelem URL-átirányítás nélkül történik. Ezekben az esetekben az Azure AD az alkalmazás típusát a tulajdonság értéke alapján fogja értelmezni. Ha az érték TRUE (igaz), a tartalék alkalmazás típusa nyilvános ügyfélként van beállítva, például egy mobileszközön futó telepített alkalmazás. Az alapértelmezett érték false (hamis), ami azt jelenti, hogy a tartalék alkalmazás típusa bizalmas ügyfél, például webalkalmazás.

Példa:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>appId attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| appId | Sztring |

Az Azure AD által az alkalmazáshoz hozzárendelt alkalmazás egyedi azonosítóját adja meg.

Példa:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| appRoles | Gyűjtemény |

Meghatározza az alkalmazás által deklarált szerepkörök gyűjteményét. Ezeket a szerepköröket felhasználókhoz, csoportokhoz vagy egyszerű szolgáltatásokhoz rendelheti hozzá. További példák és információk: alkalmazások [szerepköreinek hozzáadása az alkalmazásban és fogadása a jogkivonatban](howto-add-app-roles-in-azure-ad-apps.md).

Példa:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>errorUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| errorUrl | Sztring |

Támogatott.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
|groupMembershipClaims | Sztring |

Az alkalmazás által `groups` várt felhasználói vagy OAuth 2,0 hozzáférési jogkivonatban kiadott jogcímet konfigurálja. Az attribútum beállításához használja a következő érvényes karakterlánc-értékek egyikét:

- `"None"`
- `"SecurityGroup"` (biztonsági csoportokhoz és Azure AD-szerepkörökhöz)
- `"ApplicationGroup"` (ez a beállítás csak az alkalmazáshoz rendelt csoportokat tartalmazza)
- `"DirectoryRole"` (azon Azure AD-címtárbeli szerepkörök beolvasása, amelyeknek a felhasználó a tagja)
- `"All"` (ez az összes olyan biztonsági csoportot, terjesztési csoportot és Azure AD-címtárbeli szerepkört megkapja, amely a bejelentkezett felhasználó tagja).

Példa:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| optionalClaims | Sztring |

A jogkivonatban az adott alkalmazás biztonsági jogkivonat szolgáltatása által visszaadott választható jogcímek.

Jelenleg a személyes fiókokat és az Azure AD-t támogató alkalmazások (az alkalmazás regisztrációs portálján keresztül regisztrálva) nem használhatják a választható jogcímeket. Az csak az Azure AD-ben a 2.0-s végponton regisztrált alkalmazások azonban megkapják a jegyzékfájlban kért választható jogcímeket is. További információ: [opcionális jogcímek](active-directory-optional-claims.md).

Példa:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| identifierUris | Karakterlánc-tömb |

A felhasználó által definiált URI-k, amelyek egyedileg azonosítanak egy webalkalmazást az Azure AD-bérlőn belül, vagy egy ellenőrzött egyéni tartományon belül, ha az alkalmazás több-bérlős.

Példa:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| informationalUrls | Sztring |

Megadja az alkalmazás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat. A használati feltételek és az adatvédelmi nyilatkozat a felhasználói beleegyező felületen keresztül történik. További információkért lásd [: útmutató: a regisztrált Azure ad-alkalmazások használati feltételeinek és adatvédelmi nyilatkozatának hozzáadása](howto-add-terms-of-service-privacy-statement.md).

Példa:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>a hitelesítő adatok attribútuma

| Kulcs | Érték típusa |
| :--- | :--- |
| keyCredentials | Gyűjtemény |

Az alkalmazáshoz rendelt hitelesítő adatokra, a karakterlánc-alapú közös titkokra és az X. 509 tanúsítványokra mutató hivatkozásokat tartalmazza. A rendszer ezeket a hitelesítő adatokat használja hozzáférési tokenek kérésekor (ha az alkalmazás ügyfélként működik, nem pedig erőforrásként).

Példa:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| knownClientApplications | Karakterlánc-tömb |

A csomagban való beszerzéshez használatos, ha olyan megoldással rendelkezik, amely két részt tartalmaz: egy ügyfélalkalmazás és egy egyéni webes API-alkalmazás. Ha ezt az értéket adja meg az ügyfélalkalmazás appID, a felhasználónak csak egyszer kell megadnia az ügyfélalkalmazás számára. Az Azure AD tudni fogja, hogy az ügyfélnek való hozzájárulás implicit módon hozzájárul a webes API-hoz. A szolgáltatás automatikusan kiépíti az ügyfél és a webes API-hoz tartozó egyszerű szolgáltatásokat. Az ügyfélnek és a webes API-alkalmazásnak is regisztrálva kell lennie ugyanabban a bérlőben.

Példa:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| logoUrl | Sztring |

Csak olvasható érték, amely arra a CDN URL-címre mutat, amelyet a portálon töltöttek fel.

Példa:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| logoutUrl | Sztring |

Az alkalmazásból kijelentkezni kívánt URL-cím.

Példa:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>név attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| name | Sztring |

Az alkalmazás megjelenítendő neve.

Példa:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2AllowImplicitFlow | Logikai |

Azt adja meg, hogy a webalkalmazás igényelhet-e OAuth 2.0 implicit flow-hozzáférési jogkivonatokat. Az alapértelmezett érték a false. Ez a jelző a böngészőalapú alkalmazások, például a JavaScript egyoldalas alkalmazások esetében használatos. További információért írja be `OAuth 2.0 implicit grant flow` a tartalomjegyzékbe, és tekintse meg az implicit folyamattal kapcsolatos témaköröket.

Példa:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Logikai |

Megadja, hogy a webalkalmazás igényelhet-e OAuth 2.0 implicit flow ID-tokeneket. Az alapértelmezett érték a false. Ez a jelző a böngészőalapú alkalmazások, például a JavaScript egyoldalas alkalmazások esetében használatos.

Példa:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2Permissions | Gyűjtemény |

Megadja a OAuth 2,0 jogosultsági hatókörök gyűjteményét, amelyeket a webes API-(erőforrás-) alkalmazás az ügyfélalkalmazások számára tesz elérhetővé. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során.

Példa:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| oauth2RequiredPostResponse | Logikai |

Azt határozza meg, hogy a OAuth 2,0 jogkivonat-kérelmek részeként az Azure AD engedélyezi-e a POST kéréseket, a kérések helyett. Az alapértelmezett érték a False (hamis), amely azt határozza meg, hogy csak a GET kérelmek engedélyezettek lesznek.

Példa:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| parentalControlSettings | Sztring |

- `countriesBlockedForMinors` azokat az országokat/régiókat határozza meg, amelyekben az alkalmazás a kiskorúak számára le van tiltva.
- `legalAgeGroupRule` meghatározza az alkalmazás felhasználóira vonatkozó jogi korhatár-szabályt. Beállítható a következőre:,,, `Allow` `RequireConsentForPrivacyServices` `RequireConsentForMinors` `RequireConsentForKids` vagy `BlockMinors` .

Példa:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| passwordCredentials | Gyűjtemény |

Tekintse meg a tulajdonság leírását `keyCredentials` .

Példa:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| preAuthorizedApplications | Gyűjtemény |

Felsorolja az alkalmazásokat és a kért engedélyeket az implicit engedélyhez. Megköveteli, hogy a rendszergazda beleegyezett az alkalmazásba. a preAuthorizedApplications nem szükséges, hogy a felhasználó beleegyezik a kért engedélyekkel. A preAuthorizedApplications felsorolt engedélyek nem igénylik a felhasználói hozzájárulásukat. Azonban a preAuthorizedApplications-ben nem szereplő további kért engedélyek felhasználói beleegyezett.

Példa:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>publisherDomain attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| publisherDomain | Sztring |

Az alkalmazás ellenőrzött közzétevő tartománya. Csak olvasható.

Példa:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| replyUrlsWithType | Gyűjtemény |

Ez a többértékű tulajdonság azon regisztrált redirect_uri értékek listáját tartalmazza, amelyeket az Azure AD a célhelyként fogad el a tokenek visszaküldésekor. Minden URI-értéknek tartalmaznia kell egy társított alkalmazás típusú értéket. A támogatott típusú értékek a következők:

- `Web`
- `InstalledClient`
- `Spa`

További információ: [replyUrl-korlátozások és korlátozások](./reply-url.md).

Példa:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| requiredResourceAccess | Gyűjtemény |

A dinamikus beleegyezőség révén a rendszergazda beleegyezik a `requiredResourceAccess` rendszergazdai jogosultságokkal, és a felhasználók beleegyeznek a statikus beleegyezett. Ez a paraméter azonban nem teszi elérhetővé a felhasználói beleegyező élményt az általános esethez.

- `resourceAppId` annak az erőforrásnak az egyedi azonosítója, amelyhez az alkalmazásnak hozzá kell férnie. Ennek az értéknek meg kell egyeznie a célként megadott erőforrás-alkalmazásban deklarált appId.
- `resourceAccess` a egy olyan tömb, amely felsorolja az alkalmazás által a megadott erőforrás által igényelt OAuth 2.0 engedély-hatóköröket és alkalmazás-szerepköröket. A `id` `type` megadott erőforrások és értékek értékét tartalmazza.

Példa:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| samlMetadataUrl | Sztring |

Az alkalmazás SAML-metaadatainak URL-címe.

Példa:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| signInUrl | Sztring |

Megadja az alkalmazás kezdőlapjának URL-címét.

Példa:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| signInAudience | Sztring |

Megadja, hogy az aktuális alkalmazás milyen Microsoft-fiókokat támogat. A támogatott értékek a következők:
- `AzureADMyOrg` – Microsoft munkahelyi vagy iskolai fiókkal rendelkező felhasználók a szervezeti Azure AD-bérlőben (például egyetlen bérlő)
- `AzureADMultipleOrgs` -Microsoft munkahelyi vagy iskolai fiókkal rendelkező felhasználók bármely szervezet Azure AD-bérlőben (például több-bérlős)
- `AzureADandPersonalMicrosoftAccount` – Személyes Microsoft-fiók vagy munkahelyi vagy iskolai fiókkal rendelkező felhasználók bármely szervezet Azure AD-bérlőben
- `PersonalMicrosoftAccount` – A szolgáltatásokhoz, például az Xboxhoz és a Skype-hoz való bejelentkezéshez használt személyes fiókok.

Példa:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>Címkék attribútum

| Kulcs | Érték típusa |
| :--- | :--- |
| tags | Karakterlánc-tömb  |

Egyéni karakterláncok, amelyek használatával kategorizálhatja és azonosíthatja az alkalmazást.

Példa:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Gyakori problémák

### <a name="manifest-limits"></a>Jegyzékfájlok korlátai

Az alkalmazás-jegyzékfájl több attribútumot is tartalmaz, amelyeket gyűjteményekként nevezünk. például appRoles, a hitelesítő adatok, a knownClientApplications, a identifierUris, a redirectUris, a requiredResourceAccess és a oauth2Permissions. A teljes alkalmazás-jegyzékfájlban bármely alkalmazás esetében az összes gyűjtemény bejegyzéseinek teljes száma 1200. Ha korábban már megadta a 100 átirányítási URI-k használatát az alkalmazás jegyzékfájljában, akkor csak 1100 fennmaradó bejegyzések maradnak az összes többi, a jegyzékfájlt alkotó gyűjteményhez.

> [!NOTE]
> Ha több mint 1200 bejegyzést próbál felvenni az alkalmazás jegyzékfájljában, a következő hibaüzenet jelenhet meg: **"nem sikerült frissíteni az alkalmazást (xxxxxx). Hiba részletei: a jegyzékfájl mérete túllépte a korlátot. Csökkentse az értékek számát, majd próbálja megismételni a kérést. "**

### <a name="unsupported-attributes"></a>Nem támogatott attribútumok

Az alkalmazás jegyzékfájlja az alapul szolgáló alkalmazás modelljének sémáját jelöli az Azure AD-ben. Az alapul szolgáló séma fejlődése után a jegyzékfájl-szerkesztő frissülni fog, hogy az új sémát időről időre tükrözze. Ennek eredményeképpen előfordulhat, hogy az alkalmazás jegyzékfájljában látható új attribútumok is megjelenhetnek. Ritka esetekben előfordulhat, hogy szintaktikai vagy szemantikai változást észlel a meglévő attribútumokban, vagy előfordulhat, hogy a korábban létezett attribútumok már nem támogatottak. Láthatja például, hogy az új attribútumok megjelennek a [Alkalmazásregisztrációkban](https://go.microsoft.com/fwlink/?linkid=2083908), amelyek más néven szerepelnek a Alkalmazásregisztrációk (örökölt) élményben.

| Alkalmazásregisztrációk (örökölt)| Alkalmazásregisztrációk           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Ezeknek az attribútumoknak a leírását a [jegyzékfájl referenciája](#manifest-reference) című szakaszban találja.

Amikor egy korábban letöltött jegyzékfájlt próbál feltölteni, az alábbi hibák valamelyikét láthatja. Ez a hiba valószínűleg azért van, mert a jegyzékfájl-szerkesztő mostantól támogatja a séma újabb verzióját, amely nem egyezik meg azzal, amelyet fel szeretne tölteni.

* "Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: érvénytelen objektumazonosító (nem definiált). []."
* "Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: egy vagy több megadott tulajdonságérték érvénytelen. []."
* "Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: a frissítéshez nem engedélyezett a availableToOtherTenants beállítása ebben az API-verzióban. []."
* "Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: a "replyUrls" tulajdonság frissítései nem engedélyezettek ehhez az alkalmazáshoz. Ehelyett használja a "replyUrlsWithType" tulajdonságot. []."
* "Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: nem található A típus neve nélküli érték, és a várt típus nem érhető el. Ha a modell meg van adva, a hasznos adattartalomban szereplő minden értéknek olyan típusúnak kell lennie, amely adható meg a hasznos adattartalomban, explicit módon a hívó által, vagy implicit módon következtetett a fölérendelt értéktől. []"

Ha az alábbi hibák valamelyikét látja, a következő műveleteket javasoljuk:

1. Szerkessze az attribútumokat egyenként a jegyzékfájl-szerkesztőben egy korábban letöltött jegyzékfájl feltöltése helyett. A [jegyzékfájlok hivatkozási](#manifest-reference) táblázatával megismerheti a régi és az új attribútumok szintaxisát és szemantikaát, így sikeresen szerkesztheti a kívánt attribútumokat.
1. Ha a munkafolyamata megköveteli, hogy később is használhassa a jegyzékfájlokat a forrás-adattárban, javasoljuk, hogy az adattárban lévő mentett jegyzékfájlokat a **Alkalmazásregisztrációk** felhasználói felületén látható értékre alapozza.

## <a name="next-steps"></a>Következő lépések

* Az alkalmazások alkalmazás-és szolgáltatásnév-objektumai közötti kapcsolattal kapcsolatos további információkért lásd: [alkalmazás-és szolgáltatásnév-objektumok az Azure ad-ben](app-objects-and-service-principals.md).
* Tekintse meg a Microsoft [Identity platform fejlesztői szószedetét](developer-glossary.md) a Microsoft Identity platform néhány alapvető fejlesztői koncepciójának meghatározásához.

A következő megjegyzések szakasz a tartalom pontosítását és formázását segítő visszajelzést nyújt.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
