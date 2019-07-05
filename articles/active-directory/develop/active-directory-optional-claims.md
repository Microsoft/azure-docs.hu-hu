---
title: Ismerje meg, hogyan választható jogcímek, az Azure AD-alkalmazás megadása |} A Microsoft Docs
description: Útmutató egyéni vagy további jogcímek, az Azure Active Directory által kibocsátott SAML 2.0 és a JSON Web tokenek (JWT) tokenek hozzáadásához.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60eeb420c723e22b771b4b86b55c2ce7d6a23659
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536834"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Útmutató: Adja meg a választható jogcímeket, az Azure AD-alkalmazás

Az alkalmazásfejlesztők használatával választható jogcímek az Azure AD-alkalmazásokban adja meg, milyen jogcímeket szeretnének biztosítani az alkalmazásokban a jogkivonatokban. 

Nem kötelező jogcímeket is használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatok foglalandó további jogcímek.
- Változtathatja meg bizonyos jogcímek, az Azure AD eredményül a jogkivonatokban.
- Adja hozzá, és egyéni jogcímek, az alkalmazás eléréséhez.

Standard jogcímek listáját lásd: a [hozzáférési jogkivonat](access-tokens.md) és [id_token](id-tokens.md) jogcím-dokumentációt. 

Bár nem kötelező jogcímeket is 1.0-s verzió és a v2.0-formátumú jogkivonatokat, valamint SAML-jogkivonatok támogatottak, az érték a legtöbb nyújtanak történő áthelyezése 1.0-s verziója a 2.0-s verzió. Az egyik célja, a [v2.0 a Microsoft identity platform endpoint](active-directory-appmodel-v2-overview.md) az ügyfelek által az optimális teljesítmény biztosítása érdekében kisebb token méretű. Ennek eredményeképpen korábban szerepelni fog a hozzáférési és azonosító-jogkivonatokat több jogcím már nem találhatók a 2.0-s verziójú jogkivonatokban, és kifejezetten a alkalmazásonkénti alapján kell kérni.

**1. táblázat: Alkalmazhatósági**

| Fiók típusa | 1\.0-s verziójú jogkivonatok | 2\.0-s verziójú jogkivonatok  |
|--------------|---------------|----------------|
| Személyes Microsoft-fiók  | –  | Támogatott |
| Azure AD-fiók      | Támogatott | Támogatott |

## <a name="v10-and-v20-optional-claims-set"></a>1\.0-s és 2.0-s verziójú választható jogcímek készletébe

Az alkalmazásokkal való használatra alapértelmezés szerint elérhető nem kötelező jogcímek készlete alább láthatók. Az egyéni opcionális jogcímek az alkalmazáshoz adni, tekintse meg a [Címtárbővítmények](#configuring-directory-extension-optional-claims), az alábbi. Jogcímek hozzáadásakor a **hozzáférési jogkivonat**, ez vonatkozik a kért hozzáférési jogkivonatok *a* az alkalmazás (webes API-k), azokat *által* az alkalmazást. Ez biztosítja, hogy az ügyfelet, az API elérése, függetlenül attól, hogy a megfelelő adatok már jelen vannak a hozzáférési jogkivonat hitelesíti a rendszer az API-t használják.

> [!NOTE]
> A legtöbb ezeket a jogcímeket is szerepelnek JWTs az 1.0-s verziója és a 2.0-s verziójú jogkivonatok, de nem SAML-jogkivonatokat, kivéve, ahol ennek ellenezőjét jelöltük a jogkivonat típusa oszlopban. Felhasználói fiókok támogatja ezeket a jogcímeket, az "Felhasználó típusa" oszlopban jelölt egy részét.  A jogcímek felsorolt számos identitásrendszerében a felhasználók nem vonatkoznak (tehát nem a bérlő rendelkeznek `tenant_ctry` nem rendelkezik értékkel).  

**2. táblázat: 1.0-s és 2.0-s verziójú választható jogcímek készletéhez**

| Name (Név)                       |  Leírás   | Jogkivonat típusa | Felhasználó típusa | Megjegyzések  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Amikor a felhasználó legutóbbi hitelesített ideje. Lásd: OpenID Connect specifikációja.| JWT        |           |  |
| `tenant_region_scope`      | Az erőforrás-bérlő régió | JWT        |           | |
| `home_oid`                 | A vendégfelhasználók számára, a felhasználó bérlőjének a felhasználó Objektumazonosítóját.| JWT        |           | |
| `sid`                      | Munkamenet-azonosító, a munkamenet-felhasználó a Kijelentkezés használt. | JWT        |  Személyes és az Azure AD-fiókokat.   |         |
| `platf`                    | Eszközplatform    | JWT        |           | Eszköztípus ellenőrizheti a felügyelt eszközökre korlátozva.|
| `verified_primary_email`   | A felhasználó PrimaryAuthoritativeEmail forrása      | JWT        |           |         |
| `verified_secondary_email` | A felhasználó SecondaryAuthoritativeEmail forrása   | JWT        |           |        |
| `enfpolids`                | Kényszerített házirend azonosítók. A szabályzat az aktuális felhasználó értékelt azonosítók listáját. | JWT |  |  |
| `vnet`                     | VNET-adatok megadása. | JWT        |           |      |
| `fwd`                      | IP-cím.| JWT    |   | Felveszi az eredeti IPv4-címet a kérést küldő ügyfélnek (Ha egy virtuális hálózaton belül) |
| `ctry`                     | A felhasználó ország | JWT |  | Az Azure AD vissza a `ctry` jogcím nem kötelező, ha telepítve, és a jogcím értéke egy standard kétbetűs országkód, például FR, JP, SZ és így tovább. |
| `tenant_ctry`              | Erőforrás-bérlő ország | JWT | | |
| `xms_pdl`          | Az adatok előnyben részesített helye   | JWT | | A Multi-földrajzi bérlők számára ez az a 3 betűs kód megjelenítése a földrajzi régiót, a felhasználó szerepel. További információ: a [adatainak előnyben részesített helye az Azure AD Connect dokumentáció](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Például: `APC` Ázsia Csendes-óceáni számára. |
| `xms_pl`                   | A felhasználó preferált nyelvét  | JWT ||A felhasználó elsődleges nyelv, ha a beállítása. Származási hely a saját bérlőjén, a Vendég adathozzáférési forgatókönyvek esetében. R-CC formátumú ("en-us"). |
| `xms_tpl`                  | A bérlői elsődleges nyelv| JWT | | Az erőforrás-bérlő elsődleges nyelv, ha a beállítása. Formázott LL ("hu"). |
| `ztdid`                    | Beavatkozás nélküli telepítés azonosítója | JWT | | A használt eszközidentitás [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Címezhető e-mail a felhasználó, ha a felhasználó rendelkezik ilyennel.  | JWT, SAML | MSA, Azure AD | Ha a felhasználó a bérlő Vendég Ez az érték alapértelmezés szerint tartalmazza.  Felügyelt felhasználók (amelyeket a bérlőn belül) azt kell kérik, ez nem kötelező a jogcím, vagy a 2.0-s verziójú csak, és az OpenID hatókörének.  Felügyelt felhasználók esetén az e-mail-cím formájában kell megadni a [Office rendszergazdai portál](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Nem kötelező csoportjogcímek formázása |JWT, SAML| |A GroupMembershipClaims beállításával együtt használják a [alkalmazásjegyzék](reference-app-manifest.md), amely is kell állítani. További részletekért lásd: [jogcímek csoport](#Configuring-group-optional claims) alatt. Csoport jogcímek további információ: [csoportjogcímek konfigurálása](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Felhasználói fiók állapota-bérlőben. | JWT, SAML | | Ha a felhasználó tagja a bérlő, a értéke `0`. A Vendég, ha az értéke `1`. |
| `upn`                      | UserPrincipalName claim. | JWT, SAML  |           | Bár ez a jogcím automatikusan tartalmazza, mint egy nem kötelező jogcím csatolni a Vendég felhasználói esetben viselkedésének módosítása további tulajdonságok megadhat.  |

### <a name="v20-optional-claims"></a>Nem kötelező jogcímek 2.0-s verzió

Ezeket a jogcímeket 1.0-s verziójú Azure AD-jogkivonatok mindig szerepel, de nem tartalmazza a 2.0-s verziójú jogkivonatokban, kivéve, ha a kért. Ezeket a jogcímeket (azonosító-jogkivonatokat és hozzáférési jogkivonatok) JWTs csak vonatkoznak. 

**3. táblázat: csak v2.0 választható jogcímek**

| JWT-jogcím     | Name (Név)                            | Leírás                                | Megjegyzések |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-cím                      | Az IP-címet az ügyfél a bejelentkezett.   |       |
| `onprem_sid`  | A helyszíni biztonsági azonosítója |                                             |       |
| `pwd_exp`     | Jelszó lejárati ideje        | A dátumot/időt, amikor a jelszó lejár. |       |
| `pwd_url`     | Jelszó URL-Címének módosítása             | Egy URL-címet, amely a felhasználó ellátogathat a jelszó módosítására.   |   |
| `in_corp`     | Vállalati hálózaton belül        | Ha az ügyfél bejelentkezik a vállalati hálózatról jelek. Ha nem, a jogcím nem tartalmaz.   |  Ki-alapú a [megbízható IP-címek](../authentication/howto-mfa-mfasettings.md#trusted-ips) beállításokat az MFA-ban.    |
| `nickname`    | Becenév                        | A felhasználó, az első vagy utolsó külön további neve. | 
| `family_name` | Vezetéknév                       | Az utolsó neve, Vezetéknév vagy felhasználó családnév biztosít a user objektum a. <br>"family_name":"Miller" | Az MSA és az Azure ad-ben támogatott   |
| `given_name`  | Utónév                      | Itt az első vagy az "adott" a felhasználó nevét a user objektum készletként.<br>"given_name": "Frank"                   | Az MSA és az Azure ad-ben támogatott  |
| `upn`         | Egyszerű felhasználónév | Egy azonosítóval, a felhasználó a username_hint paraméterrel használható.  A felhasználó nem egy tartós azonosítót, és nem használható a fontos adatokat. | Lásd: [további tulajdonságok](#additional-properties-of-optional-claims) alább a jogcím-konfigurációhoz. |

### <a name="additional-properties-of-optional-claims"></a>Nem kötelező jogcímek további tulajdonságok

Egyes nem kötelező jogcímek konfigurálható megváltoztatni a jogcímet ad vissza. Ezek a további tulajdonságok vannak többnyire kódtáblázatokhoz használják a különböző adatokat elvárásainak a helyszíni alkalmazások migrálása érdekében (például `include_externally_authenticated_upn_without_hash` segít az ügyfelekkel, hogy nem tudja kezelni a kettős kereszt (`#`) UPN-jét)

**4. táblázat: Értékek nem kötelező jogcím konfigurálása**

| Tulajdonság neve  | További tulajdonság neve | Leírás |
|----------------|--------------------------|-------------|
| `upn`          |                          | A SAML- és a JWT-válaszok és az 1.0-s és 2.0-s verziójú jogkivonatok használható. |
|                | `include_externally_authenticated_upn`  | A Vendég UPN adatbázisában található az erőforrás-bérlő tartalmazza. Például: `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Ugyanaz, mint a fenti, azzal a különbséggel, hogy a kivonat jelöli meg (`#`) cserélése aláhúzásjeleket (`_`), például `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>További tulajdonságok példa

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Ez OptionalClaims objektum okoz az azonosító jogkivonat, az ügyfél egy másik egyszerű felhasználónév a további saját bérlőjén és erőforrás-bérlő adatai tartalmazzák. Ez csak módosítja a `upn` jogcím a jogkivonatban, ha a felhasználó a Vendég a bérlőben (amely egy másik Identitásszolgáltatót használja a hitelesítéshez). 

## <a name="configuring-optional-claims"></a>Nem kötelező jogcím konfigurálása

Az alkalmazás nem kötelező jogcímek az alkalmazásjegyzéknek (lásd például alább) módosításával konfigurálhatja. További információ: a [ismertetése az Azure AD application manifest cikk](reference-app-manifest.md).

> [!IMPORTANT]
> Hozzáférési jogkivonatok olyan **mindig** az erőforrás nem az ügyfél a jegyzék használatával létrehozott.  Igen, a kérelem `...scope=https://graph.microsoft.com/user.read...` az Erőforrás grafikon.  Így a hozzáférési jogkivonat jön létre a Graph-jegyzékfájl, nem az ügyfél jegyzék használatával.  A jegyzékfájl az alkalmazás módosítása máshogy néznek ki a gráf jogkivonatok sosem okozza.  Annak érdekében, hogy ellenőrizze, hogy a `accessToken` módosítások vannak érvényben, kérjen az alkalmazás, nem egy másik alkalmazás tokent.  

**Minta séma:**

```json
"optionalClaims":  
   {
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

### <a name="optionalclaims-type"></a>OptionalClaims típusa

Deklarálja a választható jogcímek, az alkalmazás által kért. Alkalmazás választható jogcímeket adja vissza a jogkivonatok (azonosító jogkivonat, access token, SAML 2 jogkivonat) három típusú megkaphatja a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól származó konfigurálhatja. Az alkalmazás beállíthatja a választható minden típusú jogkivonatot adott vissza jogcímeket külön készletét. Az alkalmazás entitás OptionalClaims tulajdonság OptionalClaims objektum.

**5. táblázat: OptionalClaims tulajdonságait**

| Name (Név)        | Típus                       | Leírás                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | A gyűjtemény (OptionalClaim) | A nem kötelező a JWT-azonosító jogkivonat visszaküldött jogcímek. |
| `accessToken` | A gyűjtemény (OptionalClaim) | A JWT jogkivonat nem kötelező jogcímeket adja vissza. |
| `saml2Token`  | A gyűjtemény (OptionalClaim) | Az SAML-jogkivonat a választható jogcímeket adja vissza.   |

### <a name="optionalclaim-type"></a>OptionalClaim típusa

Tartalmaz egy választható jogcím egy alkalmazás vagy az egyszerű szolgáltatás társítva. IdToken accessToken és saml2Token tulajdonságait a [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) típus OptionalClaim gyűjteménye.
Ha az egy adott jogcím támogatja, a további tulajdonságok mező használatával OptionalClaim viselkedését is módosíthatja.

**6. táblázat: OptionalClaim tulajdonságait**

| Name (Név)                 | Típus                    | Leírás                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | A választható jogcímszabály neve.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A forrás (címtárobjektum) a jogcím. Előre meghatározott jogcím és a felhasználói jogcímeket bővítmény tulajdonságai vannak. Ha a forrás értéke null, a jogcím előre meghatározott jogcím nem kötelező. Ha a forrás értéke felhasználó, a name tulajdonság értéke az a felhasználói objektum bővítménytulajdonság. |
| `essential`            | Edm.Boolean             | Ha az értéke igaz, a jogcímek, az ügyfél által megadott az adott feladat, a felhasználó által kért egy gördülékeny hitelesítési élmény biztosításához szükséges. Az alapértelmezett értéke FALSE (hamis).                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Az igényt további tulajdonságai. Ha egy tulajdonság már létezik ebben a gyűjteményben, módosítja a a name tulajdonság nem kötelező jogcímek viselkedését.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Címtár kiterjesztése nem kötelező jogcím konfigurálása

A standard szintű választható jogcímek készletébe mellett tartalmazza a directory-sémabővítmények jogkivonatok is beállíthatja. További információ: [Directory sémakiterjesztései](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Ez a funkció akkor hasznos, amelyek az alkalmazás használhatja – például további felhasználói adatokat, egy további azonosító vagy a fontos konfigurációs beállítás, amelyet a felhasználó be van állítva. 

> [!Note]
> - Directory sémakiterjesztései az Azure AD csak szolgáltatásai, így ha az alkalmazás jegyzékfájlja kérelmek egyedi bővítmény és a egy MSA felhasználó bejelentkezik az alkalmazás, ezek a bővítmények nem állítható vissza.
> - Az Azure AD nem kötelező jogcímek csak működnek az Azure AD-bővítmény és a munkahelyi Microsoft Graph címtár kiterjesztése nem működik. Mindkét API-k megkövetelése a `Directory.ReadWriteAll` engedéllyel, amely a rendszergazdák csak is beleegyezés.

### <a name="directory-extension-formatting"></a>Címtárkiterjesztés formázása

A bővítményattribútumok, használja a bővítmény teljes nevére (a következő formátumban: `extension_<appid>_<attributename>`) az alkalmazásjegyzékben. A `<appid>` meg kell egyeznie a jogcím kér az alkalmazás azonosítója. 

A JWT belül ezeket a jogcímeket fog kibocsátott, a következő név formátumban: `extn.<attributename>`.

Az SAML-jogkivonatok belül ezeket a jogcímeket fog bocsátja ki az URI formátuma a következő: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Nem kötelező csoportjogcímek konfigurálása

   > [!NOTE]
   > Nyilvános előzetes verzióban arra, hogy a felhasználók és csoportok szinkronizálása a helyszíni csoportnevek küldik.

Ez a szakasz ismerteti a konfigurációs beállítások módosítása a csoport a helyszíni Windows Active Directoryból szinkronizált attribútumok csoportnak az alapértelmezett csoport objectID jogcímeket használt attribútum nem kötelező jogcímeket alatt.

> [!IMPORTANT]
> Lásd: [csoportjogcímek alkalmazások konfigurálása az Azure ad-vel](../hybrid/how-to-connect-fed-group-claims.md) további részletekért, többek között a nyilvános előzetes verzió, a helyszíni attribútumok a csoportjogcímek fontos figyelmeztetések.

1. A portálon az Azure Active Directory -> Alkalmazás -> -> Válassza ki a regisztrációk alkalmazás-jegyzékfájl >

2. A groupMembershipClaim módosításával csoport tagsági jogcímek engedélyezése

   Az érvényes értékek a következők:

   - "All"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Példa:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Csoport objectid azonosítójának fog bocsátja ki a csoport alapértelmezés szerint a jogcím értéke.  A helyi csoport attribútumait tartalmazza, vagy módosítsa a jogcímtípus szerepkör jogcím értéke módosításához használja a következő OptionalClaims konfiguráció:

3. Állítsa be a csoport konfigurációja nem kötelező jogcímekben.

   Ha szeretné a jogkivonat csoportok a helyszíni AD-csoport attribútumok az opcionális jogcímek szakaszban adja meg, melyik typ tokenu választható jogcímet kell alkalmazni, a kért választható jogcím és minden egyéb tulajdonságot, kívánt nevét tartalmazza.  Több tokentípusokat is listázva lehet:

   - idToken a OIDC azonosító jogkivonat
   - az OAuth/OIDC jogkivonat a hozzáférési tokent
   - Az SAML-jogkivonatok Saml2Token.

   > [!NOTE]
   > SAML1.1 és a SAML2.0 formátumú jogkivonatokat vonatkozik a Saml2Token típusa

   Megfelelő jogkivonat alkalmazástípust módosítsa a csoportok jogcím OptionalClaims szakasz a jegyzékfájlban. A OptionalClaims séma a következőképpen történik:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Nem kötelező jogcímek séma | Érték |
   |----------|-------------|
   | **név:** | "Csoportok" kell lennie. |
   | **Forrás:** | Nincs használatban. Nincs megadva vagy null értéket adjon meg |
   | **alapvető:** | Nincs használatban. Nincs megadva, vagy adja meg a hamis |
   | **additionalProperties:** | További tulajdonságainak listája.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   In additionalProperties only one of "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name” are required.  Ha egynél több található, az első, és a többi figyelmen kívül hagyja.

   Egyes alkalmazásoknak a felhasználó a szerepkör jogcím csoport adatait.  Ha módosítani szeretné a szerepkör jogcím egy csoportból jogcímet a jogcím típusa, adja hozzá a további tulajdonságok "emit_as_roles".  A csoport értékeket fogja a szerepkör jogcím bocsátja ki.

   > [!NOTE]
   > "Emit_as_roles" használata egyetlen alkalmazás-szerepkör konfigurálva, hogy a felhasználó lesz hozzárendelve a szerepkör jogcím nem jelennek meg

**Példák:** Csoportok kibocsátható, az OAuth hozzáférési tokenek dnsDomainName\sAMAccountName formátumban nevei

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Kibocsátható csoportnevek netbiosDomain\sAMAccountName formátumban kell visszaadni, SAML és OIDC azonosító-jogkivonatokat a jogcím a szerepkörök:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>Nem kötelező jogcímek példa

Ebben a szakaszban megtekintheti, hogyan használhatja a választható jogcímek szolgáltatást az alkalmazás egy forgatókönyvet is végig.
Több lehetőség van egy alkalmazás identitás konfiguráció engedélyezése és konfigurálása nem kötelező jogcímek tulajdonságait frissítési érhető el:
-   Az alkalmazásjegyzékben módosíthatja. Az alábbi példában ez a módszer használatával a konfiguráció beállítása. Olvassa el a [ismertetése az Azure AD application manifest dokumentum](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) első bevezetést a jegyzékfájlban.
-   Akkor is használó alkalmazások írása a [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) frissíteni az alkalmazást. A [entitások és összetett típusok segédletben](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) az a Graph API-referencia útmutató segítségére lehet a nem kötelező jogcímek konfigurálása.

**Példa** Az alábbi példában egy alkalmazásjegyzékben jogcímalapú hozzáférés, az ID és a SAML hozzáadandó módosítják a tokenek számára az alkalmazás.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A hitelesítése után után válassza ki azt az oldal jobb felső sarokban válassza ki az Azure AD-bérlő.
1. Válassza ki **Alkalmazásregisztrációk** bal alsó oldalán.
1. Keresse meg az alkalmazás konfigurálása nem kötelező jogcímeket a listában, és kattintson rá.
1. Kattintson az alkalmazás oldaláról **Manifest** , nyissa meg a beágyazott alkalmazásjegyzék-szerkesztőben. 
1. Közvetlenül szerkesztheti a jegyzékfájlt a szerkesztő használatával. A jegyzékfájl követi a sémát a [alkalmazás entitás](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest), és formázza a jegyzékfájl egyszer mentve. Új elem megjelenik a `OptionalClaims` tulajdonság.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
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

    Ebben az esetben másik választható jogcímek hozzáadott minden típusú jogkivonatot, amely az alkalmazás fogadhat. Az azonosító-jogkivonatokat mostantól tartalmazza a teljes képernyőn összevont felhasználók esetében az egyszerű Felhasználónevet (`<upn>_<homedomain>#EXT#@<resourcedomain>`). A hozzáférési tokenek kérő ügyfelek számára az alkalmazás most már tartalmazza a auth_time jogcímet. Az SAML-jogkivonatok mostantól tartalmazza a skypeId directory-séma kiterjesztését (ebben a példában az alkalmazás az alkalmazás csomagazonosítója ab603c56068041afb2f6832e2a17e237). Az SAML-jogkivonatok fogja elérhetővé tenni a Skype-azonosító, `extension_skypeId`.

1. Ha elkészült, a jegyzékfájl frissítése, kattintson a **mentése** a jegyzékfájl mentése

## <a name="next-steps"></a>További lépések

További információ az Azure AD által támogatott szabványos jogcímeket.

- [Azonosító-jogkivonatokat](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
