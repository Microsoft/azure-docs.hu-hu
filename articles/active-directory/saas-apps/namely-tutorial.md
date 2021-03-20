---
title: 'Oktatóanyag: Azure Active Directory integráció a következőkkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és így.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: bafec2c1b857d3220089e2ca41190987de33edb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519491"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Oktatóanyag: Azure Active Directory integráció a következőkkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a következőt: Azure Active Directory (Azure AD).
Az Azure AD-vel való integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával (azaz egyszeri bejelentkezéssel).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Azaz egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Azaz támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-namely-from-the-gallery"></a>A gyűjtemény hozzáadása a katalógusból

A konkrétan az Azure AD-be való integráció konfigurálásához hozzá kell adnia a katalógust a felügyelt SaaS-alkalmazások listájához.

**A katalógusból való hozzáadáshoz hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **név** **, válassza ki az eredmény** panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli, a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az egyszeri **[Bejelentkezés konfigurálása](#configure-namely-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy felhasználói tesztet](#create-namely-test-user)** , hogy a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés konfigurálásához a következő lépéseket kell végrehajtania:

1. A [Azure Portal](https://portal.azure.com/) **az alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A tartomány-és URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.namely.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot az [ügyfél-támogatási csapattal](https://www.namely.com/contact/) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **set up** (jelentés készítése) szakaszban másolja ki a megfelelő URL-címeket (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-namely-single-sign-on"></a>Egyedi Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **vállalat** elemre.
   
    ![Képernyőfelvétel: a kiválasztott vállalat értéke.](./media/namely-tutorial/tutorial_namely_06.png) 

3. Kattintson a **Beállítások** fülre.
   
    ![Képernyőfelvétel: a vállalati beállítások lap van kiválasztva.](./media/namely-tutorial/tutorial_namely_07.png) 

4. Kattintson az **SAML** elemre.
   
    ![Képernyőfelvétel: SAML kiválasztva.](./media/namely-tutorial/tutorial_namely_08.png) 

5. Az **SAML-beállítások** lapon hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: SAML-beállítások, ahol megadhatja a leírt értékeket.](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kattintson az **SAML engedélyezése** lehetőségre. 

    b. Az **Identity Provider SSO URL** szövegmezőben illessze be a **bejelentkezési URL**-címet, amelyet a Azure Portalból másolt.
    
    c. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be az **Identity Provider tanúsítvány** szövegmezőbe.
     
    d. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a következőhöz:.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **nevezetesen** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **nevezetesen** lehetőséget.

    ![Az alkalmazások listájában szereplő hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-namely-test-user"></a>Hozzon létre, azaz tesztelje a felhasználót

A szakasz célja, hogy létrehozzon egy Britta Simon nevű felhasználót, azaz.

**A következő lépések végrehajtásával hozhat létre egy Britta Simon nevű felhasználót:**

1. Jelentkezzen be a vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **személyek** elemre.
   
    ![A képernyőfelvételen a kiválasztott személyek érték látható.](./media/namely-tutorial/tutorial_namely_10.png) 

3. Kattintson a **könyvtár** fülre.
   
    ![Képernyőfelvétel: a People (személyek) lap van kiválasztva.](./media/namely-tutorial/tutorial_namely_11.png) 

4. Kattintson az **új személy hozzáadása** lehetőségre.

    ![A képernyőképen az új személy hozzáadása lehetőség látható.](./media/namely-tutorial/tutorial_namely_12.png)

5. Az **új személy hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. A **Utónév** szövegmezőbe írja be a következőt: **Britta**.

    b. A vezetéknév szövegmezőbe írja be a **Simon** **nevet** .

    c. Az **e-mail** szövegmezőbe írja be a BrittaSimon **e-mail-címét** .

    d. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a konkrét csempére kattint, akkor automatikusan be kell jelentkeznie arra a Konkrétra, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)