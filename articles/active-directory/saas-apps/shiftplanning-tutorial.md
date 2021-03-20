---
title: 'Oktatóanyag: Azure Active Directory integráció az emberiséggel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az emberiség között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: e1099f271291c6d86610237454e08e06b5fe48c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522155"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Oktatóanyag: Azure Active Directory integráció az emberiséggel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az emberiséget Azure Active Directory (Azure AD) használatával.
Az emberiség az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Megadhatja az Azure AD-t, aki hozzáfér az emberhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Emberiségbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az emberiséggel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Emberi egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az emberiség támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-humanity-from-the-gallery"></a>Az emberiség felvétele a katalógusból

Az emberiség Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az emberiséget a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépések végrehajtásával adhatja hozzá az emberiséget a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **emberiség** kifejezést, válassza az **emberiség** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Emberiség az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az emberiséggel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az emberiség kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az emberiséggel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[emberi egyszeri bejelentkezés konfigurálása](#configure-humanity-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre](#create-humanity-test-user)** egy Britta, amely a felhasználó Azure ad-beli képviseletéhez kapcsolódik, hogy az emberiség tagja legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az emberiséggel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **emberi** alkalmazások integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Az emberiség tartománya és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.humanity.com/includes/saml/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://company.humanity.com/app/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. A következő értékek beszerzéséhez vegye fel a kapcsolatot az [emberiség ügyfélszolgálatával](https://www.humanity.com/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **emberiség beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-humanity-single-sign-on"></a>Az emberiség egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az **emberiség** céges webhelyére rendszergazdaként.

2. A felső menüben kattintson a **rendszergazda** elemre.

    ![Felügyelet](./media/shiftplanning-tutorial/iC786619.png "Rendszergazda")
3. Az **integráció** alatt kattintson az **egyszeri bejelentkezés** elemre.

    ![Képernyőfelvétel: az integrációs menüből kiválasztható egyetlen Sign-On.](./media/shiftplanning-tutorial/iC786620.png "Egyszeri bejelentkezés")

4. Az **egyszeri bejelentkezés** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyetlen Sign-On szakasz, ahol megadhatja a leírt értékeket.](./media/shiftplanning-tutorial/iC786905.png "Egyszeri bejelentkezés")

    a. Válassza az **SAML engedélyezve** lehetőséget.

    b. Jelölje be a **jelszó-bejelentkezés engedélyezése** jelölőnégyzetet.

    c. Az **SAML kiállító URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL** -címet, amelyet a Azure Portalból másolt.

    d. A **távoli kijelentkezés URL-címe** szövegmezőbe illessze be a **KIjelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    e. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    f. Kattintson a **Beállítások mentése** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az emberiség számára való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **emberiség** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **emberiség** lehetőséget.

    ![Az emberiség hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-humanity-test-user"></a>Az emberiség tesztelésére szolgáló felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az Emberiségbe, az emberiségnek kell kiépíteni őket. Az emberiség esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **emberiség** vállalatának webhelyére rendszergazdaként.

2. Kattintson a **rendszergazda** elemre.

    ![Felügyelet](./media/shiftplanning-tutorial/iC786619.png "Rendszergazda")

3. Kattintson a **személyzet** elemre.

    ![Személyzet](./media/shiftplanning-tutorial/ic786623.png "Személyzet")

4. A **műveletek** területen kattintson az **alkalmazottak hozzáadása** lehetőségre.

    ![Alkalmazottak hozzáadása](./media/shiftplanning-tutorial/iC786624.png "Alkalmazottak hozzáadása")

5. Az **alkalmazottak hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Alkalmazottak mentése](./media/shiftplanning-tutorial/iC786625.png "Alkalmazottak mentése")

    a. Írja be egy érvényes Azure AD-fiók **utónevét**, **vezetéknevét** és **e-mail-címét** , amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

    b. Kattintson az **alkalmazottak mentése** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, az emberiség által biztosított felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen az emberiség csempére kattint, automatikusan be kell jelentkeznie az emberiség számára, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)