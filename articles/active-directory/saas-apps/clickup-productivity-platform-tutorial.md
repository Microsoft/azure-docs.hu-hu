---
title: 'Oktatóanyag: Azure Active Directory integráció a ClickUp hatékonyságnövelő platformmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a ClickUp hatékonyságnövelő platformja között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 753256adddf8b28518bac45011d582bc7a079629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97670400"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Oktatóanyag: Azure Active Directory integráció a ClickUp hatékonyságnövelő platformmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ClickUp hatékonyságnövelő platformot Azure Active Directory (Azure AD) használatával.
A ClickUp hatékonyságnövelő platform és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a ClickUp hatékonyságnövelő platformhoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a ClickUp termelékenységi platformra (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ClickUp hatékonyságnövelő platformmal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* ClickUp hatékonyságnövelő platform egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ClickUp hatékonyságnövelő platform támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>ClickUp hatékonyságnövelő platform hozzáadása a katalógusból

A ClickUp hatékonyságnövelő platform Azure AD-integrációjának konfigurálásához hozzá kell adnia a ClickUp hatékonyságnövelő platformot a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A ClickUp hatékonyságnövelő platformnak a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ClickUp hatékonyságnövelő platform** kifejezést, válassza a **ClickUp termelékenységi platform** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![ClickUp hatékonyságnövelő platform az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a ClickUp hatékonyságnövelő platformmal konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a ClickUp hatékonyságnövelő platformon a kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés ClickUp hatékonyságnövelő platformmal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[ClickUp hatékonyságnövelő platform egyszeri bejelentkezésének konfigurálása](#configure-clickup-productivity-platform-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre ClickUp hatékonyságnövelő platform-tesztelési felhasználót](#create-clickup-productivity-platform-test-user)** – hogy a Britta Simon partnere legyen a ClickUp hatékonyságnövelő platformon, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés ClickUp hatékonyságnövelő platformmal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **ClickUp hatékonyságnövelő platform** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![ClickUp hatékonyságnövelő platform tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://app.clickup.com/login/sso`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse ezt az értéket a tényleges azonosítóval, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>A ClickUp hatékonyságnövelő platform egyetlen Sign-Onának konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp hatékonyságnövelő platform-bérlőre rendszergazdaként.

2. Kattintson a **felhasználói profilra**, majd válassza a **Beállítások** lehetőséget.

    ![Képernyőfelvétel: a ClickUp termelékenységi bérlője a beállítások ikon kiválasztásával jelenik meg.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![A képernyőfelvételen a beállítások láthatók.](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Válassza a **Microsoft** lehetőséget az egyszeri Sign-On (SSO) szolgáltató alatt.

    ![A képernyőképen a Microsoft által kiválasztott hitelesítési ablaktábla látható.](./media/clickup-productivity-platform-tutorial/configure2.png)

4. A **Microsoft egyszeri bejelentkezés konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Microsoft egyszeri bejelentkezési oldalának konfigurálása, amelyen átmásolhatja a D entitást, és mentheti az Azure összevonási metaadatokat. R L.](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Kattintson a **Másolás** elemre az entitás-azonosító értékének másolásához, majd illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában lévő **azonosító (Entity ID)** szövegmezőbe.

    b. Az **Azure összevonási metaadatok URL-címe** szövegmezőbe illessze be az alkalmazás-összevonási metaadatok URL-címét, amelyet a Azure Portal másolt, majd kattintson a **Mentés** gombra.

5. A telepítés befejezéséhez kattintson a **hitelesítés a Microsofttal lehetőségre a telepítés befejezéséhez** és a Microsoft-fiókkal való hitelesítéshez.

    ![Képernyőfelvétel: a hitelesítés a Microsofttal a telepítés befejezéséhez gomb.](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a ClickUp hatékonyságnövelő platformhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **ClickUp hatékonyságnövelő platform** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **ClickUp hatékonyságnövelő platform** elemet.

    ![A ClickUp hatékonyságnövelő platform hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-clickup-productivity-platform-test-user"></a>ClickUp hatékonyságnövelő platform tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp hatékonyságnövelő platform-bérlőre rendszergazdaként.

2. Kattintson a **felhasználói profilra**, majd válassza a **személyek** lehetőséget.

    ![Képernyőfelvétel: a ClickUp hatékonyságnövelő bérlője.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Képernyőfelvétel: a személyek hivatkozás kiválasztva.](./media/clickup-productivity-platform-tutorial/user1.png)

3. Adja meg a felhasználó e-mail-címét a szövegmezőben, majd kattintson a **meghívás** elemre.

    ![Képernyőfelvétel: a csoport felhasználói beállításai, ahol e-mailben meghívhatja a személyeket.](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > A felhasználó megkapja az értesítést, és el kell fogadnia a meghívót a fiók aktiválásához.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ClickUp hatékonyságnövelő platform csempére kattint, automatikusan be kell jelentkeznie a ClickUp hatékonyságnövelő platformra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)