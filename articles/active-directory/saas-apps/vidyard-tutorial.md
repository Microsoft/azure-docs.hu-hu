---
title: 'Oktatóanyag: Azure Active Directory integráció a Vidyard-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Vidyard között.
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
ms.openlocfilehash: 4176c92d48b67b9f9207f22ebd8939b5ec1437ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636748"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Oktatóanyag: Azure Active Directory integráció a Vidyard

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Vidyard a Azure Active Directory (Azure AD) szolgáltatással.
A Vidyard és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Vidyard.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Vidyard (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Vidyard való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Vidyard egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Vidyard támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

* A Vidyard **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard hozzáadása a gyűjteményből

A Vidyard Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Vidyard a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Vidyard szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Vidyard** kifejezést, válassza a **Vidyard** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Vidyard az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Vidyard-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Vidyard kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Vidyard való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Vidyard egyszeri bejelentkezés konfigurálása](#configure-vidyard-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Vidyard-teszt felhasználót](#create-vidyard-test-user)** – hogy a Vidyard Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Vidyard való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Vidyard** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges azonosító, a válasz URL-cím és a Sign-On URL-cím alapján fogja frissíteni, amelyet az oktatóanyag későbbi részében ismertet. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Vidyard beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-vidyard-single-sign-on"></a>Vidyard egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Vidyard Software vállalati webhelyére.

2. A Vidyard irányítópulton válassza a **csoport**  >  **Biztonság** lehetőséget.

    ![A képernyőképen a csoportból kiválasztott biztonság látható a Vidyard szoftver webhelyén.](./media/vidyard-tutorial/configure1.png)

3. Kattintson az **új profil** fülre.

    ![A képernyőképen az új profil gomb látható.](./media/vidyard-tutorial/configure2.png)

4. Az **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML konfigurációs szakasza, ahol megadhatja a leírt értékeket.](./media/vidyard-tutorial/configure3.png)

    a. Adja meg az általános profil nevét a **profilnév** szövegmezőben.

    b. Másolja az **SSO felhasználói bejelentkezési oldal** értékét, és illessze be a **bejelentkezési URL** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszának Azure Portal.

    c. Másolja az **ACS URL-címet** , és illessze be a **Válasz URL-** szövegmezőbe az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.

    d. Másolja **ki a kiállítói/metaadat URL-címet** , és illessze be az **azonosító** szövegmezőbe az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.

    e. Nyissa meg a letöltött tanúsítványfájl Azure Portal a Jegyzettömbben, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    f. Az **SAML-végpont URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    : Kattintson a **Megerősítés** gombra.

5. Az egyszeri bejelentkezés lapon válassza a **hozzárendelés** meglévő profil mellett lehetőséget.

    ![A képernyőképen az Azure A D S O-profil hozzárendelés gombja látható.](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Miután létrehozott egy SSO-profilt, rendelje hozzá bármely olyan csoporthoz, amelyhez a felhasználóknak hozzáférésre van szükségük az Azure-on keresztül. Ha a felhasználó nem létezik azon a csoporton belül, amelyhez hozzá lettek rendelve, a Vidyard automatikusan létrehoz egy felhasználói fiókot, és valós időben rendeli hozzá a szerepkört.

6. Válassza ki a szervezet csoportját, amely a **hozzárendelhető csoportok számára** látható.

    ![Képernyőfelvétel: az SAML-konfiguráció társítása a szervezetek szakaszhoz, ahol kiválaszthatja a csoportot.](./media/vidyard-tutorial/configure5.png)

7. A hozzárendelt csoportokat a **jelenleg hozzárendelt csoportok** területen tekintheti meg. Válasszon egy szerepkört a csoport számára a szervezet számára, és kattintson a **Confirm (megerősítés**) gombra.

    ![Képernyőfelvétel: az SAML-konfiguráció társítása a szervezetek szakaszhoz, ahol kiválaszthat egy szerepkört.](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > További információkért tekintse meg [ezt a dokumentációt](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Vidyard hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Vidyard** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Vidyard** lehetőséget.

    ![Az Vidyard hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-vidyard-test-user"></a>Vidyard-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Vidyard-ben. A Vidyard támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Vidyard-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Vidyard támogatási csapatával](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Vidyard csempére kattint, automatikusan be kell jelentkeznie arra a Vidyard, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)