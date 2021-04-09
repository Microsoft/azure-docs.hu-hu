---
title: 'Oktatóanyag: Azure Active Directory integráció a Sauce Labs szolgáltatással – mobil-és webes tesztelés | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Sauce Labs – mobil-és webes tesztelés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895108"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Oktatóanyag: Azure Active Directory integráció a Sauce Labs szolgáltatással – mobil-és webes tesztelés

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sauce Labs-alapú mobil-és webes tesztelést Azure Active Directory (Azure AD) használatával.
A Sauce Labs integrálása – a mobil-és webes tesztelés az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Sauce Labs-Mobile és a web teszteléshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Sauce Labs-be a mobil-és webes tesztelésbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Sauce Labs-alapú mobil-és webes teszteléssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Sauce Labs – a mobil-és webes tesztelési egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Sauce Labs – a mobil-és webes tesztelés támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Sauce Labs – a mobil-és webes tesztelés **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>A Sauce Labs hozzáadása – mobil-és webes tesztelés a katalógusból

A Sauce Labs-alapú mobil-és webes tesztek Azure AD-be való integrálásának konfigurálásához a gyűjteményből a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Sauce Labs-Mobile és a webes tesztelést.

**A következő lépésekkel adhat hozzá szósz Labs-mobil-és webes tesztelési feladatokat a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Sauce Labs – mobil-és webes tesztelés**, válassza a **Sauce Labs-mobil és webes tesztelés** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Sauce Labs – mobil-és webes tesztelés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Sauce Labs-Mobile és a Web testing használatával a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Sauce Labs-beli kapcsolódó felhasználó közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés a Sauce Labs-alapú mobil-és webes teszteléssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Sauce Labs konfigurálása – mobil-és webes tesztelési egyszeri bejelentkezés](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Sauce Labs-alapú mobil-és webes tesztelési Teszt felhasználó](#create-sauce-labs---mobile-and-web-testing-test-user)** – a Britta Simon in Sauce Labs-mobil és webes tesztelési partnere, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Sauce Labs-alapú mobil-és webes teszteléssel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Sauce Labs – mobil-és webes tesztelési alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Sauce Labs – mobil-és webes tesztelési tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Sauce Labs beállítása – mobil-és webes tesztelés** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>A Sauce Labs konfigurálása – a mobil-és webes tesztelés egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be a Sauce Labs-be a mobil-és webes tesztelési céges webhelyre rendszergazdaként.

2. Kattintson a **felhasználó ikonra** , és válassza a **csoport kezelése** lapot.

    ![A "felhasználó" ikont és a "Team Management" legördülő listát bemutató képernyőkép.](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Adja meg a **tartománynevet** a szövegmezőben.

    ![Képernyőkép, amely egy példa tartománynevet jelenít meg a szövegmezőben.](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Kattintson a **Konfigurálás** fülre.

    ![Az "egyszeri bejelentkezés engedélyezve" területen kiválasztott "configure" (Konfigurálás) lapot megjelenítő képernyőkép.](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Az **egyszeri bejelentkezés konfigurálása** szakaszban hajtsa végre a következő lépéseket.

    ![Egyetlen Sign-On konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kattintson a **Tallózás** gombra, és töltse fel a letöltött metaadat-fájlt az Azure ad-ből.

    b. Jelölje be az **igény szerinti üzembe helyezés engedélyezése** jelölőnégyzetet.

    c. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Sauce Labs-Mobile és a web teszteléshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Sauce Labs-mobil és webes tesztelés** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Sauce Labs-mobil és webes tesztelés** lehetőséget.

    ![A Sauce Labs – mobil-és webes tesztelési hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Sauce Labs létrehozása – mobil-és webes tesztelési Teszt felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Sauce Labs-Mobile és a Web testing szolgáltatásban. A Sauce Labs – a mobil-és webes tesztelés támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Sauce Labs-ben – mobil-és webes tesztelés –, akkor a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Sauce Labs-mobil-és webes tesztelési támogatási csapathoz](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a szósz Labs-mobil és webes tesztelés csempére kattint, automatikusan be kell jelentkeznie a Sauce Labs-Mobile és a web tesztelési szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)