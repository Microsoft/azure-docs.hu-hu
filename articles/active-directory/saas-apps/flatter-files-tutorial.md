---
title: 'Oktatóanyag: Azure Active Directory a laposabb fájlokkal való integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és laposabb fájlok között.
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
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453220"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Oktatóanyag: Azure Active Directory-integráció a laposabb fájlokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a laposabb fájlokat Azure Active Directory (Azure AD) használatával.
A laposabb fájlok az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a laposabb fájlokhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a laposabb fájlokba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció laposabb fájlokkal történő konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Laposabb fájlok egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A laposabb fájlok támogatják a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-flatter-files-from-the-gallery"></a>Laposabb fájlok hozzáadása a katalógusból

A laposabb fájlok Azure AD-be való integrálásának konfigurálásához a katalógusból laposabb fájlokat kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

**Ha laposabb fájlokat szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **laposabb fájlok** kifejezést, válassza a **laposabb fájlok** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Laposabb fájlok az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a **Britta Simon** nevű teszt felhasználója alapján, laposabb fájlokkal konfigurálja és teszteli.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség a laposabb fájlokban.

Az Azure AD egyszeri bejelentkezés laposabb fájlokkal történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[laposabb fájlok egyszeri bejelentkezésének konfigurálása](#configure-flatter-files-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre laposabb fájlokat tesztelési felhasználóval](#create-flatter-files-test-user)** , hogy a Britta Simon a felhasználó Azure ad-képviseletéhez kapcsolódó laposabb fájlokban legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés laposabb fájlokkal történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **laposabb fájlok** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Laposabb fájlok tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **laposabb fájlok beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-flatter-files-single-sign-on"></a>A laposabb fájlok egyetlen Sign-On konfigurálása

1. Jelentkezzen be a laposabb fájlok alkalmazásba rendszergazdaként.

2. Kattintson az **irányítópult** elemre. 
   
    ![Képernyőfelvétel: az "irányítópult" elem van kiválasztva a "laposabb fájlok" alkalmazásban.](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Kattintson a **Beállítások** elemre, majd hajtsa végre a következő lépéseket a **vállalat** lapon: 
   
    ![A "céges" fület, amely a "t L 2,0 a hitelesítéshez", a "t. m.](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **A hitelesítéshez válassza az SAML 2,0 használata** lehetőséget.
    
    b. Kattintson az **SAML konfigurálása** elemre.

4. Az **SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket: 
   
    ![Egyetlen Sign-On konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. A **tartomány** szövegmezőbe írja be a regisztrált tartományt.
   
   > [!NOTE]
   > Ha még nem rendelkezik regisztrált tartománnyal, vegye fel a kapcsolatot a laposabb fájlok támogatási csapatával a használatával [support@flatterfiles.com](mailto:support@flatterfiles.com) . 
    
    b. Az **Identity Provider URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az űrlap Azure Portal másolt.
   
    c.  Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    d. Kattintson a **Frissítés** parancsra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezi az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a laposabb fájlokhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **laposabb fájlok** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **laposabb fájlok** elemet.

    ![A laposabb fájlok hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-flatter-files-test-user"></a>Laposabb fájlok létrehozása tesztelési felhasználó

A szakasz célja, hogy létrehozzon egy Britta Simon nevű felhasználót a laposabb fájlokban.

**A Britta Simon nevű felhasználó a laposabb fájlokban való létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **laposabb fájlok** vállalati webhelyre rendszergazdaként.

2. A bal oldali navigációs ablaktáblán kattintson a **Beállítások** elemre, majd kattintson a **felhasználók** fülre.
   
    ![Képernyőkép, amely a "beállítások" lapot jeleníti meg a "felhasználók" lapon kiválasztva.](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kattintson az **Add User** (Felhasználó hozzáadása) elemre. 

4. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Laposabb fájlok felhasználójának létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. A **Utónév** szövegmezőbe írja be a következőt: **Britta**.
   
    b. A vezetéknév szövegmezőbe írja be a **Simon** **nevet** . 
   
    c. Az **E-mail cím** szövegmezőbe írja be a Britta e-mail-címét a Azure Portal.
   
    d. Kattintson a **Submit (Küldés**) gombra.   


### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a laposabb fájlok csempére kattint, automatikusan be kell jelentkeznie azokhoz a laposabb fájlokhoz, amelyekhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)