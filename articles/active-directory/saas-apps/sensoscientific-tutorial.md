---
title: 'Oktatóanyag: Azure Active Directory integráció a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszere között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 27512ac694f20544f1fdd5b79b27d7bf2cabdd66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675502"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Oktatóanyag: Azure Active Directory integráció a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerét Azure Active Directory (Azure AD) használatával.
A SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer támogatja a **identitásszolgáltató** kezdeményezett egyszeri bejelentkezést

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer hozzáadása a katalógusból

A SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer** elemet, válassza a **SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer** elemet az eredmény panelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerhez kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer egyszeri bejelentkezésének konfigurálása](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre SensoScientific vezeték nélküli hőmérséklet-figyelési Rendszerteszt felhasználót](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** – hogy rendelkezzen a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer Britta, amely a felhasználó Azure ad-beli képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerrel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **SensoScientific vezeték nélküli hőmérséklet-figyelés** rendszeralkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer tartománya és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>A SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerének konfigurálása egyetlen Sign-On

1. Jelentkezzen be a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszeralkalmazásba rendszergazdaként.

1. A felső navigációs menüben kattintson a **konfiguráció** **elemre** , és **az egyszeri bejelentkezés** lehetőségre kattintva nyissa meg az egyszeri bejelentkezés beállításait, és hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Válassza ki a **kiállító nevét** Azure ad-ként.

    b. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosítót** .

    c. Az **egyszeres Sign-On szolgáltatás URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    d. Az **egyszeres Sign-Out szolgáltatás URL-címe** szövegmezőbe illessze be azt a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    e. Tallózzon a Azure Portal letöltött tanúsítványon, és töltse fel ide.

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer** elemet.

    ![Az SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer tesztelési felhasználójának létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerbe, a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerbe kell azokat kiépíteni. A [SensoScientific vezeték nélküli hőmérséklet-figyelési rendszertámogatási csapatával](https://www.sensoscientific.com/contact-us/) felveheti a felhasználókat a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszer csempére kattint, automatikusan be kell jelentkeznie a SensoScientific vezeték nélküli hőmérséklet-figyelési rendszerébe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)