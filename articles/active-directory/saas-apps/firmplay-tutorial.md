---
title: 'Oktatóanyag: Azure Active Directory integráció a FirmPlay-vel – alkalmazottak érdekképviselete a toborzáshoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a FirmPlay – alkalmazottak érdekképviselete között a toborzáshoz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2230958fb41d8e42967beeca57cf10ea048d1ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453467"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Oktatóanyag: Azure Active Directory integráció a FirmPlay-vel – alkalmazottak érdekképviselete a toborzáshoz

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a FirmPlay-alkalmazottak érdekképviseletét Azure Active Directory (Azure AD) használatával történő toborzáshoz.
A FirmPlay integrálása az Azure AD-vel való toborzáshoz a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a FirmPlay-munkatársak Érdekképviseletéhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a FirmPlay-munkavállalói Érdekképviseletbe az Azure AD-fiókokkal való toborzáshoz (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció FirmPlay való konfigurálásához a toborzáshoz a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* FirmPlay – az egyszeri bejelentkezést engedélyező előfizetés toborzásával kapcsolatos alkalmazottak érdekképviselete

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* FirmPlay – alkalmazottak érdekképviselete a toborzáshoz az **SP** által kezdeményezett SSO-t támogatja

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>FirmPlay hozzáadása – alkalmazottak érdekképviselete a katalógusból való toborzáshoz

A FirmPlay-alkalmazottak Érdekképviseletének az Azure AD-be való toborzásának konfigurálásához hozzá kell adnia a FirmPlay-alkalmazottak érdekképviseletét a katalógusból a felügyelt SaaS-alkalmazások listájához való toborzáshoz.

**Az alábbi lépéseket követve adhat hozzá FirmPlay – alkalmazottak érdekképviseletét a katalógusból való toborzáshoz:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **FirmPlay-Employee érdekvédelmi elemet a toborzáshoz**, válassza a **FirmPlay – alkalmazottak érdekképviselete** lehetőséget az eredmények panelről való toborzáshoz, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![FirmPlay – alkalmazottak érdekképviselete az eredmények listájában való toborzáshoz](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a FirmPlay-Employee érdekvédelmi szolgáltatással a toborzáshoz a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a FirmPlay kapcsolódó felhasználó közötti kapcsolatra van szükség a toborzáshoz.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a FirmPlay-alkalmazottak érdekképviseleti szolgálatában a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[FirmPlay konfigurálása – az egyszeri bejelentkezés toborzása](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** – az alkalmazás oldalának Sign-On beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[FirmPlay létrehozása – alkalmazotti érdekképviselet a tesztelési felhasználó toborzásához](#create-firmplay---employee-advocacy-for-recruiting-test-user)** – a Britta Simon-hez tartozó, a FirmPlay-munkatársak érdekképviselete a felhasználók Azure ad-képviseletéhez kapcsolódó toborzáshoz.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezésének és a FirmPlay-alkalmazottak Érdekképviseletének konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az **FirmPlay-munkatársak érdekképviselete** lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![FirmPlay – alkalmazottak érdekképviselete a tartományok és URL-címek egyszeri bejelentkezési adatainak felvételéhez](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Vegye fel a kapcsolatot a [FirmPlay – alkalmazottak érdekképviselete az ügyfél-támogatási csoport toborzásához](mailto:engineering@firmplay.com) az érték beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **FirmPlay-Employee érdekképviselet beállítása a toborzáshoz** szakaszban másolja ki a megfelelő URL-címeket (ek) a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>FirmPlay konfigurálása – alkalmazottak érdekképviselete egyetlen Sign-On felvételéhez

Ha egyszeri bejelentkezést szeretne beállítani a **FirmPlay – alkalmazottak érdekképviselete a toborzáshoz** , el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portal [FirmPlay – a támogatási csapat toborzásához](mailto:engineering@firmplay.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a FirmPlay-alkalmazottak Érdekképviseletének a toborzáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **FirmPlay – alkalmazottak érdekképviselete lehetőséget a toborzáshoz**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **FirmPlay – alkalmazottak érdekképviselete lehetőséget a toborzáshoz**.

    ![FirmPlay – alkalmazottak érdekképviselete az alkalmazások listáján szereplő hivatkozás felvételéhez](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>FirmPlay létrehozása – alkalmazotti érdekképviselet a tesztelési felhasználó toborzásához

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a FirmPlay-Employee érdekvédelmi alkalmazásban a toborzáshoz. A [FirmPlay használata – alkalmazottak érdekképviselete a támogatási csapat](mailto:engineering@firmplay.com) felvételéhez, hogy felvegye a felhasználókat a FirmPlay a platform toborzásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a csempék toborzása FirmPlay gombra kattint, automatikusan be kell jelentkeznie a FirmPlay-Employee érdekvédelmi szolgáltatásba a toborzáshoz, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)