---
title: Opcionális jogcímek megadása az Azure AD-alkalmazásokhoz
titleSuffix: Microsoft identity platform
description: Egyéni vagy további jogcímek hozzáadása a Microsoft Identity platform által kiadott SAML 2,0 és JSON web tokens (JWT) jogkivonatokhoz.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 1/06/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 7c0394e765923c027cc15a6278ee451fb13ed1b2
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104280"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Útmutató: opcionális jogcímek megadása az alkalmazás számára

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban választható jogcímeket annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak.

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa a Microsoft Identity platform által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A standard jogcímek listájáért tekintse meg a [hozzáférési jogkivonat](access-tokens.md) és a [id_token](id-tokens.md) jogcím dokumentációját.

Míg a választható jogcímek a v 1.0 és a v 2.0 formátumú jogkivonatokban, valamint az SAML-jogkivonatokban is támogatottak, az értékük nagy részét az 1.0 és a v 2.0 közötti váltáskor adja meg. A [Microsoft Identity platform](./v2-overview.md) egyik célja kisebb méretű tokenek az ügyfelek optimális teljesítményének biztosítása érdekében. Ennek eredményeképpen számos, korábban a hozzáférési és azonosító jogkivonatban szereplő jogcím már nem található meg a v 2.0-s jogkivonatokban, és a kérést külön alkalmazási alapon kell megadnia.

**1. táblázat: alkalmazhatóság**

| Fiók típusa               | 1.0-s verziós tokenek | v 2.0-tokenek |
|----------------------------|-------------|-------------|
| Személyes Microsoft-fiók | N/A         | Támogatott   |
| Azure AD-fiók           | Támogatott   | Támogatott   |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 és v 2.0 választható jogcímek készlete

Az alábbi listában a használni kívánt alkalmazások alapértelmezett választható jogcímei érhetők el. Ha egyéni opcionális jogcímeket szeretne hozzáadni az alkalmazáshoz, tekintse meg az alábbi [címtárszolgáltatás-bővítményeket](#configuring-directory-extension-optional-claims). Ha jogcímeket ad hozzá a **hozzáférési jogkivonathoz**, a jogcímek az alkalmazáshoz (webes API-hoz *) kért hozzáférési* jogkivonatokra vonatkoznak, az alkalmazás *által* kért jogcímekre nem. Függetlenül attól, hogy az ügyfél hogyan fér hozzá az API-hoz, a megfelelő információ szerepel az API-val való hitelesítéshez használt hozzáférési jogkivonatban.

> [!NOTE]
>Ezeknek a jogcímeknek a többsége megadható a JWTs for 1.0 és v 2.0 tokenekhez, de nem SAML-tokenekhez, kivéve, ha a jogkivonat típusa oszlopban szerepel. A fogyasztói fiókok a "felhasználói típus" oszlopban megjelölt jogcímek egy részhalmazát támogatják.  A felsorolt jogcímek többsége nem vonatkozik a felhasználói felhasználókra (nem rendelkezik Bérlővel, ezért `tenant_ctry` nem rendelkezik értékkel).

**2. táblázat: v 1.0 és v 2.0 opcionális jogcím-készlet**

| Név                       |  Leírás   | Jogkivonat típusa | Felhasználó típusa | Jegyzetek  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | A felhasználó utolsó hitelesítésének időpontja. Lásd: OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régiója | JWT        |           | |
| `sid`                      | Munkamenet-azonosító, amely a felhasználónkénti felhasználói kijelentkezéshez használatos. | JWT        |  Személyes és Azure AD-fiókok.   |         |
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail származik      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail származik   | JWT        |           |        |
| `vnet`                     | A VNET megadására vonatkozó információk. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Hozzáadja a kérelmező ügyfél eredeti IPv4-címe (egy VNET belül) |
| `ctry`                     | Felhasználó országa/régiója | JWT |  | Az Azure AD visszaadja a `ctry` választható jogcímet, ha a jelen van, és a mező értéke standard kétbetűs ország-vagy régiókód, például fr, JP, sz stb. |
| `tenant_ctry`              | Erőforrás-bérlő országa | JWT | | Ugyanaz, mint `ctry` a rendszergazda által a bérlői szinten beállítottak kivételével.  Standard kétbetűs értéknek is kell lennie. |
| `xms_pdl`             | Elsődleges adatelérési hely   | JWT | | A többszörös földrajzi bérlők esetében az előnyben részesített adatterület a felhasználó földrajzi régióját megjelenítő hárombetűs kód. További információ: [Azure ad Connect dokumentáció az előnyben részesített adatok helyéről](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Például: `APC` Ázsia és a csendes-óceáni térség. |
| `xms_pl`                   | Felhasználó által előnyben részesített nyelv  | JWT ||A felhasználó által választott nyelv, ha be van állítva. A saját bérlőtől származik, a vendég hozzáférési forgatókönyvekben. Formázott LL-CC ("en-us"). |
| `xms_tpl`                  | Bérlő által előnyben részesített nyelv| JWT | | Az erőforrás-bérlő előnyben részesített nyelve, ha be van állítva. Formázott LL ("en"). |
| `ztdid`                    | Nulla érintéses telepítési azonosító | JWT | | A [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) szolgáltatáshoz használt eszköz identitása |
| `email`                    | A felhasználó címezhető e-mail-címe, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | MSA, Azure AD | Alapértelmezés szerint ez az érték szerepel, ha a felhasználó vendég a bérlőben.  A felügyelt felhasználók (a bérlőn belüli felhasználók) esetében ezt a választható jogcímen keresztül kell kérni, vagy csak a 2.0-s verzióban az OpenID hatókörrel.  A felügyelt felhasználók esetében az e-mail-címet be kell állítani az [Office felügyeleti portálon](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Felhasználói fiók állapota a bérlőben | JWT, SAML | | Ha a felhasználó tagja a bérlőnek, az érték a `0` . Ha vendég, az érték a `1` . |
| `groups`| Csoportos jogcímek opcionális formázása |JWT, SAML| |Az GroupMembershipClaims beállítással használható az [alkalmazás jegyzékfájljában](reference-app-manifest.md), amelyet is be kell állítani. Részletekért lásd az alábbi [csoportos jogcímeket](#configuring-groups-optional-claims) . A csoportok jogcímeivel kapcsolatos további információkért lásd: [csoportos jogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | A username_hint paraméterrel használható felhasználó termékazonosító.  Nem tartós azonosító a felhasználó számára, és nem használható a felhasználói adatok egyedi azonosítására (például adatbázis-kulcsként). Ehelyett használja a felhasználói objektum AZONOSÍTÓját ( `oid` ) az adatbázis kulcsaként. Az [alternatív bejelentkezési azonosítóval](../authentication/howto-authentication-use-email-signin.md) bejelentkezett felhasználók nem láthatják az egyszerű felhasználónevet (UPN). Ehelyett használja a következő azonosító jogkivonat-jogcímeket a bejelentkezési állapot felhasználónak való megjelenítéséhez: `preferred_username` vagy `unique_name` v1-tokenekhez és `preferred_username` v2-tokenekhez. Bár ez a jogcím automatikusan szerepel, megadhatja opcionális jogcímként is, ha további tulajdonságokat szeretne csatolni a vendég felhasználói eset működésének módosításához.  |
| `idtyp`                    | Jogkivonat típusa   | JWT hozzáférési jogkivonatok | Speciális: csak az alkalmazáshoz tartozó hozzáférési jogkivonatokban |  Az érték az, `app` Ha a jogkivonat csak alkalmazási token. Ez a legpontosabb módszer egy API számára annak megállapítására, hogy a token alkalmazás-jogkivonat vagy alkalmazás + felhasználói jogkivonat-e.|

## <a name="v20-specific-optional-claims-set"></a>a 2.0-s verzióra vonatkozó nem kötelező jogcímek beállítása

Ezeket a jogcímeket mindig tartalmazza a v 1.0 Azure AD-jogkivonatok, de a nem tartalmazza a v 2.0-tokeneket, kivéve, ha erre kérték. Ezek a jogcímek csak a JWTs (azonosító jogkivonatok és hozzáférési tokenek) esetén érvényesek.

**3. táblázat: v 2.0 – csak opcionális jogcímek**

| JWT jogcím     | Név                            | Leírás                                | Jegyzetek |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az ügyféltől bejelentkezett IP-cím.   |       |
| `onprem_sid`  | Helyszíni biztonsági azonosító |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | Az a dátum és idő, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-címének módosítása             | URL-cím, amelyet a felhasználó megkereshet a jelszavuk módosításához.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Azt jelzi, hogy az ügyfél bejelentkezik-e a vállalati hálózatról. Ha nem, a rendszer nem tartalmazza a jogcímet.   |  Az MFA [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -címeinek beállításai alapján.    |
| `family_name` | Vezetéknév                       | A felhasználó vezetéknevét, vezetéknevét vagy családjának nevét adja meg a felhasználói objektumban definiált módon. <br>"family_name": "Miller" | Támogatott a MSA és az Azure AD-ben. A `profile` hatókört igényli.   |
| `given_name`  | Utónév                      | A felhasználó első vagy "megadott" nevét adja meg a felhasználói objektumra vonatkozóan.<br>"given_name": "Frank"                   | Támogatott a MSA és az Azure AD-ben.  A `profile` hatókört igényli. |
| `upn`         | Felhasználó egyszerű neve | A username_hint paraméterrel használható felhasználó termékazonosító.  Nem tartós azonosító a felhasználó számára, és nem használható a felhasználói adatok egyedi azonosítására (például adatbázis-kulcsként). Ehelyett használja a felhasználói objektum AZONOSÍTÓját ( `oid` ) az adatbázis kulcsaként. Az [alternatív bejelentkezési azonosítóval](../authentication/howto-authentication-use-email-signin.md) bejelentkezett felhasználók nem láthatják az egyszerű felhasználónevet (UPN). Ehelyett használja a következő `preferred_username` jogcímet a bejelentkezési állapot felhasználónak való megjelenítéséhez. | A jogcím konfigurálásához tekintse meg az alábbi [további tulajdonságokat](#additional-properties-of-optional-claims) . A `profile` hatókört igényli.|

## <a name="v10-specific-optional-claims-set"></a>1.0-s verzióra vonatkozó opcionális jogcímek készlete

A v2-token formátumának néhány továbbfejlesztése a v1 token formátumot használó alkalmazások számára érhető el, mivel ezek javítják a biztonságot és a megbízhatóságot. Ezek nem lépnek érvénybe a v2-végponttól kért azonosító tokenek esetében, valamint a v2 jogkivonat-formátumot használó API-k hozzáférési jogkivonatai. Ezek csak a JWTs vonatkoznak, nem SAML-jogkivonatokra. 

**4. táblázat: v 1.0 – csak opcionális jogcímek**


| JWT jogcím     | Név                            | Leírás | Jegyzetek |
|---------------|---------------------------------|-------------|-------|
|`aud`          | Célközönség | Mindig szerepel a JWTs-ben, de a v1 hozzáférési jogkivonatokban többféle módon is elvégezhető – bármely appID URI-ja, záró perjelgel vagy anélkül, valamint az erőforrás ügyfél-azonosítója. Ez a véletlenszerűség nem hajtható végre a jogkivonat-érvényesítés végrehajtásakor.  A [jogcím további tulajdonságaival](#additional-properties-of-optional-claims) biztosíthatja, hogy mindig az erőforrás ügyfél-azonosítójához legyen beállítva v1 hozzáférési jogkivonatokban. | v1 csak JWT hozzáférési jogkivonatok|
|`preferred_username` | Előnyben részesített Felhasználónév        | Az előnyben részesített felhasználónévi jogcímet adja meg v1-tokeneken belül. Ez megkönnyíti az alkalmazások számára a felhasználónevek és az olvasható megjelenítendő nevek megjelenítését, a jogkivonat típusától függetlenül.  Javasoljuk, hogy használja ezt a választható jogcímet a következő használata helyett: például: `upn` vagy `unique_name` . | v1 azonosító tokenek és hozzáférési jogkivonatok |

### <a name="additional-properties-of-optional-claims"></a>A választható jogcímek további tulajdonságai

Egyes választható jogcímek úgy konfigurálhatók, hogy megváltoztassák a jogcím visszaadásának módját. Ezeket a további tulajdonságokat többnyire a helyszíni alkalmazások áttelepítésére használják a különböző adatelérési elvárásokkal. Például `include_externally_authenticated_upn_without_hash` segít olyan ügyfelek számára, akik nem kezelhetik a kivonatoló jeleket ( `#` ) az UPN-ben.

**4. táblázat: választható jogcímek konfigurálásának értékei**

| Tulajdonság neve  | További tulajdonságnév | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Az SAML-és JWT-válaszokhoz, valamint a 1.0-s és a v 2.0-tokenekhez is használható. |
|                | `include_externally_authenticated_upn`  | Az erőforrás-bérlőben tárolt vendég UPN-t tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, azzal a különbséggel, hogy a kivonatoló jeleket () a `#` rendszer aláhúzással () váltja le `_` , például: `foo_hometenant.com_EXT_@resourcetenant.com`|
| `aud`          |                          | A v1 hozzáférési jogkivonatokban ez a jogcím formátumának módosítására szolgál `aud` .  Ennek nincs hatása a v2-tokenekre vagy a verzió azonosító jogkivonatára, ahol a `aud` jogcím mindig az ügyfél-azonosító. Ezzel a konfigurációval biztosíthatja, hogy az API könnyebben tudja elvégezni a célközönség-ellenőrzést. A hozzáférési jogkivonatot befolyásoló összes választható jogcímhez hasonlóan a kérelemben szereplő erőforrásnak be kell állítania ezt a választható jogcímet, mivel az erőforrások tulajdonosa a hozzáférési jogkivonat.|
|                | `use_guid`               | GUID formátumban bocsátja ki az erőforrás (API) ügyfél-AZONOSÍTÓját, mivel a `aud` jogcím mindig a futtatókörnyezettől függ. Ha például egy erőforrás beállítja ezt a jelzőt, és az ügyfél-azonosítója, akkor az `bb0a297b-6a42-4a55-ac40-09a501456577` adott erőforráshoz hozzáférési jogkivonatot kérő alkalmazások hozzáférési jogkivonatot kapnak a következővel `aud` : `bb0a297b-6a42-4a55-ac40-09a501456577` . </br></br> A jogcím nélkül az API-k megkapják a jogkivonatokat, `aud` `api://MyApi.com` `api://MyApi.com/` `api://myapi.com/AdditionalRegisteredField` vagy bármely más, az adott API-hoz tartozó alkalmazás-azonosító URI-t, valamint az erőforrás ügyfél-azonosítóját. |

#### <a name="additional-properties-example"></a>További tulajdonságok – példa

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Ez a OptionalClaims objektum azt eredményezi, hogy a rendszer visszaadja az azonosító jogkivonatot az ügyfélnek, hogy tartalmazza a `upn` további otthoni Bérlővel és erőforrás-bérlői információkkal rendelkező A `upn` jogcímek csak abban az esetben módosulnak a jogkivonatban, ha a felhasználó a bérlő egyik vendége (amely más identitásszolgáltató használ a hitelesítéshez).

## <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

> [!IMPORTANT]
> A hozzáférési jogkivonatok **mindig** az erőforrás jegyzékfájljának használatával jönnek létre, nem az ügyfél.  Tehát a kérelemben `...scope=https://graph.microsoft.com/user.read...` az erőforrás a Microsoft Graph API.  Így a hozzáférési jogkivonat a Microsoft Graph API-jegyzékfájl használatával jön létre, nem az ügyfél jegyzékfájlja.  Az alkalmazás jegyzékfájljának módosítása soha nem okoz jogkivonatot a Microsoft Graph API-nak a különböző megjelenéséhez.  Annak ellenőrzéséhez, hogy a `accessToken` módosítások érvényben vannak-e, igényeljen egy jogkivonatot az alkalmazáshoz, nem pedig egy másik alkalmazást.

Az alkalmazásra vonatkozó opcionális jogcímeket a felhasználói felület vagy az alkalmazás jegyzékfájlja segítségével konfigurálhatja.

1. Nyissa meg az <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>. 
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.

**Választható jogcímek konfigurálása a felhasználói felületen keresztül:**

[![Választható jogcímek konfigurálása a felhasználói felületen](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. A **kezelés** területen válassza a **jogkivonat-konfiguráció** elemet.
   - A felhasználói felületi beállítás **jogkivonat-konfigurációja** panel nem érhető el olyan Azure ad B2C-bérlőben regisztrált alkalmazásokhoz, amelyek az alkalmazás jegyzékfájljának módosításával konfigurálhatók. További információ:  [jogcímek hozzáadása és felhasználói bevitel testreszabása Egyéni szabályzatok használatával Azure Active Directory B2C](../../active-directory-b2c/configure-user-input.md)  

1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. Válassza ki a konfigurálni kívánt jogkivonat-típust.
1. Válassza ki a hozzáadandó választható jogcímeket.
1. Válassza a **Hozzáadás** lehetőséget.


**Választható jogcímek konfigurálása az alkalmazás jegyzékfájlján keresztül:**

[![Bemutatja, hogyan konfigurálhat választható jogcímeket az alkalmazás jegyzékfájljának használatával](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. A **kezelés** területen válassza a **jegyzékfájl** lehetőséget. Megnyílik egy web-alapú jegyzékfájl-szerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését. Másik lehetőségként a **Letöltés** lehetőséget választva a helyi gépen is szerkesztheti az alkalmazásjegyzéket, majd a **Feltöltés** gombra kattintva alkalmazhatja a módosításokat az alkalmazásra. Az alkalmazás jegyzékfájljának megismeréséhez tekintse meg az [Azure ad Application manifest ismertetése című cikket](reference-app-manifest.md).

    A következő Application manifest bejegyzés hozzáadja a auth_time, a IPADDR és az UPN opcionális jogcímeit az AZONOSÍTÓhoz, a hozzáféréshez és az SAML-jogkivonatokhoz.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. Ha elkészült, válassza a **Mentés** lehetőséget. Most a megadott választható jogcímek szerepelni fognak az alkalmazás jogkivonatában.


### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja az alkalmazás által kért választható jogcímeket. Egy alkalmazás olyan választható jogcímeket is beállíthat, amelyek a biztonsági jogkivonat szolgáltatástól kapott három jogkivonat (azonosító token, hozzáférési jogkivonat, SAML 2 token) mindhárom típusában visszatérhetnek. Az alkalmazás különböző választható jogcímeket konfigurálhat az egyes token-típusokban való visszatéréshez. Az alkalmazás entitás OptionalClaims tulajdonsága egy OptionalClaims objektum.

**5. táblázat: OptionalClaims típusának tulajdonságai**

| Név          | Típus                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Gyűjtemény (OptionalClaim) | A JWT azonosító jogkivonatában visszaadott választható jogcímek.     |
| `accessToken` | Gyűjtemény (OptionalClaim) | Az JWT hozzáférési jogkivonatban visszaadott választható jogcímek. |
| `saml2Token`  | Gyűjtemény (OptionalClaim) | Az SAML-jogkivonatban visszaadott választható jogcímek.       |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Egy alkalmazáshoz vagy egy egyszerű szolgáltatáshoz társított opcionális jogcímet tartalmaz. A [OptionalClaims](/graph/api/resources/optionalclaims) típus IdToken, AccessToken és saml2Token tulajdonsága a OptionalClaim gyűjteménye.
Ha egy adott jogcím támogatja, a OptionalClaim viselkedését a AdditionalProperties mező használatával is módosíthatja.

**6. táblázat: OptionalClaim típusának tulajdonságai**

| Név                   | Típus                    | Description                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcím neve.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A jogcím forrása (Directory-objektum). A bővítmény tulajdonságaiban előre definiált jogcímek és felhasználó által definiált jogcímek találhatók. Ha a forrás értéke null, a jogcím egy előre meghatározott opcionális jogcím. Ha a forrás értéke felhasználó, a Name (név) tulajdonság értéke a felhasználói objektum kiterjesztés tulajdonsága. |
| `essential`            | Edm.Boolean             | Ha az érték TRUE (igaz), akkor az ügyfél által megadott jogcím szükséges a végfelhasználó által kért feladat zökkenőmentes engedélyezési élményének biztosításához. Az alapértelmezett érték a hamis.                                                                                                                 |
| `additionalProperties` | Gyűjtemény (EDM. String) | A jogcím további tulajdonságai. Ha egy tulajdonság létezik ebben a gyűjteményben, a módosítja a Name (név) tulajdonságban megadott választható jogcím viselkedését.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>A címtár-bővítmény választható jogcímeinek konfigurálása

A standard opcionális jogcímek készleten kívül a jogkivonatokat is konfigurálhatja a bővítmények belefoglalásához. További információ: [Microsoft Graph extensionProperty dokumentációja](/graph/api/resources/extensionproperty).

A sémák és a nyitott bővítmények nem támogatottak a választható jogcímek esetében, csak a AAD-Graph Style Directory-bővítmények. Ez a funkció hasznos lehet az alkalmazás által használható további felhasználói adatok csatolásához – például egy további azonosító vagy fontos konfigurációs beállítás, amelyet a felhasználó beállított. Példaként tekintse meg az oldal alját.

A címtár-séma bővítményei egy Azure AD-only szolgáltatás. Ha az alkalmazás jegyzékfájlja egyéni kiterjesztést kér, és egy MSA-felhasználó bejelentkezik az alkalmazásba, ezek a bővítmények nem lesznek visszaadva.

### <a name="directory-extension-formatting"></a>Címtárszolgáltatás-bővítmény formázása

Ha az alkalmazás jegyzékfájljának használatával konfigurálhatja a címtár-bővítmény választható jogcímeit, használja a bővítmény teljes nevét (a következő formátumban: `extension_<appid>_<attributename>` ). Az értéknek `<appid>` meg kell egyeznie a jogcímet kérő alkalmazás azonosítójával.

A JWT belül ezeket a jogcímeket a következő formátumnév fogja kiadni:  `extn.<attributename>` .

Az SAML-tokeneken belül ezek a jogcímek a következő URI-formátummal lesznek kibocsátva: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Csoportok konfigurálása választható jogcímek

Ez a szakasz azokat a konfigurációs beállításokat ismerteti, amelyek a választható jogcímek területen az alapértelmezett csoport objectID a helyi Windows Active Directoryról szinkronizált attribútumokra vonatkozóan használt csoportok attribútumainak módosítására használhatók. A felhasználói felület vagy az alkalmazás jegyzékfájlja segítségével az alkalmazáshoz választható jogcímeket is konfigurálhat.

> [!IMPORTANT]
> További részletek, beleértve a helyszíni attribútumokból származó csoportos jogcímek fontos figyelmeztetéseit is: [alkalmazások csoportos jogcímek konfigurálása az Azure ad-vel](../hybrid/how-to-connect-fed-group-claims.md).

**Csoportok konfigurálása választható jogcímek a felhasználói felületen keresztül:**

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** területen válassza a **jogkivonat-konfiguráció** elemet.
1. Válassza a **Csoport hozzáadása jogcímet**.
1. Válassza ki a visszaadni kívánt csoportok típusát (**biztonsági csoportok**, **címtárbeli szerepkörök**, **az alkalmazáshoz rendelt** **összes csoport** és/vagy csoport). Az **alkalmazás-beállításhoz rendelt csoportok** csak az alkalmazáshoz rendelt csoportokat tartalmazzák. A **minden csoport** beállítás magában foglalja az **SecurityGroup**, a **DirectoryRole** és a **DistributionList**, de **az alkalmazáshoz nem rendelt csoportokat**. 
1. Nem kötelező: válassza ki az adott jogkivonat-típus tulajdonságait, hogy módosítsa a csoportok jogcím értékét, amely a helyszíni csoport attribútumait tartalmazza, vagy ha módosítani szeretné a jogcím típusát a szerepkörre.
1. Kattintson a **Mentés** gombra.

**Csoportok konfigurálása választható jogcímek az alkalmazás jegyzékfájlján keresztül:**

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** területen válassza a **jegyzékfájl** lehetőséget.
1. Adja hozzá a következő bejegyzést a jegyzékfájl-szerkesztő használatával:

   Az érvényes értékek a következők:

   - "All" (ez a beállítás magában foglalja az SecurityGroup, a DirectoryRole és a DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"
   - "Alkalmazáscsoport" (ez a beállítás csak az alkalmazáshoz rendelt csoportokat tartalmazza)

   Például:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Alapértelmezés szerint a csoport ObjectIDs a rendszer a csoportos jogcím értékét fogja kiállítani.  Ha módosítani szeretné a jogcím értékét, hogy a tartalmazza a helyszíni csoport attribútumait, vagy módosítani szeretné a jogcím típusát a szerepkörre, használja a OptionalClaims-konfigurációt az alábbiak szerint:

1. Adja meg a csoport neve konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a tokenben lévő csoportok tartalmazzák a helyszíni AD-csoport attribútumait a választható jogcímek szakaszban, adja meg, hogy melyik jogkivonat-típust kívánja alkalmazni, a kérelmezett választható jogcím és a szükséges további tulajdonságok nevét.  Több jogkivonat-típus is szerepelhet:

   - a OIDC azonosító token idToken
   - a OAuth hozzáférési token accessToken
   - SAML-tokenek Saml2Token.

   A Saml2Token típusa SAML 1.1 és SAML 2.0 formátumú jogkivonatokra is vonatkozik.

   Minden releváns jogkivonat-típus esetében módosítsa a csoportok jogcímet a jegyzékfájl OptionalClaims szakaszának használatára. A OptionalClaims séma a következő:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Választható jogcímek sémája | Érték |
   |----------|-------------|
   | **név:** | "Groups" értéknek kell lennie |
   | **forrás** | Nincs használatban. Kihagyás vagy a Null érték meghatározása |
   | **alapvető** | Nincs használatban. Kihagyás vagy a hamis meghatározása |
   | **additionalProperties:** | További tulajdonságok listája.  Az érvényes beállítások: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A additionalProperties-ben a "sam_account_name", a "dns_domain_and_sam_account_name", a "netbios_domain_and_sam_account_name" értéknek csak az egyike szükséges.  Ha egynél több van jelen, az első használatban van, és minden más figyelmen kívül lesz hagyva.

   Egyes alkalmazások a szerepkör-jogcímben szereplő felhasználóra vonatkozó csoportos adatokat igényelnek.  Ha módosítani szeretné a jogcím típusát egy csoport jogcímen egy szerepkör-jogcímre, adja hozzá a "emit_as_roles" értéket a további tulajdonságokhoz.  A csoport értékeit a rendszer a szerepkör-jogcímben fogja kiállítani.

   Ha "emit_as_roles" van használatban, a felhasználó által hozzárendelt összes alkalmazás-szerepkör nem jelenik meg a szerepkör-jogcímben.

**Példák:**

1) Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\sAMAccountName formátumban

    **Felhasználói felület konfigurációja:**

    [![Nem kötelező jogcímek konfigurálása](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Alkalmazás jegyzékfájljának bejegyzése:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) A netbiosDomain\sAMAccountName formátumban visszaadott csoportok nevének kibocsátása az SAML-és OIDC-azonosító jogkivonatokban lévő szerepkörök jogcímként

    **Felhasználói felület konfigurációja:**

    [![Nem kötelező jogcímek a jegyzékfájlban](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Alkalmazás jegyzékfájljának bejegyzése:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Választható jogcímek – példa

Ebben a szakaszban áttekintheti a forgatókönyvet, amelyből megtudhatja, hogyan használhatja a választható jogcímek funkciót az alkalmazásához.
Több lehetőség is rendelkezésre áll az alkalmazás identitás-konfigurációjának tulajdonságainak frissítésére az opcionális jogcímek engedélyezéséhez és konfigurálásához:

- Használhatja a jogkivonat- **konfigurációs** felhasználói felületet (lásd az alábbi példát)
- Használhatja a **jegyzékfájlt** (lásd az alábbi példát). Olvassa el az [Azure ad Application manifest-dokumentum megismerése című dokumentumot](./reference-app-manifest.md) a jegyzékfájl bevezetésének első lépéseit ismertető dokumentumban.
- Olyan alkalmazást is írhat, amely a [Microsoft Graph API](/graph/use-the-api) -t használja az alkalmazás frissítéséhez. A Microsoft Graph API-referenciák útmutatójának [OptionalClaims](/graph/api/resources/optionalclaims) -típusa segíthet a választható jogcímek konfigurálásában.

**Példa**

Az alábbi példában a **jogkivonat-konfigurációs** felhasználói felület és a **jegyzékfájl** használatával adhat hozzá opcionális jogcímeket az alkalmazáshoz szánt hozzáféréshez, azonosítóhoz és SAML-jogkivonatokhoz. Különböző választható jogcímek lesznek hozzáadva az egyes tokenekhez, amelyeket az alkalmazás fogadni tud:

- Az azonosító tokenek mostantól a teljes űrlapon () is tartalmazzák az összevont felhasználók egyszerű felhasználónevét `<upn>_<homedomain>#EXT#@<resourcedomain>` .
- Az alkalmazáshoz tartozó más ügyfelek által igényelt hozzáférési jogkivonatok mostantól tartalmazzák a auth_time jogcímet.
- Az SAML-tokenek mostantól tartalmazzák a skypeId Directory sémakezelő bővítményt (ebben a példában az alkalmazáshoz tartozó ab603c56068041afb2f6832e2a17e237). Az SAML-tokenek a Skype ID-t teszik elérhetővé `extension_skypeId` .

**Felhasználói felület konfigurációja:**

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.

1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában, majd válassza ki azt.

1. A **kezelés** területen válassza a **jogkivonat-konfiguráció** elemet.

1. Válassza a **választható jogcím hozzáadása** lehetőséget, válassza ki az **azonosító** token típusát, válassza az **egyszerű felhasználónév** lehetőséget a jogcímek listájából, majd válassza a **Hozzáadás** lehetőséget.

1. Válassza a **választható jogcím hozzáadása** lehetőséget, válassza ki a **hozzáférési** token típusát, válassza a **auth_time** elemet a jogcímek listájából, majd válassza a **Hozzáadás** lehetőséget.

1. A jogkivonat-konfiguráció áttekintése képernyőn válassza az **egyszerű felhasználónév** melletti ceruza ikont, válassza a **külsőleg hitelesített** váltógomb elemet, majd kattintson a **Mentés** gombra.

1. Válassza az **opcionális jogcím hozzáadása** lehetőséget, válassza ki az **SAML** -token típusát, válassza a **extn. skypeID** elemet a jogcímek listájából (csak akkor érvényes, ha létrehozott egy skypeID nevű Azure ad felhasználói objektumot), majd válassza a **Hozzáadás** lehetőséget.

    [![SAML-tokenhez választható jogcímek](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Jegyzékfájl konfigurációja:**

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. A hitelesítés után válassza ki az Azure AD-bérlőt az oldal jobb felső sarkában.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. Keresse meg azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában, majd válassza ki azt.
1. A **kezelés** területen válassza a **jegyzékfájl** elemet a beágyazott jegyzékfájl-szerkesztő megnyitásához.
1. A jegyzékfájlt közvetlenül szerkesztheti a szerkesztő használatával. A jegyzékfájl az [alkalmazás entitásának](./reference-app-manifest.md)sémáját követi, és a mentés után automatikusan formázza a jegyzékfájlt. Új elemek lesznek hozzáadva a `OptionalClaims` tulajdonsághoz.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. Amikor befejezte a jegyzékfájl frissítését **, a Mentés gombra kattintva** mentheti a jegyzékfájlt.

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD által biztosított standard jogcímekről.

- [Azonosító jogkivonatok](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
