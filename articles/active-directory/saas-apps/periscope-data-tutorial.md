---
title: 'Oktatóanyag: Azure Active Directory integráció a periszkóp-szolgáltatással | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést Azure Active Directory és periszkóp között.
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
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520857"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Oktatóanyag: Azure Active Directory integráció a periszkóp-szolgáltatásokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a periszkóp-információkat Azure Active Directory (Azure AD) használatával.
A periszkóp-és az Azure AD-integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a periszkóp-adatbázisokhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy periszkóp az adataikat (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció periszkóp-adatokkal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Periszkóp-alapú egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A periszkóp-ben az **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-periscope-data-from-the-gallery"></a>Periszkóp-adatok hozzáadása a gyűjteményből

A periszkóp-adatok Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a periszkóp adatait a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A periszkóp-adatok katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **periszkóp adatok** kifejezést, válassza a **periszkóp adatok** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Periszkóp az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a periszkóp-val a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti periszkóp-adatkapcsolatra van szükség.

Az Azure AD egyszeri bejelentkezés periszkóp-alapú használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Periszkóp-alapú egyszeri bejelentkezés konfigurálása](#configure-periscope-data-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Periszkóp-adattesztelési felhasználó létrehozása](#create-periscope-data-test-user)** – ha a periszkóp-ben a felhasználó Azure ad-képviseletéhez kapcsolódó Britta Simon-beli partnere van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés periszkóp-adatokkal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **periszkóp** adatalkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Periszkóp adattartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be a következő URL-címeket:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értékeket a tényleges bejelentkezési URL-címmel. Ha ezt az értéket és az azonosító értékét szeretné megkapni, lépjen kapcsolatba a [periszkóp-adatügyfél támogatási csoportjával](mailto:support@periscopedata.com) , **amelyet az oktatóanyag későbbi részében** talál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Periszkóp-adatSign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a periszkóp-be rendszergazdaként.

2. Nyissa meg a fogaskerék menüt a bal alsó sarokban, és nyissa meg a **Számlázási**  >  **Biztonság** menüt, és hajtsa végre az alábbi lépéseket. Csak a rendszergazdák férhetnek hozzá ezekhez a beállításokhoz.

    ![Képernyőkép, amely a "biztonság" párbeszédpanelt jeleníti meg a kiválasztott beállításokkal.](./media/periscope-data-tutorial/configure01.png)

    a. Másolja az **alkalmazás-összevonás metaadat-URL-címét** #5 **SAML-aláíró tanúsítvány** lépéséről, és nyissa meg egy böngészőben. Ekkor megnyílik egy XML-dokumentum.

    b. Az **egyszeri bejelentkezés** szövegmezőben válassza a **Azure Active Directory** lehetőséget.

    c. Keresse meg a címke **SingleSignOnService** , és illessze be a **Location** értéket az **SSO URL-** szövegmezőbe.

    d. Keresse meg a címke **SingleLogoutService** , és illessze be a **Location** értéket az **slo URL** szövegmezőbe.

    e. Másolja a példány **azonosító** értékét, és illessze be az **alapszintű SAML-konfigurációs** szakasz **azonosító (Entity ID)** szövegmezőbe Azure Portal.

    f. Keresse meg az XML-fájl első címkéjét, másolja ki a **entityID** értékét, és illessze be a **kiállító** szövegmezőbe.

    : Keresse meg az SAML protokollt használó **IDPSSODescriptor** címkét. A szakaszon belül keresse meg a címke- **leírót** a **use = aláírással**. másolja a **x509** értékét, és illessze be a **tanúsítvány** szövegmezőbe.

    h. A több szóközzel rendelkező helyek kiválaszthatják az alapértelmezett helyet az **alapértelmezett szóköz** legördülő listából. Ez lesz az a hely, amelyet új felhasználók adnak hozzá, amikor első alkalommal jelentkeznek be a periszkóp-ba, és a Active Directory egyszeri bejelentkezéssel vannak kiépítve.

    i. Végül kattintson a **Mentés** gombra, és **erősítse meg** az egyszeri bejelentkezési beállítások módosítását a **kijelentkezés** beírásával.

    ![A "kijelentkezés" nevű, "kijelentkezés" és a "megerősítés" gomb kiválasztásakor megjelenő képernyőkép.](./media/periscope-data-tutorial/configure02.png)

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a periszkóp-adathoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **periszkóp**-adatelemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **periszkóp**-adatelemet.

    ![A periszkóp-adatkapcsolat az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-periscope-data-test-user"></a>Periszkóp-adattesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a periszkóp-ba, periszkóp-adatként kell kiépíteni őket. A periszkóp-adatokban a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a periszkóp-be rendszergazdaként.

2. Kattintson a menü bal alsó részén található **Beállítások** ikonra, és navigáljon az **engedélyek** elemre.

    ![Képernyőkép, amely a "beállítások" menüt jeleníti meg az "engedélyek" lehetőség kiválasztásával.](./media/periscope-data-tutorial/configure03.png)

3. Kattintson a **felhasználó hozzáadása** elemre, és hajtsa végre a következő lépéseket:

      ![Periszkóp adatok konfigurálása](./media/periscope-data-tutorial/configure04.png)

    a. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **brittasimon \@ contoso.com**-e-mail-címét.

    d. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a periszkóp-adatcsempe elemre kattint, automatikusan be kell jelentkeznie azokra a periszkóp-adatlapokra, amelyekhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)