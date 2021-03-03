---
title: 'Oktatóanyag: Azure Active Directory integráció a bónusszal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és bónuszok között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: e93d03ac9ec404a18b480a0cd1bfe289944b60d8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689113"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Oktatóanyag: Azure Active Directory integráció a Bonus-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a bónuszokat a Azure Active Directory (Azure AD) szolgáltatással.
Az Azure AD-vel való prémium integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a bónuszok eléréséhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a prémium szintű (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció prémium bevezetéséhez a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Egyszeri bejelentkezésre engedélyezett egyszeri előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogató bónuszok

## <a name="adding-bonusly-from-the-gallery"></a>Bónuszok hozzáadása a katalógusból

Az Azure AD-be való bónuszok integrálásának konfigurálásához bónuszokat kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha bónuszt szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Bonus**, válassza a **bónuszok** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Bónuszok az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD-alapú egyszeri bejelentkezést az **Britta Simon** nevű teszt-felhasználó alapján, prémium szintűen konfigurálja és teszteli.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség.

Az Azure AD egyszeri bejelentkezés prémium szintű konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[prémium szintű egyszeri bejelentkezés konfigurálása](#configure-bonusly-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre prémium szintű tesztelési felhasználót](#create-bonusly-test-user)** – hogy a Britta Simon partnere legyen a bónuszban, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés prémium szintű konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **prémium** szintű alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A prémium szintű tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-reply.png)

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez vegye fel [a kapcsolatot az ügyfélszolgálattal](https://bonus.ly/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **prémium szakasz beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-bonusly-single-sign-on"></a>Bónusz egyszeri Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **bónusz** bérlőbe.

1. A felső eszköztáron kattintson a **Beállítások** , majd az **integrációk és alkalmazások** elemre.

    ![Rendkívüli közösségi szakasz](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Az **egyszeri bejelentkezés** területen válassza az **SAML** lehetőséget.

1. Az **SAML** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Rendkívüli SAML-párbeszédpanel](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. A **IDENTITÁSSZOLGÁLTATÓ SSO-cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    c. A **identitásszolgáltató kiállító** szövegmezőben illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.
    
    d. Illessze be Azure Portalba másolt **ujjlenyomatot** a **CERT ujjlenyomat** szövegmezőbe.
    
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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha prémium szintű hozzáférést biztosít.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **bónuszok** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **bónuszok** lehetőséget.

    ![Az alkalmazások listájában szereplő prémium hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-bonusly-test-user"></a>Prémium tesztelési felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Bónuszokba, akkor azt bónuszban kell kiépíteni. Bónusz esetén a kiépítés manuális feladat.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármely más, prémium jogosultsággal rendelkező felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat fel. 

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. A webböngésző ablakban jelentkezzen be a bónusz bérlőbe.

1. Kattintson a **Beállítások** elemre.

    ![Beállítások](./media/bonus-tutorial/ic781041.png "Beállítások")

1. Kattintson a **felhasználók és bónuszok** fülre.

    ![Felhasználók és bónuszok](./media/bonus-tutorial/ic781042.png "Felhasználók és bónuszok")

1. Kattintson a **felhasználók kezelése** lehetőségre.

    ![Felhasználók kezelése](./media/bonus-tutorial/ic781043.png "Felhasználók kezelése")

1. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

    ![Képernyőfelvétel: a felhasználók kezelése, ahol a felhasználó hozzáadása lehetőséget választhatja.](./media/bonus-tutorial/ic781044.png "Felhasználó hozzáadása")

1. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználó hozzáadása párbeszédpanel, amelyen megadhatja ezeket az adatokat.](./media/bonus-tutorial/ic781045.png "Felhasználó hozzáadása")  

    a. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    b. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `brittasimon@contoso.com` .

    d. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az Azure AD-fiók tulajdonosa egy e-mailt kap, amely tartalmaz egy hivatkozást, amely megerősíti a fiókot, mielőtt az aktívvá válna.  

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a bónusz csempére kattint, automatikusan be kell jelentkeznie a bónuszba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)