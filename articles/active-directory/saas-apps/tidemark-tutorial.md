---
title: 'Oktatóanyag: Azure Active Directory integrálása a Tidemark | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Tidemark között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8cbccc3fb4cc513d7c86253ae07fbced4626a15e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599655"
---
# <a name="tutorial-azure-active-directory-integration-with-tidemark"></a>Oktatóanyag: Azure Active Directory integrálása a Tidemarkkal

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Tidemarkot a Azure Active Directory (Azure AD) használatával.
A Tidemark és az Azure AD integrálása a következő előnyöket biztosítja:

* Az Azure AD-ban szabályozhatja, hogy ki férhet hozzá a Tidemarkhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkezzenek be a Tidemarkba (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne tudni az SaaS-alkalmazások Azure [AD-val](../manage-apps/what-is-single-sign-on.md)való integrációjáról, tekintse meg a Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD és a Tidemark integrációjának konfigurálása érdekében a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nincs Azure AD-környezete, itt kaphat egy hónapos [próbaverziót](https://azure.microsoft.com/pricing/free-trial/)
* Pipa egyszeri bejelentkezést engedélyező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Tidemark támogatja **az SP által** kezdeményezett SSO-t

## <a name="adding-tidemark-from-the-gallery"></a>A Tidemark hozzáadása a katalógusból

A Tidemark Azure AD-be való integrálásának konfigurálához fel kell vennie a Tidemark jelölőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha tidemarkot szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A bal **[Azure Portal](https://portal.azure.com)** panelen kattintson a Bal Azure Active Directory **ikonra.**

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás** lehetőséget.

    ![A Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **párbeszédpanel tetején** található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Tidemark**, majd a **Tidemark** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A jelölőjel az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli a Tidemark használatával egy Britta Simon nevű **tesztfelhasználó alapján.**
Az egyszeri bejelentkezéshez kapcsolati kapcsolatot kell létrehozni egy Azure AD-felhasználó és a Tidemarkban található kapcsolódó felhasználó között.

Az Azure AD-alapú egyszeri bejelentkezés tidemark használatával való konfiguráláshoz és teszteléshez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** – ezzel a funkcióval engedélyezheti a felhasználók számára.
2. **[A Tidemark egyszeri bejelentkezés konfigurálása](#configure-tidemark-single-sign-on)** – az Sign-On konfigurálja az alkalmazásoldalon.
3. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – Az Azure AD egyszeri bejelentkezésének tesztelése Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti Britta Simonnak az Azure AD egyszeri bejelentkezés használatát.
5. **[Hozzon létre egy Tidemark](#create-tidemark-test-user)** tesztfelhasználót, hogy Britta Simon megfelelője legyen a Tidemarkban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
6. **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD-alapú egyszeri bejelentkezés tidemark beállításhoz hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Tidemark** alkalmazásintegrációs oldalán válassza az **Egyszeri bejelentkezés lehetőséget.**

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az Egyszeri **bejelentkezési módszer** kiválasztása párbeszédpanelen válassza az **SAML/WS-Fed mód** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési módja](common/select-saml-option.png)

3. A Set up Single Sign-On with SAML (Egyetlen alkalmazás beállítása **SAML-sel)** lapon kattintson az **Edit** (Szerkesztés) ikonra az SamL Basic Configuration (SamL-alapkonfiguráció) **párbeszédpanel megnyitásához.**

    ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    ![A Tidemark tartományra és URL-címekre vonatkozó egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával:

    - `https://<subdomain>.tidemark.com/login`
    - `https://<subdomain>.tidemark.net/login`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával:


    - `https://<subdomain>.tidemark.com/saml`
    - `https://<subdomain>.tidemark.net/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket az értékeket a Tidemark ügyféltámogatási csapattól kapják meg. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-hez) lap **SAML** aláíró tanúsítvány szakaszában kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

6. A **Tidemark beállítása** szakaszban másolja ki a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-tidemark-single-sign-on"></a>A Tidemark single Sign-On

Az egyszeri bejelentkezés **tidemark** oldalán való konfigurálához el kell küldenie a letöltött tanúsítványt **(Base64)** és a megfelelő másolt URL-címeket a Azure Portal a Tidemark támogatási csapatának. Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ennek a szakasznak az a célja, hogy létrehoz egy Britta Simon nevű tesztfelhasználót Azure Portal a tesztfelhasználóban.

1. A Azure Portal bal oldali panelen válassza a Azure Active Directory lehetőséget, válassza **a** **Felhasználók** lehetőséget, majd a **Minden felhasználó lehetőséget.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" hivatkozás](common/users.png)

2. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságai között hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A Név **mezőbe írja** be **a BrittaSimon nevet.**
  
    b. A Felhasználónév **mezőbe írja be** a következőt: brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje **be a Jelszó megjelenítése** jelölőnégyzetet, majd írja fel a Jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure-beli egyszeri bejelentkezés használatát a Tidemarkhoz való hozzáférés engedélyezésével.

1. A Azure Portal válassza a Vállalati **alkalmazások** lehetőséget, válassza a **Minden alkalmazás** lehetőséget, majd a **Tidemark lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Tidemark lehetőséget.**

    ![A Tidemark hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása gombra,** majd válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés hozzáadása **párbeszédpanelen.**

    ![A Hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A Felhasználók **és csoportok párbeszédpanelen** válassza **Britta Simont** a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra. 

6. Ha bármilyen szerepkörértéket vár az SAML helyességi feltételben, akkor a Szerepkör kiválasztása párbeszédpanelen  válassza ki a felhasználónak megfelelő szerepkört a listából, majd kattintson a képernyő alján található Kijelölés gombra. 

7. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

### <a name="create-tidemark-test-user"></a>A Tidemark tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Tidemarkban. A Tidemark támogatási csapatával együtt vegye fel a felhasználókat a Tidemark platformra. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel.

Amikor a csempén a Tidemark hozzáférési panel, automatikusan be kell jelentkezve lennie arra a Tidemarkra, amelyhez beállította az SSO-t. További információ a [hozzáférési panel: Bevezetés a hozzáférési panel.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok listája az SaaS-alkalmazások és a Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
