---
title: Alkalmazás SAML-jogkivonat jogcímének testreszabása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szabhatja testre a Microsoft identitásplatformja által kiadott jogcímeket a vállalati alkalmazások SAML-jogkivonatában.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 25e737afb524cb8c6f45ac8e99f46a8064ae7855
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598839"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>How to: customize claims issued in the SAML token for enterprise applications (Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz)

Napjainkban a Microsoft identitásplatformja támogatja az egyszeri bejelentkezést (SSO) a legtöbb vállalati alkalmazással, beleértve az Azure AD-alkalmazásgyűjteménybe előre integrált alkalmazásokat és az egyéni alkalmazásokat is. Amikor egy felhasználó az SAML 2.0 protokollal hitelesít egy alkalmazást a Microsoft identitásplatformján keresztül, a Microsoft identitásplatformja egy jogkivonatot küld az alkalmazásnak (EGY HTTP POST-on keresztül). Ezután az alkalmazás ellenőrzi és felhasználja a jogkivonatot, hogy bejelentkeztetse a felhasználót a felhasználónév és jelszó kérése helyett. Ezek az SAML-jogkivonatok jogcímekként ismert információkat tartalmaznak a *felhasználóról.*

A *jogcím* olyan információ, amit az identitásszolgáltató a felhasználóról a felhasználó számára ki adott jogkivonatban állít ki. Az [SAML-jogkivonatban](https://en.wikipedia.org/wiki/SAML_2.0)ezeket az adatokat általában az SAML Attribútum utasítás tartalmazza. A felhasználó egyedi azonosítóját általában az SAML-tulajdonos, más néven névazonosító képviseli.

Alapértelmezés szerint a Microsoft identitásplatformja SAML-jogkivonatot ad ki az alkalmazásnak, amely a felhasználó felhasználónevének (más néven egyszerű felhasználónevének) az Azure AD-beli értékét tartalmazó jogcímet tartalmaz, amely egyedileg képes azonosítani `NameIdentifier` a felhasználót. Az SAML-jogkivonat további jogcímeket is tartalmaz, amelyek tartalmazzák a felhasználó e-mail-címét, vezetéknevét és vezetéknevét.

Az ALKALMAZÁS SAML-jogkivonatában kiadott jogcímek megtekintéséhez vagy szerkesztéséhez nyissa meg az alkalmazást a Azure Portal. Ezután nyissa meg **a Felhasználói attribútumok & jogcímek szakaszt.**

![Nyissa meg a Felhasználói attribútumok & jogcímek szakaszt a Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Az SAML-jogkivonatban kiadott jogcímek szerkesztésének két lehetséges oka lehet:

* Az alkalmazás megköveteli, hogy a vagy a NameID jogcím nem az Azure AD-ban tárolt felhasználónév (vagy egyszerű `NameIdentifier` felhasználónév) legyen.
* Az alkalmazás úgy lett megírva, hogy a jogcím-URI-k vagy jogcímértékek egy másik készletét követeli meg.

## <a name="editing-nameid"></a>Névazonosító szerkesztése

A NameID (névazonosító érték) szerkesztése:

1. Nyissa meg **a Névazonosító érték lapot.**
1. Válassza ki az attribútumra alkalmazni kívánt attribútumot vagy átalakítást. Igény szerint megadhatja a NameID jogcím formátumát is.

   ![A NameID (névazonosító) érték szerkesztése](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID formátum

Ha az SAML-kérelem a NameIDPolicy elemet egy adott formátummal tartalmazza, akkor a Microsoft identitásplatformja a kérés formátumát is be fogja fogni.

Ha az SAML-kérelem nem tartalmazza a NameIDPolicy elemet, akkor a Microsoft identitásplatformja ki fogja adni a NameID azonosítót az Ön által megadott formátumban. Ha nincs megadva formátum, a Microsoft identitásplatformja a kiválasztott jogcímforráshoz társított alapértelmezett forrásformátumot fogja használni.

A **Névazonosító formátumának kiválasztása legördülő** menüben az alábbi lehetőségek közül választhat.

| NameID formátum | Description |
|---------------|-------------|
| **Alapértelmezett** | A Microsoft identitásplatformja az alapértelmezett forrásformátumot fogja használni. |
| **Állandó** | A Microsoft identitásplatformja a Persistent (Állandó) formátumot fogja használni NameID formátumként. |
| **EmailAddress** | A Microsoft identitásplatformja az EmailAddress formátumot fogja használni NameID formátumként. |
| **Meghatározatlan** | A Microsoft identitásplatformja az Unspecified formátumot fogja használni NameID formátumként. |

Az átmeneti névazonosító is támogatott, de nem érhető el a legördülő menüben, és nem konfigurálható az Azure oldalán. További információ a NameIDPolicy attribútumról: [Single Sign-On SAML protocol](single-sign-on-saml-protocol.md)..

### <a name="attributes"></a>Attribútumok

Válassza ki a kívánt forrást `NameIdentifier` a (vagy NameID) jogcímhez. Az alábbi lehetőségek közül választhat.

| Név | Leírás |
|------|-------------|
| E-mail | A felhasználó e-mail címe |
| userprincipalName | A felhasználó egyszerű felhasználóneve (UPN) |
| onpremisessamaccountname | A helyszíni Azure AD-ről szinkronizált SAM-fiók neve |
| objectid (objektumazonosító) | A felhasználó objektumazonosítója az Azure AD-ban |
| employeeid (alkalmazottazonosító) | A felhasználó alkalmazotti azonosítója |
| Címtárbővítmények | [Címtárbővítmények szinkronizálása a helyi Active Directory szinkronizálási Azure AD Connect használatával](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Bővítményattribútumok 1–15 | Az Azure AD-séma kiterjesztéséhez használt helyszíni bővítményattribútumok |

További információ: [3. táblázat: Érvényes azonosítóértékek forrásonként.](reference-claims-mapping-policy-type.md#table-3-valid-id-values-per-source)

Bármilyen állandó (statikus) értéket hozzárendelhet bármely, az Azure AD-ban megadott jogcímhez. Konstans érték hozzárendeléshez kövesse az alábbi lépéseket:

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>a Jogcímek felhasználói attribútumok **&** szakaszban kattintson a  Szerkesztés ikonra a jogcímek szerkesztéséhez.
1. Kattintson a módosítani kívánt jogcímre.
1. Adja meg a konstans értéket idézőjelek nélkül a **Forrás attribútumban** a szervezet szerint, majd kattintson a **Mentés gombra.**

    ![Szervezeti attribútumok & jogcímek szakasza a Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Az állandó érték az alábbi módon jelenik meg.

    ![Szerkessze & jogcímek szakaszát a Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciális jogcímek – átalakítások

A jogcím-transzformációs függvényeket is használhatja.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartomány utótagját az e-mail-címből vagy az egyszerű felhasználónévből. Ez csak a felhasználónév első részét bontja ki (például "joe_smith" joe_smith@contoso.com helyett). |
| **Join()** | Egy attribútumot egy ellenőrzött tartományhoz csatlakozik. Ha a kiválasztott felhasználói azonosító értéke tartományt tartalmaz, kinyeri a felhasználónevet a kiválasztott ellenőrzött tartomány hozzáfűzéseként. Ha például az e-mailt ( ) választja ki a felhasználói azonosító értékeként, és az contoso.onmicrosoft.com tartományt választja ellenőrzött tartományként, az a joe_smith@contoso.com következőt eredményezi: joe_smith@contoso.onmicrosoft.com . |
| **ToLower()** | Kisbetűssé alakítja a kijelölt attribútum karaktereit. |
| **ToUpper()** | Nagybetűssé alakítja a kijelölt attribútum karaktereit. |

## <a name="adding-application-specific-claims"></a>Alkalmazásspecifikus jogcímek hozzáadása

Alkalmazásspecifikus jogcímek hozzáadása:

1. A **Felhasználói attribútumok & jogcímek lapon** válassza az Új jogcím **hozzáadása** lehetőséget a **Felhasználói jogcímek kezelése lap megnyitásához.**
1. Adja meg **a** jogcímek nevét. Az értéknek nem feltétlenül kell követnie az URI-mintát az SAML-specifikáció alapján. Ha URI-mintára van szüksége, azt a Névtér **mezőbe is beteheti.**
1. Válassza ki **azt a forrást,** ahol a jogcím lekéri az értékét. Kiválaszthat egy felhasználói attribútumot a forrásattribútum legördülő menüből, vagy átalakítást alkalmazhat a felhasználói attribútumra, mielőtt jogcímként kibocsátja azt.

### <a name="claim-transformations"></a>Jogcím-átalakítások

Átalakítás alkalmazása felhasználói attribútumra:

1. A **Jogcím kezelése lapon** válassza az *Átalakítás* lehetőséget jogcímforrásként az Átalakítás kezelése **lap megnyitásához.**
2. Válassza ki a függvényt az átalakítás legördülő menüből. A kiválasztott függvénytől függően meg kell adnia a paramétereket és egy állandó értéket az átalakítás során való kiértékeléséhez. Az elérhető függvényekkel kapcsolatos további információkért tekintse meg az alábbi táblázatot.
3. Ha több átalakítást szeretne alkalmazni, kattintson az **Átalakítás hozzáadása elemre.** Egy jogcímen legfeljebb két átalakítást alkalmazhat. Például először kinyerheti a e-mail-előtagját. `user.mail` Ezután nagybetűsre kell tenni a sztringet.

   ![Több jogcím átalakítása](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

A jogcímek átalakításához az alábbi függvényeket használhatja.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartomány utótagját az e-mail-címből vagy az egyszerű felhasználónévből. Ez csak a felhasználónév első részét bontja ki( például "joe_smith" joe_smith@contoso.com helyett). |
| **Join()** | Új értéket hoz létre két attribútum összevonása alapján. A két attribútum között elválasztó is használható. A NameID jogcímátalakításhoz az illesztés ellenőrzött tartományra van korlátozva. Ha a kiválasztott felhasználói azonosító értéke tartományt tartalmaz, kinyeri a felhasználónevet a kiválasztott ellenőrzött tartomány hozzáfűzéseként. Ha például az e-mailt ( ) választja ki a felhasználói azonosító értékeként, és az contoso.onmicrosoft.com tartományt választja ellenőrzött tartományként, az eredmény a joe_smith@contoso.com következő lesz: joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase()** | Kisbetűssé alakítja a kijelölt attribútum karaktereit. |
| **ToUppercase()** | Nagybetűssé alakítja a kijelölt attribútum karaktereit. |
| **Contains()** | Attribútumot vagy állandót ad ki, ha a bemenet megfelel a megadott értéknek. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátatni, amelyben az érték a felhasználó e-mail-címe, ha az tartalmazza a " "" tartományt, ellenkező esetben az egyszerű felhasználónevet @contoso.com szeretné kibocsátni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)*: user.email<br/>*Érték:*" @contoso.com "<br/>2. paraméter (kimenet): user.email<br/>3. paraméter (kimenet, ha nincs egyezés): user.userprincipalname |
| **EndWith()** | Attribútumot vagy állandót ad ki, ha a bemenet a megadott értékre végződik. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátatni, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az alkalmazott azonosítója "000" végződik, ellenkező esetben bővítményattribútumot szeretne kibocsátatni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (input)*: user.employeeid<br/>*Érték:*"000"<br/>2. paraméter (kimenet): user.employeeid<br/>3. paraméter (kimenet, ha nincs egyezés): user.extensionattribute1 |
| **StartWith()** | Attribútumot vagy állandót ad ki, ha a bemenet a megadott értékkel kezdődik. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például egy olyan jogcímet szeretne kibocsátatni, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az ország/régió "US" előtérértékkel kezdődik, ellenkező esetben bővítményattribútumot szeretne kibocsátatni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (input)*: user.country<br/>*Érték:*"US"<br/>2. paraméter (kimenet): user.employeeid<br/>3. paraméter (kimenet, ha nincs egyezés): user.extensionattribute1 |
| **Extract() – Egyeztetés után** | A megadott értéknek megfelelő karakterláncot adja vissza.<br/>Ha például a bemenet értéke "Finance_BSimon", az egyező érték "Finance_", akkor a jogcím kimenete "BSimon". |
| **Extract() – Egyeztetés előtt** | A karakterlánc alsztringet adja vissza, amíg meg nem egyezik a megadott értékkel.<br/>Ha például a bemenet értéke "BSimon_US", az egyező érték "_US", akkor a jogcím kimenete "BSimon". |
| **Extract() – Egyezések között** | A karakterlánc alsztringet adja vissza, amíg meg nem egyezik a megadott értékkel.<br/>Ha például a bemenet értéke "Finance_BSimon_US", az első egyező érték "Finance", a második egyező érték "US", a jogcím kimenete pedig \_ \_ "BSimon". |
| **ExtractAlpha() – Előtag** | A sztring betűrendbe szedett előtagját adja vissza.<br/>Ha például a bemenet értéke "BSimon_123", akkor a "BSimon" értéket adja vissza. |
| **ExtractAlpha() – Utótag** | A sztring betűrendben megadott részét adja vissza.<br/>Ha például a bemenet értéke "123_Simon", akkor a "Simon" értéket adja vissza. |
| **ExtractNumeric() – Előtag** | A sztring numerikus előtagját adja vissza.<br/>Ha például a bemenet értéke "123_BSimon", akkor az "123" értéket adja vissza. |
| **ExtractNumeric() – Utótag** | A sztring utótag numerikus részét adja vissza.<br/>Ha például a bemenet értéke "BSimon_123", akkor az "123" értéket adja vissza. |
| **IfEmpty()** | Egy attribútumot vagy állandót ad ki, ha a bemenet null vagy üres.<br/>Ha például egy extensionattribute attribútumban tárolt attribútumot szeretne kioszteni, ha egy adott felhasználó alkalmazotti azonosítója üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter (bemenet): user.employeeid<br/>2. paraméter (kimenet): user.extensionattribute1<br/>3. paraméter (kimenet, ha nincs egyezés): user.employeeid |
| **IfNotEmpty()** | Attribútumot vagy állandót ad ki, ha a bemenet nem null vagy üres.<br/>Ha például egy extensionattribute attribútumban tárolt attribútumot szeretne kioszteni, ha egy adott felhasználó alkalmazotti azonosítója nem üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter (bemenet): user.employeeid<br/>2. paraméter (kimenet): user.extensionattribute1 |

Ha további átalakításra van szüksége, küldje be ötletét az [Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) visszajelzési fórumán a *SaaS-alkalmazás kategóriában.*

## <a name="emitting-claims-based-on-conditions"></a>Jogcímek kibocsátása feltételek alapján

A jogcím forrását a felhasználó típusa és az a csoport alapján adhatja meg, amelyhez a felhasználó tartozik. 

A felhasználó típusa a következő lehet:
- **Any**: Minden felhasználó hozzáférhet az alkalmazáshoz.
- **Tagok:** A bérlő natív tagja
- **Minden vendég:** A felhasználó egy külső szervezetből lesz áthozva Az Azure AD-val vagy anélkül.
- **AAD-vendégek:** A vendégfelhasználó egy másik, az Azure AD-t használó szervezethez tartozik.
- **Külső vendégek:** A vendégfelhasználó olyan külső szervezethez tartozik, amely nem tagja az Azure AD-nek.


Az egyik forgatókönyv, ahol ez hasznos lehet, ha egy jogcím forrása eltér egy vendég és egy alkalmazáshoz hozzáférő alkalmazott esetében. Megadhatja, hogy ha a felhasználó egy alkalmazott, a NameID az user.email-ból származik, de ha a felhasználó vendég, akkor a NameID forrása a user.extensionattribute1.

Jogcím-feltétel hozzáadása:

1. A **Jogcím kezelése között** bontsa ki a Jogcímfeltételeket.
2. Válassza ki a felhasználó típusát.
3. Válassza ki azokat a csoportokat, amelyekhez a felhasználónak tartozni kell. Egy adott alkalmazás összes jogcímében legfeljebb 50 egyedi csoportot választhat ki. 
4. Válassza ki **azt a forrást,** ahol a jogcím lekéri az értékét. Kiválaszthat egy felhasználói attribútumot a forrásattribútum legördülő menüből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímként kibocsátja azt.

Fontos, hogy milyen sorrendben adja hozzá a feltételeket. Az Azure AD felülről lefelé kiértékeli a feltételeket, és eldönti, hogy melyik értéket bocsátja ki a jogcímben. A kifejezésnek megfelelő utolsó érték lesz kiadva a jogcímben.

Britta Simon például vendégfelhasználó a Contoso bérlőben. Egy másik szervezethez tartozik, amely szintén az Azure AD-t használja. A Fabrikam alkalmazás alábbi konfigurációja alapján, amikor Britta megpróbál bejelentkezni a Fabrikambe, a Microsoft identitásplatformja a következőképpen értékeli ki a feltételeket.

Először is a Microsoft identitásplatformja ellenőrzi, hogy Britta felhasználótípusa a következő-e: `All guests` . Mivel ez így van, a Microsoft identitásplatformja hozzárendeli a jogcím forrását a következőnek: `user.extensionattribute1` . Másodszor, a Microsoft identitásplatformja ellenőrzi, hogy Britta felhasználótípusa , mivel ez is igaz, akkor a Microsoft identitásplatformja hozzárendeli a jogcím forrását a `AAD guests` következőnek: `user.mail` . Végül a jogcím a Britta értékével `user.mail` lesz kiadva.

![Jogcímek feltételes konfigurálása](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Következő lépések

* [Alkalmazáskezelés az Azure AD-ban](../manage-apps/what-is-application-management.md)
* [Az Azure AD alkalmazásgyűjteményében nem található egyszeri bejelentkezéses alkalmazások konfigurálása](../manage-apps/configure-saml-single-sign-on.md)
* [Az SAML-alapú egyszeri bejelentkezés hibaelhárítása](../manage-apps/debug-saml-sso-issues.md)
