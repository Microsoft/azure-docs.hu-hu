---
title: 'Oktatóanyag: Azure Active Directory Az eTMF-integráció | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Azure Active Directory eTMF között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: f0744b98b0264a79072e60ddb7627a4b527bdf1e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600607"
---
# <a name="tutorial-azure-active-directory-integration-with-wingspan-etmf"></a>Oktatóanyag: Azure Active Directory Az eTMF-integráció integrálása

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Azure Active Directory eTMF-et.
Az eTMF És az Azure AD integrálása a következő előnyöket biztosítja:

* Az Azure AD-ban szabályozhatja, hogy ki férhet hozzá a Canpan eTMF-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkezzenek be Az Azure AD-fiókjukkal az ETMF-be (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne tudni az SaaS-alkalmazások Azure [AD-val](../manage-apps/what-is-single-sign-on.md)való integrációjáról, tekintse meg a Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD és a Majdpan eTMF integrációjának konfigurálása érdekében a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nincs Azure AD-környezete, ingyenes fiókot [is kaphat](https://azure.microsoft.com/free/)
* Az eTMF egyszeri bejelentkezést engedélyező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* Az eTMF támogatja az **SP által** kezdeményezett SSO-t

## <a name="adding-wingspan-etmf-from-the-gallery"></a>Az eTMF hozzáadása a katalógusból

Ahhoz, hogy a Rendszerpan eTMF integrálható lesz az Azure AD-be, hozzá kell adni a Galleryből a Listához a Felügyelt SaaS-alkalmazások listáját.

**Ha a Katalógusból szeretne hozzáadni Egy eTMF-t, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** bal oldali navigációs panelen kattintson a Azure Active Directory **ikonra.**

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás** lehetőséget.

    ![A Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **párbeszédpanel tetején** található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a Következőt: Ezután írja be **a Következőt: ETMF,** válassza ki Az eredménypanelen a **Majd** kattintson a Hozzáadás gombra az alkalmazás hozzáadásához. 

     ![Az eTMF-fájl a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli a Következővel: Az eTMF-et egy Britta Simon nevű **tesztfelhasználó alapján.**
Ahhoz, hogy az egyszeri bejelentkezés működjön, kapcsolati kapcsolatot kell létrehozni egy Azure AD-felhasználó és a Kapcsolódó felhasználó között a Következőben: Az eTMF-ben.

Az Azure AD egyszeri bejelentkezésÉnek a Az Azure AD eTMF-fel való konfiguráláshoz és teszteléshez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy a felhasználók használják ezt a funkciót.
2. **[Configure Fogpan eTMF Single Sign-On](#configure-wingspan-etmf-single-sign-on)** –, hogy konfigurálja az Sign-On-beállításokat az alkalmazás oldalán.
3. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – Az Azure AD egyszeri bejelentkezésének tesztelése Britta Simon használatával.
4. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti Britta Simonnak az Azure AD egyszeri bejelentkezés használatát.
5. **[Hozzon létre Egy eTMF-tesztfelhasználót,](#create-wingspan-etmf-test-user)** hogy legyen britta Simon az eTMF-ban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
6. **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezését a Majdpan eTMF-fel konfigurálja, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Lap Lap ETMF** alkalmazásintegrációs oldalán válassza az **Egyszeri bejelentkezés lehetőséget.**

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az Egyszeri **bejelentkezési módszer** kiválasztása párbeszédpanelen válassza az **SAML/WS-Fed** mód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési módja](common/select-saml-option.png)

3. A Set up Single Sign-On with SAML  (Egyetlen alkalmazás beállítása **SAML-sel)** lapon kattintson a Szerkesztés ikonra az **SamL-alapkonfiguráció** párbeszédpanel megnyitásához.

    ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    ![Az eTMF tartományra és URL-címekre vonatkozó egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva: `https://<customer name>.<instance name>.mywingspan.com/saml`

    b. Az Azonosító **mezőbe** írjon be egy URL-címet a következő mintával: `http://saml.<instance name>.wingspan.com/shibboleth`

    c. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva: `https://<customer name>.<instance name>.mywingspan.com/`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges URLSign-On, azonosító és válasz URL-cím értékével. Ezeket az értékeket az eTMF-ügyfél ügyfélszolgálati csapatával kapcsolatba lépve szerezze be. A minta az **SAML-konfiguráció** alapszintű szakaszában látható mintákat is Azure Portal.

5. Az Egyszeres hitelesítés beállítása **samlSign-On val** lapon, az **SAML** aláíró tanúsítvány szakaszban kattintson a Letöltés gombra az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre. 

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

6. A **Set upMetszéspan eTMF** (A Windows eTMF beállítása) szakaszban másolja ki a megfelelő URL-cím(eke)t a saját követelményének megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-wingspan-etmf-single-sign-on"></a>Az eTMF Single Sign-On

Ha egyszeri bejelentkezést konfigurál az **eTMF** oldalon, el kell küldenie a letöltött összevonási metaadatok **XML-ét** és a megfelelő másolt URL-címeket a Azure Portal-ből a Tokpan eTMF támogatási csapatának. Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ennek a szakasznak az a célja, hogy létrehoz egy Britta Simon nevű tesztfelhasználót Azure Portal-ban.

1. A Azure Portal bal oldali panelen válassza a Azure Active Directory lehetőséget, válassza **a** **Felhasználók** lehetőséget, majd a **Minden felhasználó lehetőséget.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" hivatkozás](common/users.png)

2. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságai között hajtsa végre az alábbi lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A Név **mezőbe írja** be **a következőt: BrittaSimon.**
  
    b. A Felhasználónév **mezőbe írja be a** következőt: brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje **be a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít a Következő eTMF-hez:

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a **Minden** alkalmazás lehetőséget, végül pedig a **eTMF lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Majdpan eTMF lehetőséget.**

    ![Az Applications (Alkalmazások) listában található Az eTMF-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása gombra,** majd a Hozzárendelés **hozzáadása párbeszédpanelen válassza** a Felhasználók és csoportok lehetőséget. 

    ![A Hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A Felhasználók **és csoportok párbeszédpanelen** válassza **Britta Simon**  gombra a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.

6. Ha bármilyen szerepkörértéket vár az SAML helyességi feltételben, akkor a Szerepkör kiválasztása párbeszédpanelen  válassza ki a felhasználónak megfelelő szerepkört a listából, majd kattintson a képernyő alján található Kijelölés gombra. 

7. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

### <a name="create-wingspan-etmf-test-user"></a>ETMF-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Következő eTMF-fájlban: Britta Simon. A Felhasználók hozzáadása a Fogpan eTMF platformhoz az eTMF-csapattal együtt. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel.

Amikor a hozzáférési panel-ban a Hozzáférési panel eTMF csempére kattint, automatikusan be kell jelentkezve lennie arra a Lappan eTMF-be, amelyhez beállította az SSO-t. További információ a [hozzáférési panel: Bevezetés a](../user-help/my-apps-portal-end-user-access.md)hozzáférési panel.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok listája az SaaS-alkalmazások Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
