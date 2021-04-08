---
title: 'Oktatóanyag: Azure Active Directory integráció a ScreenSteps-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ScreenSteps között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999790"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Oktatóanyag: Azure Active Directory integráció a ScreenSteps

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ScreenSteps a Azure Active Directory (Azure AD) szolgáltatással.
A ScreenSteps és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a ScreenSteps.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a ScreenSteps (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ScreenSteps való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* ScreenSteps egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ScreenSteps támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps hozzáadása a gyűjteményből

A ScreenSteps Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ScreenSteps a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha ScreenSteps szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ScreenSteps** kifejezést, válassza a **ScreenSteps** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ScreenSteps az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az ScreenSteps-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a ScreenSteps kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés ScreenSteps való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[ScreenSteps egyszeri bejelentkezés konfigurálása](#configure-screensteps-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre ScreenSteps-teszt felhasználót](#create-screensteps-test-user)** – hogy a ScreenSteps Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés ScreenSteps való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **ScreenSteps** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![ScreenSteps tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **ScreenSteps beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-screensteps-single-sign-on"></a>ScreenSteps egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ScreenSteps vállalati webhelyre rendszergazdaként.

1. Kattintson a **Fiókbeállítások** lehetőségre.

    ![Fiókkezelés](./media/screensteps-tutorial/ic778523.png "Fiókkezelés")

1. Kattintson az **egyszeri bejelentkezés** elemre.

    ![Képernyőkép, amely az "egyszeri bejelentkezés" elemet jeleníti meg.](./media/screensteps-tutorial/ic778524.png "Távoli hitelesítés")

1. Kattintson az **egyszeri bejelentkezési végpont létrehozása** lehetőségre.

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778525.png "Távoli hitelesítés")

1. Az **egyszeri bejelentkezési végpont létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Hitelesítési végpont létrehozása](./media/screensteps-tutorial/ic778526.png "Hitelesítési végpont létrehozása")

    a. A **title (cím** ) szövegmezőbe írja be a címet.

    b. A **Mode (mód** ) listából válassza ki az **SAML** elemet.

    c. Kattintson a **Létrehozás** lehetőségre.

1. **Szerkessze** az új végpontot.

    ![Végpont szerkesztése](./media/screensteps-tutorial/ic778528.png "Végpont szerkesztése")

1. Az **egyszeri bejelentkezési végpont szerkesztése** szakaszban hajtsa végre a következő lépéseket:

    ![Távoli hitelesítési végpont](./media/screensteps-tutorial/ic778527.png "Távoli hitelesítési végpont")

    a. Kattintson az **Új SAML-tanúsítványfájl feltöltése** elemre, majd töltse fel a tanúsítványt, amelyet a Azure Portalról töltött le.

    b. Illessze be a **bejelentkezési URL** értékét, amelyet a Azure Portal a **távoli bejelentkezési URL** szövegmezőbe másolt.

    c. Illessze be a **kijelentkezési URL-címet** , amelyet a Azure Portal másolt a kijelentkezési **URL-** szövegmezőbe.

    d. Válassza ki azt a **csoportot** , amelyhez hozzá szeretné rendelni a felhasználókat a kiépítés során.

    e. Kattintson a **Frissítés** parancsra.

    f. Másolja a **SAML-felhasználó URL-címét** a vágólapra, és illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    : Térjen vissza az **egyszeri bejelentkezési végpont szerkesztéséhez**.

    h. Kattintson az **alapértelmezett fiók létrehozása** gombra, hogy ezt a végpontot használja a ScreenSteps bejelentkező összes felhasználóhoz. Azt is megteheti, hogy a **Hozzáadás a helyhez** gombra kattint, hogy ezt a végpontot a **ScreenSteps** meghatározott helyeire használja.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a ScreenSteps hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **ScreenSteps** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **ScreenSteps** lehetőséget.

    ![Az ScreenSteps hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-screensteps-test-user"></a>ScreenSteps-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a ScreenSteps-ben. A [ScreenSteps ügyfél-támogatási csapattal](https://www.screensteps.com/contact) együttműködve veheti fel a felhasználókat a ScreenSteps-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ScreenSteps csempére kattint, automatikusan be kell jelentkeznie arra a ScreenSteps, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)