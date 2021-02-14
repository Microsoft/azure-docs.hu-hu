---
title: Alkalmazás SAML-token jogcímek testreszabása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szabhatja testre a Microsoft Identity platform által kibocsátott jogcímeket a vállalati alkalmazások SAML-jogkivonatában.
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
ms.openlocfilehash: 05447f41ca891adfe14533a74dfedf153e3c5773
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102699"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz

Napjainkban a Microsoft Identity platform támogatja az egyszeri bejelentkezést (SSO) a legtöbb nagyvállalati alkalmazással, beleértve az Azure AD-katalógusban és az egyéni alkalmazásokban előre integrált alkalmazásokat is. Amikor a felhasználó az SAML 2,0 protokoll használatával hitelesít egy alkalmazást a Microsoft Identity platformon keresztül, a Microsoft Identity platform tokent küld az alkalmazásnak (HTTP-POSTon keresztül). Ezután az alkalmazás érvényesíti és használja a jogkivonatot a felhasználó bejelentkezésére a Felhasználónév és a jelszó kérése helyett. Ezek az SAML-tokenek a *jogcímek* által ismert felhasználóra vonatkozó adatokat tartalmaznak.

A *jogcím* olyan információ, amelyet az identitás-szolgáltató az adott felhasználóhoz tartozó jogkivonatban lévő felhasználóval kapcsolatos. Az [SAML-tokenben](https://en.wikipedia.org/wiki/SAML_2.0)ezeket az adatok JELLEMZŐEN az SAML-attribútum utasításában találhatók. A felhasználó egyedi AZONOSÍTÓját általában az SAML-tulajdonos jelöli, más néven a name Identifier.

Alapértelmezés szerint a Microsoft Identity platform egy SAML-jogkivonatot bocsát ki az alkalmazásnak, amely a `NameIdentifier` felhasználó felhasználónevét (más néven az egyszerű felhasználónevet) tartalmazza az Azure ad-ben, amely egyedileg azonosíthatja a felhasználót. Az SAML-jogkivonat a felhasználó e-mail-címét, utónevét és vezetéknevét tartalmazó további jogcímeket is tartalmaz.

Az SAML-jogkivonatban kiadott jogcímek megtekintéséhez vagy módosításához nyissa meg az alkalmazást Azure Portalban. Ezután nyissa meg a **felhasználói attribútumok & jogcímek** szakaszt.

![Nyissa meg a felhasználói attribútumok & jogcímek szakaszt a Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Az SAML-jogkivonatban kiadott jogcímek szerkesztésének két lehetséges oka lehet:

* Az alkalmazáshoz a `NameIdentifier` vagy a NameID jogcímnek olyannak kell lennie, amely nem az Azure ad-ban tárolt Felhasználónév (vagy egyszerű felhasználónév).
* Az alkalmazás úgy lett írva, hogy egy másik jogcím-URI-t vagy jogcím-értéket igényel.

## <a name="editing-nameid"></a>NameID szerkesztése

A NameID (név azonosító érték) szerkesztése:

1. Nyissa meg a **név-azonosító érték** lapot.
1. Válassza ki azt az attribútumot vagy átalakítást, amelyre alkalmazni kívánja az attribútumot. Megadhatja azt a formátumot is, amelyre a NameID jogcím vonatkozik.

   ![A NameID (Name Identifier) értékének szerkesztése](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID formátuma

Ha az SAML-kérelem megadott formátumú NameIDPolicy tartalmaz, a Microsoft Identity platform tiszteletben tartja a kérelem formátumát.

Ha az SAML-kérelem nem tartalmaz NameIDPolicy-elemet, akkor a Microsoft Identity platform a megadott formátumban fogja kiadni a NameID. Ha nincs megadva a formátum, a Microsoft Identity platform a kiválasztott jogcím forrásához társított alapértelmezett formátumot fogja használni.

A **név-azonosító formátum** legördülő listából választhatja ki az alábbi lehetőségek egyikét.

| NameID formátuma | Description |
|---------------|-------------|
| **Alapértelmezett** | A Microsoft Identity platform az alapértelmezett forrás formátumot fogja használni. |
| **Állandó** | A Microsoft Identity platform a NameID formátumot fogja használni állandóként. |
| **EmailAddress** | A Microsoft Identity platform NameID formátumban fogja használni az EmailAddress formátumot. |
| **Meghatározatlan** | A Microsoft Identity platform NameID formátuma nem lesz meghatározva. |

Az átmeneti NameID is támogatott, de nem érhető el a legördülő menüben, és nem konfigurálható az Azure oldalán. További információ a NameIDPolicy attribútumról: [Single Sign-On SAML protokoll](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribútumok

Válassza ki a kívánt forrást a `NameIdentifier` (vagy NameID) jogcím számára. A következő lehetőségek közül választhat.

| Név | Leírás |
|------|-------------|
| E-mail | A felhasználó e-mail címe |
| userprincipalName | A felhasználó egyszerű felhasználóneve (UPN) |
| onpremisessamaccount | A helyszíni Azure AD-ből szinkronizált SAM-fiók neve |
| ObjectId | A felhasználó ObjectId az Azure AD-ben |
| Alkalmazottkód | A felhasználó alkalmazott azonosítója |
| Címtárbővítmények | A helyszíni [Active Directory szinkronizált címtárszolgáltatás-bővítmények Azure ad Connect Sync használatával](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Bővítmény attribútumai 1-15 | Az Azure AD-séma kibővítéséhez használt helyszíni bővítmény attribútumai |

További információ [: 3. táblázat: érvényes azonosító értékek forrásként](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Bármilyen állandó (statikus) értéket hozzárendelhet az Azure AD-ben definiált jogcímekhez. Állandó érték hozzárendeléséhez kövesse az alábbi lépéseket:

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>a **felhasználói attribútumok & jogcímek** szakaszban kattintson a **Szerkesztés** ikonra a jogcímek szerkesztéséhez.
1. Kattintson arra a szükséges jogcímere, amelyet módosítani szeretne.
1. Adja meg az állandó értéket idézőjelek nélkül a **forrás attribútumban** a szervezetnél, majd kattintson a **Save (Mentés**) gombra.

    ![Szervezeti attribútumok & jogcímek szakasz a Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Az állandó érték az alábbiak szerint fog megjelenni.

    ![Attribútumok szerkesztése & jogcímek szakasz a Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciális jogcímek – átalakítások

Használhatja a jogcím-átalakítási funkciókat is.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományi utótagot az e-mail-címről vagy az egyszerű felhasználónévből. Ez csak a Felhasználónév első részét (például "joe_smith") adja ki a (z joe_smith@contoso.com ) helyett. |
| **Csatlakozás ()** | Egy attribútumot ellenőrzött tartománnyal társít. Ha a kiválasztott felhasználóazonosító-érték tartományhoz tartozik, a rendszer kibontja a felhasználónevet a kiválasztott ellenőrzött tartomány hozzáfűzéséhez. Ha például az e-mail-címet ( joe_smith@contoso.com ) adja meg a felhasználói azonosító értékként, és a contoso.onmicrosoft.com-t ellenőrzött tartományként választja, akkor ez a következőt eredményezi: joe_smith@contoso.onmicrosoft.com . |
| **ToLower ()** | A kijelölt attribútum karaktereit kisbetűs karakterekké alakítja. |
| **ToUpper()** | A kijelölt attribútum karaktereit nagybetűvé alakítja. |

## <a name="adding-application-specific-claims"></a>Alkalmazás-specifikus jogcímek hozzáadása

Alkalmazás-specifikus jogcímek hozzáadása:

1. A **felhasználói attribútumok & jogcímek** területen válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** lap megnyitásához.
1. Adja meg a jogcímek **nevét** . Az értéknek nem feltétlenül kell követnie egy URI-mintát az SAML-specifikáció alapján. Ha URI-mintázatra van szüksége, azt a **névtér** mezőbe helyezheti.
1. Válassza ki azt a **forrást** , ahol a jogcím le fogja kérdezni az értékét. Kiválaszthat egy felhasználói attribútumot a forrás attribútum legördülő menüből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímet kibocsátja jogcímként.

### <a name="claim-transformations"></a>Jogcím-átalakítások

Átalakítás alkalmazása felhasználói attribútumra:

1. A **jogcím kezelése** lapon válassza az *átalakítás* lehetőséget jogcím forrásaként az **átalakítás kezelése** lap megnyitásához.
2. Válassza ki a függvényt az átalakítás legördülő listából. A kiválasztott függvénytől függően paramétereket és állandó értéket kell megadnia, hogy kiértékelje az átalakítást. Az elérhető funkciókkal kapcsolatos további információkért tekintse meg az alábbi táblázatot.
3. Több átalakítás alkalmazásához kattintson az **átalakítás hozzáadása** lehetőségre. A jogcímek számára legfeljebb két átalakítás alkalmazható. Először kinyerheti például az e-mail-előtagot `user.mail` . Ezután végezze el a karakterláncot a nagybetűvel.

   ![Több jogcím-átalakítás](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

A jogcímek átalakításához a következő függvények használhatók.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományi utótagot az e-mail-címről vagy az egyszerű felhasználónévből. Ez csak a Felhasználónév első részét (például "joe_smith") adja ki a (z joe_smith@contoso.com ) helyett. |
| **Csatlakozás ()** | Létrehoz egy új értéket két attribútum összekapcsolásával. Igény szerint elválasztót is használhat a két attribútum között. A NameID-jogcím átalakításához az illesztés egy ellenőrzött tartományra korlátozódik. Ha a kiválasztott felhasználóazonosító-érték tartományhoz tartozik, a rendszer kibontja a felhasználónevet a kiválasztott ellenőrzött tartomány hozzáfűzéséhez. Ha például az e-mail-címet ( joe_smith@contoso.com ) adja meg a felhasználói azonosító értékként, és a contoso.onmicrosoft.com-t ellenőrzött tartományként választja, akkor ez a következőt eredményezi: joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase()** | A kijelölt attribútum karaktereit kisbetűs karakterekké alakítja. |
| **ToUppercase()** | A kijelölt attribútum karaktereit nagybetűvé alakítja. |
| **Tartalmazza ()** | Attribútumot vagy állandót ad eredményül, ha a bemenet megfelel a megadott értéknek. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó e-mail-címe, ha a (z @contoso.com ) "" tartományt tartalmazza, ellenkező esetben az egyszerű felhasználónevet is ki szeretné állítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)*: user. e-mail<br/>*Érték*: " @contoso.com "<br/>2. paraméter (kimenet): user. e-mail<br/>3. paraméter (kimenet, ha nincs egyezés): user. userPrincipalName |
| **EndWith()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet a megadott értékkel végződik. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az alkalmazott azonosítója "000" végződéssel végződik, ellenkező esetben egy Extension attribútumot szeretne kiállítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)*: user. Alkalmazottkód<br/>*Érték*: "000"<br/>2. paraméter (kimenet): user. Alkalmazottkód<br/>3. paraméter (kimenet, ha nincs egyezés): user. extensionAttribute1 |
| **StartWith()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet a megadott értékkel kezdődik. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az ország/régió az "USA"-val kezdődik, ellenkező esetben egy Extension attribútumot szeretne kiállítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)*: felhasználó. ország<br/>*Érték*: "US"<br/>2. paraméter (kimenet): user. Alkalmazottkód<br/>3. paraméter (kimenet, ha nincs egyezés): user. extensionAttribute1 |
| **Kinyerés () – a megfeleltetés után** | Azt az alsztringet adja vissza, amely a megadott értéknek felel meg.<br/>Ha például a bemeneti érték "Finance_BSimon", a megfelelő érték "Finance_", akkor a jogcím kimenete "BSimon". |
| **Kinyerés () – a megfeleltetés előtt** | Az alsztringet adja vissza, amíg meg nem felel a megadott értéknek.<br/>Ha például a bemeneti érték "BSimon_US", a megfelelő érték "_US", akkor a jogcím kimenete "BSimon". |
| **Kinyerés () – megfeleltetések között** | Az alsztringet adja vissza, amíg meg nem felel a megadott értéknek.<br/>Ha például a bemeneti érték "Finance_BSimon_US", az első egyező érték a "Finance \_ ", a második egyező érték pedig " \_ us", akkor a jogcím kimenete "BSimon". |
| **ExtractAlpha () – előtag** | A karakterlánc ábécé részét adja vissza.<br/>Ha például a bemeneti érték "BSimon_123", akkor a "BSimon" értéket adja vissza. |
| **ExtractAlpha () – utótag** | A karakterlánc ábécé részét képező utótagot adja vissza.<br/>Ha például a bemeneti érték "123_Simon", akkor a "Simon" értéket adja vissza. |
| **ExtractNumeric () – előtag** | A sztring numerikus részét adja vissza.<br/>Ha például a bemeneti érték "123_BSimon", akkor a "123" értéket adja vissza. |
| **ExtractNumeric () – utótag** | A karakterlánc numerikus részét adja vissza.<br/>Ha például a bemeneti érték "BSimon_123", akkor a "123" értéket adja vissza. |
| **IfEmpty()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet null értékű vagy üres.<br/>Ha például egy extensionattribute tárolt attribútumot szeretne kiállítani, ha egy adott felhasználó alkalmazott azonosítója üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter (bemenet): user. Alkalmazottkód<br/>2. paraméter (kimenet): user. extensionAttribute1<br/>3. paraméter (kimenet, ha nincs egyezés): user. Alkalmazottkód |
| **IfNotEmpty()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet nem null vagy üres.<br/>Ha például egy extensionattribute tárolt attribútumot szeretne kiállítani, ha egy adott felhasználó alkalmazott azonosítója nem üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter (bemenet): user. Alkalmazottkód<br/>2. paraméter (kimenet): user. extensionAttribute1 |

Ha további átalakításokra van szüksége, küldje el ötletét az [Azure ad visszajelzési fórumában](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) az *SaaS-alkalmazás* kategóriában.

## <a name="emitting-claims-based-on-conditions"></a>Jogcímek kibocsátása feltételek alapján

Megadhatja a jogcímek forrását felhasználói típus alapján, valamint azt a csoportot, amelyhez a felhasználó tartozik. 

A felhasználó típusa a következő lehet:
- **Any**: minden felhasználó hozzáférhet az alkalmazáshoz.
- **Tagok**: a bérlő natív tagja
- **Minden vendég**: a felhasználót az Azure ad-vel vagy anélkül egy külső szervezettől kezdeményezik.
- **HRE vendégek**: a vendég felhasználó egy másik szervezethez tartozik az Azure ad használatával.
- **Külső vendégek**: a vendég felhasználó olyan külső szervezethez tartozik, amely nem rendelkezik Azure ad-vel.


Az egyik forgatókönyv, ahol ez hasznos lehet, ha a jogcím forrása eltér a vendég számára, és az alkalmazáshoz hozzáférő alkalmazott. Érdemes lehet megadnia, hogy ha a felhasználó egy alkalmazott, a NameID a User. e-mail címről származik, de ha a felhasználó vendég, akkor a NameID a User. extensionAttribute1 forrásból származik.

Jogcím feltételének hozzáadása:

1. A **jogcím kezelése** területen bontsa ki a jogcím feltételeit.
2. Válassza ki a felhasználó típusát.
3. Válassza ki azokat a csoportokat, amelyekhez a felhasználónak tartoznia kell. Egy adott alkalmazáshoz tartozó jogcímek közül legfeljebb 50 egyedi csoportot választhat ki. 
4. Válassza ki azt a **forrást** , ahol a jogcím le fogja kérdezni az értékét. Kiválaszthat egy felhasználói attribútumot a forrás attribútum legördülő menüből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímet kibocsátja jogcímként.

Fontos, hogy milyen sorrendben adja hozzá a feltételeket. Az Azure AD kiértékeli a feltételeket felülről lefelé, hogy eldöntse, melyik értéket kell kibocsátania a jogcímben. A kifejezésben szereplő utolsó érték a jogcímben lesz kibocsátva.

A Britta Simon például egy vendég felhasználó a contoso-bérlőben. Egy másik szervezethez tartozik, amely az Azure AD-t is használja. A fabrikam alkalmazás alábbi konfigurációjának megfelelően, amikor a Britta megpróbál bejelentkezni a fabrikam szolgáltatásba, a Microsoft Identity platform az alábbi feltételek szerint értékeli ki a feltételeket.

Először is a Microsoft Identity platform ellenőrzi, hogy a Britta felhasználói típusa a-e `All guests` . Mivel ez igaz, a Microsoft Identity platform hozzárendeli a jogcímek forrását a következőhöz: `user.extensionattribute1` . Másodszor, a Microsoft Identity platform ellenőrzi, hogy a Britta felhasználói típusa van `AAD guests` -e, mivel ez is igaz, a Microsoft Identity platform a jogcím forrását rendeli hozzá `user.mail` . Végezetül a jogcím a Britta értékével van kibocsátva `user.mail` .

![Feltételes konfiguráció igénylése](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Következő lépések

* [Alkalmazások kezelése az Azure AD-ben](../manage-apps/what-is-application-management.md)
* [Egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek az Azure AD-alkalmazás-katalógusban](../manage-apps/configure-saml-single-sign-on.md)
* [Az SAML-alapú egyszeri bejelentkezés hibaelhárítása](../manage-apps/debug-saml-sso-issues.md)
