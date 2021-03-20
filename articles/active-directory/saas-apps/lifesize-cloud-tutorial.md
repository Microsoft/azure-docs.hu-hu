---
title: 'Oktatóanyag: Azure Active Directory integráció a életnagyságú-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a életnagyságú-felhő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 483377d0376a5a98d822392af1fa2d84ad8498e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458591"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Oktatóanyag: Azure Active Directory integráció a életnagyságú-felhővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a életnagyságú-felhőt Azure Active Directory (Azure AD-val).
A életnagyságú Cloud és az Azure AD integrálásával a következő előnyöket nyújtja:

* A életnagyságú-felhőhöz hozzáférő Azure AD-ben is szabályozható.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a életnagyságú felhőbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció életnagyságú-felhővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Életnagyságú-alapú felhőalapú egyszeri bejelentkezéses előfizetés

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A életnagyságú-felhő támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A életnagyságú Cloud támogatja a felhasználók **automatikus** üzembe helyezését

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Életnagyságú-felhő hozzáadása a gyűjteményből

A életnagyságú Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a életnagyságú-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A életnagyságú-felhőnek a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **életnagyságú Cloud** kifejezést, válassza a **életnagyságú Cloud** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Életnagyságú felhő az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a életnagyságú-felhővel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a életnagyságú-felhőben lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés életnagyságú-felhővel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Életnagyságú Felhőbeli egyszeri bejelentkezés konfigurálása](#configure-lifesize-cloud-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy életnagyságú Cloud test User](#create-lifesize-cloud-test-user)** -t, hogy a életnagyságú-felhőben található, a felhasználó Azure ad-beli Britta kapcsolt,
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a életnagyságú-felhővel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **életnagyságú Cloud** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Életnagyságú felhőalapú tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-relay.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.lifesizecloud.com/ls/?acs`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.lifesizecloud.com/<companyname>`

    c. Kattintson a **további URL-címek beállítása** elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és továbbítási állapottal. Vegye fel a kapcsolatot a [életnagyságú felhőalapú](https://www.lifesize.com/en/support) ügyfélszolgálatával Sign-On URL-cím és azonosító értékek beszerzéséhez, és a továbbítási állapot értékének beszerzéséhez az oktatóanyag későbbi részében ISMERTETett SSO-konfigurációból. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **életnagyságú-felhő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-lifesize-cloud-single-sign-on"></a>A életnagyságú Cloud Single Sign-On konfigurálása

1. Ha be szeretné állítani az egyszeri bejelentkezést az alkalmazáshoz, jelentkezzen be a életnagyságú Cloud alkalmazásba rendszergazdai jogosultságokkal.

2. A jobb felső sarokban kattintson a nevére, majd az **előre megadott beállítások** elemre.

    ![A képernyőképen a speciális beállítások menüpont látható.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Az előzetes beállítások lehetőségnél kattintson az **egyszeri bejelentkezés konfigurációs** hivatkozására. Ekkor megnyílik a példány SSO konfigurációs lapja.

    ![Képernyőfelvétel: speciális beállítások, ahol kiválaszthatja az S S O konfigurációt.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Most konfigurálja a következő értékeket az SSO konfigurációs felhasználói felületén.

    ![Képernyőfelvétel: az S S O konfiguráció lapja, ahol megadhatja a leírt értékeket.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Az **Identity Provider kiállító** szövegmezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b.  A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. Nyissa meg az alap-64 kódolású tanúsítványt a Jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.
  
    d. Az SAML attribútum-hozzárendelések az utónév szövegmezőbe mezőben adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. Az SAML attribútum a **vezetéknévhez** való leképezése mezőben adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Az **e-mail** -szövegmezőhöz tartozó SAML-attribútumhoz adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. A konfiguráció ellenőrzéséhez kattintson a **teszt** gombra.

    >[!NOTE]
    >A sikeres teszteléshez el kell végeznie a konfigurációs varázslót az Azure AD-ben, és hozzáférést kell biztosítania azokhoz a felhasználókhoz vagy csoportokhoz, akik elvégezhetik a tesztet.

6. Engedélyezze az egyszeri bejelentkezést az egyszeri **bejelentkezés engedélyezése** gomb ellenőrzésével.

7. Most kattintson a **frissítés** gombra, hogy minden beállítás mentve legyen. Ezzel létrehozza a RelayState értéket. Másolja a szövegmezőbe létrehozott RelayState értéket, illessze be a **Relay State** szövegmezőbe a **életnagyságú Cloud domain és URLs** szakaszban.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a életnagyságú-felhőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **életnagyságú-felhő** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **életnagyságú-felhő** elemet.

    ![Az életnagyságú Felhőbeli hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-lifesize-cloud-test-user"></a>Életnagyságú Cloud test-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a életnagyságú-felhőben. A életnagyságú-felhő támogatja a felhasználók automatikus üzembe helyezését. Az Azure AD-beli sikeres hitelesítés után a rendszer automatikusan kiépíti a felhasználót az alkalmazásba.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a életnagyságú Cloud (felhő) csempére kattint, akkor a életnagyságú Cloud Application bejelentkezési lapját kell beolvasnia. Itt meg kell adnia a felhasználónevét, majd ezután átirányítjuk az alkalmazást a kezdőlapra.

További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)