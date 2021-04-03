---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler Private Access Administrator szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler privát hozzáférési rendszergazdája között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890989"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler privát hozzáférési rendszergazdájával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler privát hozzáférés-rendszergazdáját Azure Active Directory (Azure AD) használatával.
A Zscaler privát hozzáférés-rendszergazdájának az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Zscaler hozzáférés-rendszergazdához.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával Zscaler a privát hozzáférési rendszergazdának (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Zscaler-hozzáférés a rendszergazdai egyszeri bejelentkezéshez – engedélyezett előfizetés

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler-hozzáférés rendszergazdája támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler-hozzáférés-adminisztrátor hozzáadása a katalógusból

A Zscaler privát hozzáférési rendszergazdája Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler privát hozzáférési rendszergazdát a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépéseket követve adhat hozzá Zscaler-hozzáférési rendszergazdát a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler privát hozzáférési rendszergazdája** kifejezést, majd az alkalmazás hozzáadásához kattintson a **Zscaler privát hozzáférési rendszergazdája** elemre, majd a **Hozzáadás** gombra.

    ![Zscaler privát hozzáférési rendszergazdája az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Zscaler privát hozzáférési rendszergazdájával egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Zscaler-hozzáférés-kezelő rendszergazdája közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Zscaler privát hozzáférési rendszergazdájával a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Zscaler privát hozzáférés-rendszergazdai egyszeri bejelentkezésének konfigurálása](#configure-zscaler-private-access-administrator-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Zscaler Private Access Administrator test User](#create-zscaler-private-access-administrator-test-user)** -to have a Britta Simon partnere a Zscaler Private Access rendszergazdája, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Zscaler privát hozzáférési rendszergazdájával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Zscaler Private Access Administrator** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőképen az alapszintű SAML-konfiguráció látható, ahol megadhatja a leírt értékeket.](common/idp-relay.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kattintson a **további URL-címek beállítása** elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `idpadminsso`

5.  Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/both-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a [Zscaler privát hozzáférés-felügyeleti ügyfelének ügyfélszolgálatához](https://help.zscaler.com/zpa-submit-ticket) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Zscaler magánhálózati hozzáférésének beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>A Zscaler magánhálózati hozzáférés-felügyeleti szolgáltatásának konfigurálása egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Zscaler privát hozzáférési rendszergazdájához.

2. A felső részen kattintson az **Adminisztráció** elemre, és navigáljon a **hitelesítés** szakaszra, majd kattintson a **identitásszolgáltató konfigurálása** elemre

    ![Zscaler-rendszergazdai rendszergazda](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. A jobb felső sarokban kattintson a **identitásszolgáltató-konfiguráció hozzáadása** lehetőségre. 

    ![Zscaler privát hozzáférés-felügyeleti addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. A **identitásszolgáltató-konfiguráció hozzáadása** oldalon hajtsa végre a következő lépéseket:
 
    ![Zscaler privát hozzáférés-felügyeleti idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kattintson a **fájl kiválasztása** lehetőségre a letöltött metaadat-fájl feltöltéséhez az Azure ad-ből a **identitásszolgáltató metaadat-fájl feltöltése** mezőben.

    b. Beolvassa a **identitásszolgáltató metaadatait** az Azure ad-ből, és az alább látható módon feltölti az összes mezőt.

    ![Zscaler privát hozzáférés-felügyeleti idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Válassza az **egyszeri bejelentkezés** **rendszergazdaként** lehetőséget.

    d. Válassza ki a tartományt a **tartományok** mezőben.
    
    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Zscaler privát hozzáférési rendszergazdájához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Zscaler privát hozzáférési rendszergazda** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler privát hozzáférési rendszergazda** elemet.

    ![A Zscaler Private Access Administrator hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Zscaler-hozzáférés rendszergazdai tesztelési felhasználójának létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Zscaler privát hozzáférési Rendszergazdájába, a Zscaler privát hozzáférési rendszergazdájának kell kiépíteni őket. A Zscaler privát hozzáférési rendszergazdája esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Zscaler privát hozzáférés-adminisztrátor vállalati webhelyére rendszergazdaként.

2. A felső részen kattintson az **Adminisztráció** elemre, és navigáljon a **hitelesítés** szakaszra, majd kattintson a **identitásszolgáltató konfigurálása** elemre

    ![Zscaler-rendszergazdai rendszergazda](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. A menü bal oldalán kattintson a **rendszergazdák** elemre.

    ![Zscaler-hozzáférés rendszergazdai rendszergazdája](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. A jobb felső sarokban kattintson a **rendszergazda hozzáadása** lehetőségre:

    ![Zscaler – rendszergazdai jogosultságok hozzáadása](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. A **rendszergazda hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Zscaler privát hozzáférés-adminisztrátor felhasználói rendszergazdája](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet BrittaSimon@contoso.com .

    b. A **jelszó** szövegmezőbe írja be a jelszót.

    c. A **Jelszó megerősítése** szövegmezőbe írja be a jelszót.

    d. Válassza ki a **szerepkört** **Zscaler privát hozzáférési rendszergazdaként**.

    e. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet BrittaSimon@contoso.com .

    f. A **telefon** szövegmezőbe írja be a telefonszámot.

    : Az **időzóna** szövegmezőben válassza ki az időzónát.

    h. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Zscaler Private Access Administrator csempére kattint, automatikusan be kell jelentkeznie a Zscaler privát hozzáférési Rendszergazdájába, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)