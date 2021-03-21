---
title: 'Oktatóanyag: Azure Active Directory integráció a Signagelive-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Signagelive között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 78324cfa58a8ac015b085052bdec7e3793befc1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348398"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Oktatóanyag: Azure Active Directory integráció a Signagelive

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Signagelive a Azure Active Directory (Azure AD) szolgáltatással.
A Signagelive és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Signagelive.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Signagelive (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való SaaS-alkalmazások integrálásával kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory](../manage-apps/what-is-single-sign-on.md). Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Signagelive való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is beszerezhet.
* Egy Signagelive egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Signagelive támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive hozzáadása a gyűjteményből

A Signagelive Azure AD-ba való integrálásának konfigurálásához először adja hozzá a Signagelive a katalógusból a felügyelt SaaS-alkalmazások listájához.

Ha Signagelive szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza a **Azure Active Directory** ikont.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található  **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Signagelive** kifejezést. 

     ![Signagelive az eredmények listájában](common/search-new-app.png)

5. Válassza az **Signagelive** lehetőséget az eredmények ablaktábláján, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Signagelive-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Signagelive-ben.

Az Azure AD egyszeri bejelentkezés Signagelive való konfigurálásához és teszteléséhez először végezze el a következő építőelemeket:

1. Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
2. [Konfigurálja az Signagelive egyszeri bejelentkezést](#configure-signagelive-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
3. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. [Hozzon létre egy Signagelive](#create-a-signagelive-test-user) , amely a felhasználó Azure ad-Britta kapcsolódó, a Signagelive-hoz tartozó Simon-beli partneri kapcsolattal rendelkezik.
6. Az [egyszeri bejelentkezés tesztelésével](#test-single-sign-on) ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Signagelive való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Signagelive** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri **Bejelentkezés módszerének kiválasztása** párbeszédpanelen válassza az **SAML** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza a **Szerkesztés** lehetőséget az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Signagelive tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** mezőben adjon meg egy URL-címet, amely a következő mintát használja:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [Signagelive ügyfél-támogatási csapatához](mailto:support@signagelive.com). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (RAW)** letöltéséhez a szükséges beállítások alapján. Ezután mentse a számítógépére.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **Signagelive beállítása** szakaszban másolja ki a szükséges URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-signagelive-single-sign-on"></a>Signagelive egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a Signagelive oldalon, küldje el a letöltött **tanúsítvány (RAW)** és a másolt URL-címeket a Azure Portal a [Signagelive támogató csapatnak](mailto:support@signagelive.com). Gondoskodnak arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: " brittasimon@yourcompanydomain.extension ". Ebben az esetben például megadhatja a következőt: " BrittaSimon@contoso.com ".

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a jelszó mezőben megjelenő értéket.

    d. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Signagelive hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Signagelive** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Signagelive** lehetőséget.

    ![Az Signagelive hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** gombot. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanel **felhasználók** listájában válassza a **Britta Simon** elemet. Ezután kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-a-signagelive-test-user"></a>Signagelive-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Signagelive-ben. Együttműködik a [Signagelive támogatási csapatával](mailto:support@signagelive.com) , hogy hozzáadja a felhasználókat a Signagelive platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hoznia és aktiválnia kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a MyApps-portál használatával.

Amikor kiválasztja a **Signagelive** csempét a MyApps-portálon, automatikusan be kell jelentkeznie. További információ a MyApps-portálról: [Mi a MyApps-portál?](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)