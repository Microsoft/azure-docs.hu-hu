---
title: 'Oktatóanyag: Azure Active Directory integráció a központi asztallal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a központi asztal között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 36ba61c86082e191831c2c890de4466181f1a4db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674187"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Azure Active Directory integráció a központi asztallal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a központi asztalt Azure Active Directory (Azure AD) használatával.
A központi asztal az Azure AD-vel való integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a központi asztalhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a központi asztalra (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a központi asztallal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Központi asztali egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A központi asztal támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-central-desktop-from-the-gallery"></a>Központi asztal hozzáadása a gyűjteményből

A központi asztal Azure AD-integrációjának konfigurálásához hozzá kell adnia a központi asztalt a galériából a felügyelt SaaS-alkalmazások listájához.

**A központi asztal a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **központi asztal** kifejezést, válassza a **központi asztal** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Központi asztal az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a központi asztallal a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a központi asztal kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés központi asztallal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[központi asztal egyszeri bejelentkezésének konfigurálása](#configure-central-desktop-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy központi asztali tesztelési felhasználót](#create-central-desktop-test-user)** – a Britta Simon a Central Desktopban, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a központi asztallal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **központi asztali** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Központi asztali tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.centraldesktop.com`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek megszerzéséhez lépjen kapcsolatba a [központi asztali](https://imeetcentral.com/contact-us) ügyfélszolgálattal. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (RAW)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **központi asztal beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-central-desktop-single-sign-on"></a>A központi asztal egyetlen Sign-On konfigurálása

1. Jelentkezzen be a **központi asztali** bérlőbe.

2. Válassza a **Beállítások lehetőséget**. Válassza a **speciális** lehetőséget, majd az **egyszeri bejelentkezés** lehetőséget.

    ![Beállítás – speciális](./media/central-desktop-tutorial/ic769563.png "Beállítás – speciális")

3. Az **egyszeri bejelentkezés beállításai** oldalon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési beállítások](./media/central-desktop-tutorial/ic769564.png "Egyszeri bejelentkezés beállításai")

    a. Válassza **az SAML v2 egyszeri bejelentkezés engedélyezése** lehetőséget.

    b. Az **egyszeri bejelentkezési URL-cím** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Az **SSO bejelentkezési URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL** -értéket.

    d. Az **SSO kijelentkezési URL-címe** mezőbe illessze be a Azure Portalból másolt **KIjelentkezési URL** -értéket.

4. Az **üzenet aláírása ellenőrzési módszer** szakaszban hajtsa végre a következő lépéseket:

    ![Üzenet-aláírás ellenőrzési módszere](./media/central-desktop-tutorial/ic769565.png "Üzenet-aláírás ellenőrzési módszere")

    a. Válassza a **Tanúsítvány** elemet.

    b. Az **SSO-tanúsítvány** listában válassza az **rsh sha256** elemet.

    c. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben. Ezután másolja be a tanúsítvány tartalmát, és illessze be az **SSO-tanúsítvány** mezőbe.

    d. Válassza **a SAMLv2-bejelentkezési oldal hivatkozásának megjelenítése** lehetőséget.

    e. Válassza a **Frissítés** lehetőséget.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a központi asztalhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **központi asztal** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **központi asztal** elemet.

    ![A központi asztal hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-central-desktop-test-user"></a>Központi asztali tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudják jelentkezni, a központi asztali alkalmazásban kell kiépíteni őket. Ez a szakasz azt ismerteti, hogyan hozhat létre Azure AD-felhasználói fiókokat a központi asztalon.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármely más, a központi asztal által biztosított, központi asztali felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

**Felhasználói fiókok kiépítése a központi asztalra:**

1. Jelentkezzen be a központi asztali bérlőbe.

2. Válassza a **személyek** lehetőséget, majd válassza a **belső Tagok hozzáadása** lehetőséget.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Az **új tagok E-mail címe** mezőbe írja be a kiépíteni kívánt Azure ad-fiókot, majd kattintson a **tovább** gombra.

    ![Új tagok e-mail-címei](./media/central-desktop-tutorial/ic781052.png "Új tagok e-mail-címei")

4. Válassza a **belső tag (ok) hozzáadása** lehetőséget.

    ![Belső tag hozzáadása](./media/central-desktop-tutorial/ic781053.png "Belső tag hozzáadása")

   > [!NOTE]
   > A hozzáadott felhasználók egy e-mailt kapnak, amely tartalmazza a fiókjuk aktiválásához szükséges megerősítési hivatkozást.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a központi asztal csempére kattint, automatikusan be kell jelentkeznie arra a központi asztalra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)