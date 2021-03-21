---
title: 'Oktatóanyag: Azure Active Directory integráció a InstaVR Viewerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és InstaVR megjelenítője között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460003"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Oktatóanyag: Azure Active Directory integráció a InstaVR Viewerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a InstaVR Viewert Azure Active Directory (Azure AD) használatával.
A InstaVR Viewer az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a InstaVR-megjelenítőhöz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a InstaVR megjelenítőbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció InstaVR-megjelenítővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* InstaVR-megjelenítő egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A InstaVR Viewer támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A InstaVR Viewer **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-instavr-viewer-from-the-gallery"></a>InstaVR-megjelenítő hozzáadása a katalógusból

A InstaVR Viewer Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a InstaVR-megjelenítőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A InstaVR Viewer a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **InstaVR Viewer** kifejezést, válassza a **InstaVR Viewer** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![InstaVR megjelenítője az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a InstaVR Viewerrel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség a InstaVR-megjelenítőben.

Az Azure AD egyszeri bejelentkezés InstaVR-megjelenítővel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[InstaVR-megjelenítő egyszeri bejelentkezésének konfigurálása](#configure-instavr-viewer-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy InstaVR Viewer test User](#create-instavr-viewer-test-user)** -t, hogy a Britta Simon partnere legyen a InstaVR-megjelenítőben, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés InstaVR-megjelenítővel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **InstaVR-megjelenítő** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![InstaVR-megjelenítő tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Nincs rögzített minta a bejelentkezés URL-címéhez. Akkor jön létre, amikor a InstaVR-megjelenítő ügyfél webes csomagolást végez. Minden ügyfél és csomag esetében egyedi. A pontos bejelentkezési URL-cím beszerzéséhez be kell jelentkeznie a InstaVR Viewer-példányba, és el kell végeznie a webes csomagolást.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse ezt az értéket az oktatóanyag későbbi részében ismertetett tényleges azonosító értékkel.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** és az **összevonási metaadatok fájljának** a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadata-certificatebase64.png)

6. A **InstaVR-megjelenítő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-instavr-viewer-single-sign-on"></a>A InstaVR Viewer egyetlen Sign-On konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a InstaVR-megjelenítő vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználó ikonra** , és válassza a **fiók** lehetőséget.

    ![Képernyőfelvétel: a InstaVR megjelenítője a kiválasztott felhasználóval.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Görgessen le az **SAML-hitelesítéshez** , és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML-hitelesítés oldal, ahol megadhatja az ebben a lépésben ismertetett értékeket.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Az **egyszeri bejelentkezés URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    b. A **kijelentkezési URL** szövegmezőbe illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    c. Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    d. A letöltött tanúsítványfájl feltöltéséhez kattintson a **frissítés** gombra.

    e. A letöltött összevonási metaadat-fájl feltöltéséhez kattintson a **frissítés** gombra.

    f. Másolja az **entitás-azonosító** értéket, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** szakaszának **azonosító (entitás azonosítója)** szövegmezőbe.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a InstaVR-megjelenítőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **InstaVR Viewer** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **InstaVR Viewer** elemet.

    ![Az InstaVR-megjelenítő hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-instavr-viewer-test-user"></a>InstaVR-megjelenítő tesztelési felhasználó létrehozása

Ebben a szakaszban a Britta Simon nevű felhasználó jön létre a InstaVR Viewerben. A InstaVR Viewer támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a InstaVR-megjelenítőben, a hitelesítés után létrejön egy újat. Ha bármilyen problémába ütközne, lépjen kapcsolatba a [InstaVR Viewer támogatási csapatával](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a InstaVR-megjelenítő vállalati webhelyre rendszergazdaként.

2. Válassza a **csomag** lehetőséget a bal oldali navigációs panelen, és válassza a **csomag készítése webre** lehetőséget.

    ![Képernyőfelvétel: a InstaVR megjelenítője céges webhelye a csomag kiválasztása és a csomag webes kiválasztásának tétele.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Válassza a **Letöltés** lehetőséget.

    ![A képernyőképen a kiválasztott Letöltés ikon látható.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Válassza az **üzemeltetett oldal megnyitása** azt követően, hogy a rendszer átirányítja az Azure ad-ba a bejelentkezéshez.

    ![A képernyőképen a megnyitott tárolt oldal látható.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Adja meg az Azure ad-beli hitelesítő adatait, hogy sikeresen bejelentkezzen az Azure AD-be az SSO használatával.

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)