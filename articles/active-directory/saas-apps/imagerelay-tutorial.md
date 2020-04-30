---
title: 'Oktatóanyag: Azure Active Directory integráció a rendszerkép-továbbítóval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a képek továbbítása között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd0637a632b277eae019ac4aebfbc7cdb87e8e2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73158974"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Oktatóanyag: Azure Active Directory integráció a rendszerkép-továbbítóval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a rendszerkép-továbbítót Azure Active Directory (Azure AD) használatával.
A képtovábbítás az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a képek továbbításához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a képtovábbítóba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a képtovábbítóval való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Képtovábbító egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A rendszerkép-továbbító támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-image-relay-from-the-gallery"></a>Rendszerkép-továbbító hozzáadása a katalógusból

A képtovábbítás Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a képtovábbítást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A képtovábbítás a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **képtovábbító**kifejezést, válassza a **képtovábbítás** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Képtovábbító az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a rendszerkép-továbbító kapcsolódó felhasználója közötti kapcsolati kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a rendszerkép-továbbítóval való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[képtovábbító egyszeri bejelentkezésének konfigurálása](#configure-image-relay-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Rendszerkép-továbbítási teszt felhasználó létrehozása](#create-image-relay-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó képtovábbítási partnere.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a képtovábbítóval való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **rendszerkép-továbbító** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A rendszerkép-továbbító tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.imagerelay.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez forduljon a [rendszerkép-továbbító ügyfél-támogatási csapatához](http://support.imagerelay.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **rendszerkép-továbbítás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-image-relay-single-sign-on"></a>A rendszerkép-továbbító egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a rendszerkép-továbbító vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **felhasználók & engedélyek** munkaterhelésre.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Kattintson az **új engedély létrehozása**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Az **egyszeri bejelentkezés beállításai** munkaterhelésen jelölje be az **Ez a csoport csak egyszeri bejelentkezéssel** bejelentkezhet jelölőnégyzetet, majd kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Lépjen a **Fiókbeállítások**menüpontra.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Ugrás az **egyszeri bejelentkezés beállításainak** munkaterhelésére.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Az **SAML-beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    b. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    c. A **név azonosítójának formátuma**beállításnál válassza az **urn: Oasis: nevek: TC: SAML: 1.1: nameid-Format: emailAddress**elemet.

    d. **A szolgáltatótól érkező kérések kötési lehetőségeiként válassza a** **kötés küldése**lehetőséget.

    e. Az **x. 509 tanúsítvány**alatt kattintson a **tanúsítvány frissítése**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be az **x. 509 tanúsítvány** szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Az igény szerinti **felhasználó üzembe** helyezése szakaszban válassza az igény szerinti **felhasználó üzembe**helyezésének engedélyezése lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Válassza ki az engedélyezési csoportot (például az **egyszeri bejelentkezés alapszintű**), amely csak egyszeri bejelentkezéssel jelentkezhet be.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a képtovábbításhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **képtovábbítás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **képtovábbító**elemet.

    ![A rendszerkép-továbbítási hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-image-relay-test-user"></a>Rendszerkép-továbbító tesztelési felhasználó létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű felhasználót a képek továbbításában.

**Ha a Britta Simon nevű felhasználót szeretné létrehozni a képtovábbítóban, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a rendszerkép-továbbító céges webhelyre rendszergazdaként.

2. Lépjen a **felhasználók & engedélyek** elemre, és válassza az **SSO-felhasználó létrehozása**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Adja meg a kiépíteni kívánt felhasználó **e-mail-címét** **, utónevét,** **vezetéknevét**és **vállalatát** , és válassza ki az engedély csoportot (például: SSO Basic), amely az a csoport, amely csak egyszeri bejelentkezéssel tud bejelentkezni.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Kattintson a **Létrehozás**gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a rendszerkép-továbbítási csempére kattint, automatikusan be kell jelentkeznie arra a rendszerkép-továbbítóra, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)