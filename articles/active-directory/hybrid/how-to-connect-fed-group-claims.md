---
title: Csoportos jogcímek konfigurálása a Azure Active Directorykal rendelkező alkalmazásokhoz | Microsoft Docs
description: Információk a csoportos jogcímek konfigurálásáról az Azure AD-vel való használatra.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: bef5942707c1ded22ba82bdb0d945b9fdb23fffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349350"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Jogcímek konfigurálása az alkalmazásokhoz Azure Active Directory

A Azure Active Directory az alkalmazásokon belüli használatra a felhasználók csoport tagsági adatait is megadhatja a jogkivonatokban.  Két fő mintázat támogatott:

- A Azure Active Directory objektumazonosító (OID) attribútummal azonosított csoportok
- Active Directory (AD) szinkronizált csoportok és felhasználók sAMAccountName vagy GroupSID attribútumai által azonosított csoportok

> [!IMPORTANT]
> A funkcióra vonatkozóan számos figyelmeztetést kell megjegyezni:
>
>- A helyszínen szinkronizált sAMAccountName-és biztonsági azonosítók (SID-ek) használatának támogatása lehetővé teszi a meglévő alkalmazások áthelyezését AD FS és más identitás-szolgáltatókból. Az Azure AD-ben felügyelt csoportok nem tartalmazzák a jogcímek kibocsátásához szükséges attribútumokat.
>- Nagyobb szervezeteknél a felhasználók azon csoportjai száma, amelyeknek a felhasználó tagja, túllépheti azt a korlátot, amelyet a Azure Active Directory felvesz a tokenbe. 150 csoport egy SAML-tokenhez és egy JWT 200. Ez előre nem látható eredményekhez vezethet. Ha a felhasználók nagy számú csoporttagság közül választhatnak, javasoljuk, hogy a jogcímek által kibocsátott csoportokat az alkalmazás megfelelő csoportjaira korlátozza.  
>- Az új alkalmazások fejlesztéséhez, illetve azokban az esetekben, amikor az alkalmazás konfigurálható, és ahol nincs szükség beágyazott csoport támogatására, javasoljuk, hogy az alkalmazáson belüli engedélyezés a csoportok helyett az alkalmazás szerepkörein alapuljon.  Ez korlátozza a tokenbe bekerülő információk mennyiségét, biztonságosabb, és elkülöníti a felhasználói hozzárendelést az alkalmazás konfigurációjától.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>A AD FS és más identitás-szolgáltatótól áttelepítésre kerülő alkalmazások jogcímeinek csoportosítása

A AD FS való hitelesítésre konfigurált számos alkalmazás a Windows AD Group-attribútumok formájában használja a csoporttagság-információkat.   Ezek az attribútumok a csoport sAMAccountName, amelyek a tartománynevek vagy a Windows-csoport biztonsági azonosítója (GroupSID) alapján lehetnek minősítettek.  Ha az alkalmazás összevonása AD FS, a AD FS a TokenGroups függvény használatával kéri le a felhasználó csoporttagságait.

Egy alkalmazás, amely a AD FS igényeknek megfelelő formátumban lett áthelyezve. A csoport-és szerepkör-jogcímek a tartomány minősített sAMAccountName vagy a GroupSID szinkronizált, Active Directory nem a csoport Azure Active Directory objectID származó adatokból is kiAzure Active Directory állíthatók.

A csoportos jogcímek által támogatott formátumok a következők:

- **Azure Active Directory csoport ObjectId** (az összes csoport számára elérhető)
- **sAMAccountName** (Active Directory) szinkronizált csoportok számára elérhető
- **NetbiosDomain\sAMAccountName** (Active Directory) szinkronizált csoportok számára elérhető
- **DNSDomainName\sAMAccountName** (Active Directory) szinkronizált csoportok számára elérhető
- Helyszíni **csoport biztonsági azonosítója** (a Active Directoryról szinkronizált csoportok számára elérhető)

> [!NOTE]
> a sAMAccountName és a helyszíni SID attribútumok csak a Active Directory szinkronizált csoport objektumain érhetők el.   Azure Active Directory vagy Office 365 létrehozott csoportokban nem érhetők el.   A Azure Active Directoryban konfigurált alkalmazások szinkronizált helyszíni csoportok attribútumai csak szinkronizált csoportok számára válnak elérhetővé.

## <a name="options-for-applications-to-consume-group-information"></a>Beállítások a csoport adatainak felhasználásához

Az alkalmazások meghívhatják az MS Graph groups végpontot a csoport adatainak beszerzéséhez a hitelesített felhasználó számára. Ez a hívás biztosítja, hogy a felhasználó összes csoportja elérhető legyen, még akkor is, ha nagy számú csoport vesz részt.  A csoport enumerálása ezután független a tokenek méretének korlátaitól.

Ha azonban egy meglévő alkalmazás jogcímek alapján kívánja felhasználni a csoport adatait, Azure Active Directory több különböző jogcím-formátummal is konfigurálható.  Vegye figyelembe a következő lehetőségeket:

- Ha csoporttagság használata az alkalmazáson belüli hitelesítéshez, a csoport ObjectID használata ajánlott. A ObjectID csoport nem módosítható, és a Azure Active Directoryban egyedi, és minden csoport számára elérhető.
- Ha a helyszíni csoport sAMAccountName használja az engedélyezéshez, használjon tartományneveket a tartomány minősített neveivel.  kevesebb esélye van a nevek összeütközésére. a sAMAccountName egyedi lehet egy Active Directory tartományon belül, de ha több Active Directory tartomány van szinkronizálva egy Azure Active Directory-Bérlővel, több csoport is rendelkezhet ugyanazzal a névvel.
- Vegye fontolóra az [alkalmazási szerepkörök](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) használatát, hogy a csoporttagság és az alkalmazás közötti átirányítási réteget biztosítson.   Az alkalmazás ezután belső engedélyezési döntéseket tesz a jogkivonatban lévő szerepkör-jogcímek alapján.
- Ha az alkalmazás úgy van konfigurálva, hogy az Active Directoryról szinkronizált csoport-attribútumokat kapjon, és a csoport nem tartalmazza ezeket az attribútumokat, akkor nem fog szerepelni a jogcímek között.
- A jogkivonatok csoportba foglalása beágyazott csoportokat tartalmaz, kivéve ha az alkalmazáshoz hozzárendelt csoportokra vonatkozó jogcímek korlátozására van szükség.  Ha a felhasználó a GroupB tagja, és a GroupB a Groupa tagja, akkor a felhasználóhoz tartozó jogcímek a Groupe és a GroupB is tartalmazzák. Ha egy szervezet felhasználóinak nagy számú csoporttagság van, a tokenben felsorolt csoportok száma növelheti a token méretét.  Azure Active Directory korlátozza azoknak a csoportoknak a számát, amelyeket a rendszer 150 tokenben bocsát ki az SAML-kijelentésekhez, és 200 a JWT.  Ha a felhasználó nagyobb számú csoport tagja, akkor a csoportok kimaradnak, és a diagram végpontra mutató hivatkozás is szerepel a csoport adatainak beszerzéséhez.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>A Active Directoryről szinkronizált csoportok attribútumainak használatának előfeltételei

Ha az ObjectId formátumot használja, akkor a csoporttagság-jogcímek jogkivonatokban is kiadhatók bármely csoport esetében. Ha a csoport ObjectId eltérő formátumban szeretné használni a jogcímeket, a csoportokat szinkronizálni kell Active Directory a Azure AD Connect használatával.

A Azure Active Directory konfigurálásának két lépése van Active Directory csoportoknak.

1. **Csoportok nevének szinkronizálása Active Directory** Mielőtt Azure Active Directory a csoport-vagy szerepkör-jogcímek csoportjai számára a csoportok nevét vagy a helyszíni csoport biztonsági azonosítóját, a szükséges attribútumokat szinkronizálni kell a Active Directory.  Azure AD Connect 1.2.70 vagy újabb verziót kell futtatnia.   A Azure AD Connect korábbi verziói a 1.2.70 szinkronizálják a csoport objektumait Active Directory, de nem tartalmazzák a szükséges csoportnév-attribútumokat.  Frissítsen az aktuális verzióra.

2. **Az alkalmazás regisztrációjának konfigurálása a Azure Active Directoryban a jogkivonatokban lévő csoportos jogcímek belefoglalásához** A csoportos jogcímeket a portál Enterprise Applications (vállalati alkalmazások) szakaszában, vagy az alkalmazás-regisztrációk szakaszban lehet használni.  Ha a csoport jogcímeit az alkalmazás jegyzékfájljában szeretné konfigurálni, tekintse meg az alábbi "Azure Active Directory alkalmazás-regisztráció konfigurálása a csoport attribútumaihoz" című szakaszt.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Jogcímek hozzáadása az SAML-alkalmazások tokenekhez SSO-konfiguráció használatával

Egy katalógus vagy nem katalógus SAML-alkalmazás csoportos jogcímeinek konfigurálásához nyissa meg a **vállalati alkalmazásokat**, kattintson a listában szereplő alkalmazásra, válassza az **egyszeri bejelentkezés konfigurálása** lehetőséget, majd válassza a **felhasználói attribútumok & jogcímek** lehetőséget.

Kattintson a **Csoport hozzáadása** elemre.  

![Képernyőfelvétel: a "felhasználói attribútumok & jogcímek" lap, amelynél a "Csoport hozzáadása" lehetőség be van jelölve.](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

A választógombok használatával kiválaszthatja, hogy mely csoportok szerepeljenek a jogkivonatban

![A "csoportok jogcímei" ablakot a "biztonsági csoportok" beállítással megjelenítő képernyőkép.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Kiválasztás | Leírás |
|----------|-------------|
| **Minden csoport** | Biztonsági csoportokat és terjesztési listát és szerepköröket bocsát ki.  |
| **Biztonsági csoportok** | Biztonsági csoportokat bocsát ki, a felhasználó tagja a groups jogcímnek |
| **Címtárbeli szerepkörök** | Ha a felhasználóhoz címtárbeli szerepkörök vannak hozzárendelve, a rendszer "wids" jogcímként bocsátja ki őket (a csoportok jogcíme nem lesz kibocsátva) |
| **Az alkalmazáshoz hozzárendelt csoportok** | Csak azokat a csoportokat bocsátja ki, amelyek kifejezetten az alkalmazáshoz vannak rendelve, és a felhasználó tagja |

Például az összes olyan biztonsági csoport kibocsátásához, amely tagja a felhasználónak, válassza a biztonsági csoportok lehetőséget.

![Képernyőkép, amely megjeleníti a "csoportok jogcímei" ablakot a "biztonsági csoportok" beállításnál, a "forrás attribútum" legördülő menüt pedig megnyitva.](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Ha az Azure AD objectIDs helyett Active Directory szinkronizált Active Directory attribútumokkal szeretne csoportokat kibocsátani, válassza ki a kívánt formátumot a legördülő listából. A jogcímek között csak a Active Directory szinkronizált csoportok lesznek felszámítva.

![A "forrás attribútum" legördülő menüt megjelenítő képernyőkép.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Ha csak az alkalmazáshoz hozzárendelt csoportokat kívánja kibocsátani, válassza **az alkalmazáshoz hozzárendelt csoportok** elemet.

![Képernyőfelvétel: a "csoport jogcímei" ablak "az alkalmazáshoz rendelt csoportokkal" van kiválasztva.](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Az alkalmazáshoz rendelt csoportok szerepelni fognak a jogkivonatban.  Más csoportok, amelyeknek a felhasználó tagja, el lesz hagyva.  Ha ezt a lehetőséget választja, a beágyazott csoportok nem szerepelnek, és a felhasználónak az alkalmazáshoz rendelt csoport közvetlen tagjának kell lennie.

Az alkalmazáshoz rendelt csoportok módosításához válassza ki az alkalmazást a **vállalati alkalmazások** listából, majd kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

Tekintse meg a [felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) című dokumentumot, ahol megtudhatja, hogyan kezelheti a csoportok hozzárendelését az alkalmazásokhoz.

### <a name="advanced-options"></a>Speciális beállítások

A csoportos jogcímek kibocsátása a speciális beállítások területen található beállításokkal módosítható

A csoportjogcím nevének testreszabása: Ha be van jelölve, egy másik jogcím-típus adható meg a csoport jogcímeihez.   A névtér mezőben adja meg a jogcím típusát a név mezőben, valamint a jogcím opcionális névterét.

![Képernyőkép: "speciális beállítások" szakasz, "a csoport nevének testreszabása" kiválasztott és "név" és "névtér" érték van megadva.](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Egyes alkalmazásokban a csoporttagság információit kell megjeleníteni a "szerepkör" jogcímben. A felhasználó csoportjait szerepkörökként is kibocsáthatja a "szerepkör-jogcímek kibocsátása" jelölőnégyzet bejelölésével.

![Képernyőkép: "speciális beállítások" szakasz, "a csoport nevének testreszabása" és a "csoportok kibocsátása szerepkör-jogcímként" beállítás látható.](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Ha a csoportok adatai szerepkörként való kibocsátásának lehetősége van használatban, csak a csoportok jelennek meg a szerepkör-jogcímben.  A felhasználó által hozzárendelt összes alkalmazás-szerepkör nem jelenik meg a szerepkör-jogcímben.

### <a name="edit-the-group-claims-configuration"></a>A csoport jogcímek konfigurációjának szerkesztése

Miután hozzáadta a csoportjogcím-konfigurációt a felhasználói attribútumok & jogcím-konfigurációhoz, a rendszer szürkévé teszi a csoportos jogcímek hozzáadásának lehetőségét.  A csoportjogcím konfigurációjának módosításához kattintson a csoport jogcímere a **további jogcímek** listájában.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Az Azure AD-alkalmazás regisztrációjának konfigurálása a csoport attribútumaihoz

A csoportos jogcímeket az [alkalmazás jegyzékfájljának](../../active-directory/develop/reference-app-manifest.md) [opcionális jogcímek](../../active-directory/develop/active-directory-optional-claims.md) szakaszában is lehet konfigurálni.

1. A portálon – >Azure Active Directory – > alkalmazás regisztrációi – >válassza az alkalmazás – >jegyzékfájlt

2. Csoporttagság-jogcímek engedélyezése a groupMembershipClaim módosításával

Az érvényes értékek a következők:

| Kiválasztás | Leírás |
|----------|-------------|
| **Összes** | Biztonsági csoportokat, terjesztési listát és szerepköröket bocsát ki |
| **"SecurityGroup"** | Biztonsági csoportokat bocsát ki, a felhasználó tagja a groups jogcímnek |
| **"DirectoryRole"** | Ha a felhasználóhoz címtárbeli szerepkörök vannak hozzárendelve, a rendszer "wids" jogcímként bocsátja ki őket (a csoportok jogcíme nem lesz kibocsátva) |
| **Alkalmazáscsoport** | Csak azokat a csoportokat bocsátja ki, amelyek kifejezetten az alkalmazáshoz vannak rendelve, és a felhasználó tagja |
| **NEz egy** | A rendszer nem ad vissza csoportokat. (A nem sensetive, így nem is működik, és közvetlenül az alkalmazás jegyzékfájljában is megadható.) |

   Például:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Alapértelmezés szerint a csoport ObjectIDs a rendszer a csoportos jogcím értékét fogja kiállítani.  Ha módosítani szeretné a jogcím értékét, hogy a tartalmazza a helyszíni csoport attribútumait, vagy módosítani szeretné a jogcím típusát a szerepkörre, használja a OptionalClaims-konfigurációt az alábbiak szerint:

3. Adja meg a csoport neve konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a tokenben lévő csoportok tartalmazzák a helyszíni AD-csoport attribútumait, akkor a választható jogcímek szakaszban adja meg, hogy melyik jogkivonat-típust kell alkalmazni.  Több jogkivonat-típus is szerepelhet:

   - a OIDC azonosító token idToken
   - a OAuth/OIDC hozzáférési token accessToken
   - SAML-tokenek Saml2Token.

   > [!NOTE]
   > A Saml2Token típusa SAML 1.1 és SAML 2.0 formátumú jogkivonatokra is vonatkozik.

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

   > [!NOTE]
   > Ha a "emit_as_roles" minden olyan alkalmazási szerepkört használ, amelyet a felhasználóhoz rendeltek, nem jelennek meg a szerepkör-jogcímben

### <a name="examples"></a>Példák

Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\SAMAccountName formátumban

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Az SAML-és OIDC-azonosító jogkivonatokban a netbiosDomain\samAccountName formátumban visszaadott csoportok nevének kibocsátása:

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

## <a name="next-steps"></a>Következő lépések

- [Engedélyezés hozzáadása csoportok & csoportok jogcímek használata ASP.NET Core webalkalmazáshoz (kód minta)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Szerepkörjogcímek konfigurálása](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
