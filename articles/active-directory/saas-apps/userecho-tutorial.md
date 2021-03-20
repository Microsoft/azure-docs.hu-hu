---
title: 'Oktatóanyag: Azure Active Directory integráció a UserEcho-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és UserEcho között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92509372"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Oktatóanyag: Azure Active Directory integráció a UserEcho

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a UserEcho a Azure Active Directory (Azure AD) szolgáltatással.
A UserEcho és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a UserEcho.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a UserEcho (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció UserEcho való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* UserEcho egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A UserEcho támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-userecho-from-the-gallery"></a>UserEcho hozzáadása a gyűjteményből

A UserEcho Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a UserEcho a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha UserEcho szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **UserEcho** kifejezést, válassza a **UserEcho** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![UserEcho az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az UserEcho-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a UserEcho kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés UserEcho való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[UserEcho egyszeri bejelentkezés konfigurálása](#configure-userecho-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre UserEcho-teszt felhasználót](#create-userecho-test-user)** – hogy a UserEcho Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés UserEcho való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **UserEcho** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![UserEcho tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.userecho.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a UserEcho ügyfélszolgálati [csapatához](https://feedback.userecho.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **UserEcho beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-userecho-single-sign-on"></a>UserEcho egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a UserEcho vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a felhasználónevére a menü kibontásához, majd kattintson a **telepítés** elemre.
   
    ![Képernyőfelvétel: a UserEcho helyről kiválasztott beállítás.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Kattintson az **integrációk** elemre.
   
    ![A képernyőfelvételen a beállítások menüből kiválasztott integrációk láthatók.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Kattintson a **webhely** elemre, majd az **egyszeri bejelentkezés (egy saml2)** elemre.
   
    ![A képernyőképen az egyszeri bejelentkezés egy SAML2 van kiválasztva az integrációk menüből.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Az **egyszeri bejelentkezés (SAML)** oldalon hajtsa végre a következő lépéseket:
   
    ![A képernyőképen az egyszeri bejelentkezési SAML-oldal látható, ahol megadhatja a leírt értékeket.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Ha **SAML-kompatibilis**, válassza az **Igen** lehetőséget.
    
    b. Illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portal az **SAML SSO URL** szövegmezőbe másolt.
    
    c. Illessze be a kijelentkezési **URL-címet**, amelyet a Azure Portal a **távoli kijelentkezési URL** szövegmezőbe másolt.
    
    d. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.
    
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
  
    b. A Felhasználónév mezőbe írja be a **nevet** brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a UserEcho hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **UserEcho** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **UserEcho** lehetőséget.

    ![Az UserEcho hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-userecho-test-user"></a>UserEcho-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a UserEcho-ben.

**A következő lépések végrehajtásával hozhat létre egy Britta Simon nevű felhasználót a UserEcho-ben:**

1. Jelentkezzen be a UserEcho vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a felhasználónevére a menü kibontásához, majd kattintson a **telepítés** elemre.
   
    ![Képernyőfelvétel: a UserEcho helyről kiválasztott beállítás.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. A **felhasználók** szakasz kibontásához kattintson a **felhasználók** elemre.
   
    ![A képernyőfelvételen a beállítások menüből kiválasztott felhasználók láthatók.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Kattintson a **felhasználók** elemre.
   
    ![Képernyőfelvétel: a kiválasztott felhasználók.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Kattintson **az új felhasználó meghívása** elemre.
   
    ![A képernyőképen az új felhasználói vezérlő meghívása látható.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Az **új felhasználó meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: az új felhasználó meghívása párbeszédpanel, amelyen megadhatja a felhasználói adatokat.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. A **név** szövegmezőbe írja be a felhasználó nevét, például a Britta Simon nevet.
    
    b.  Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .
    
    c. Kattintson a **meghívás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a UserEcho csempére kattint, automatikusan be kell jelentkeznie arra a UserEcho, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)