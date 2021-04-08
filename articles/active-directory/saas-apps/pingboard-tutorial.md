---
title: 'Oktatóanyag: Azure Active Directory integráció a Pingboard-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pingboard között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d7b090eb5228a449212ceebd6cd299c5f8860089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520795"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Oktatóanyag: Azure Active Directory integráció a Pingboard

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pingboard a Azure Active Directory (Azure AD) szolgáltatással.
A Pingboard és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Pingboard.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Pingboard (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Pingboard való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Pingboard egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Pingboard támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

* A Pingboard támogatja az [automatikus felhasználó-kiépítés](./pingboard-provisioning-tutorial.md) használatát 

## <a name="adding-pingboard-from-the-gallery"></a>Pingboard hozzáadása a gyűjteményből

A Pingboard Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Pingboard a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Pingboard szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Pingboard** kifejezést, válassza a **Pingboard** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Pingboard az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Pingboard-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Pingboard kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Pingboard való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Pingboard egyszeri bejelentkezés konfigurálása](#configure-pingboard-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Pingboard-teszt felhasználót](#create-pingboard-test-user)** – hogy a Pingboard Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Pingboard való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Pingboard** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű S L konfiguráció" elemet mutatja az "azonosító" és a "válasz U R L" szövegmezőben, és a "Mentés" gomb van kiválasztva.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `http://app.pingboard.com/sp`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Pingboard tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a Pingboard ügyfélszolgálati [csapatához](https://support.pingboard.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Pingboard beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-pingboard-single-sign-on"></a>Pingboard egyetlen Sign-On konfigurálása

1. Ha az SSO-t Pingboard oldalon szeretné konfigurálni, nyisson meg egy új böngészőablakot, és jelentkezzen be a Pingboard-fiókjába. Az egyszeri bejelentkezés beállításához Pingboard-rendszergazdának kell lennie.

2. A felső menüben válassza az **alkalmazások > integrációk** lehetőséget.

    ![Egyetlen Sign-On konfigurálása](./media/pingboard-tutorial/Pingboard_integration.png)

3. Az **integrációk** lapon keresse meg a **"Azure Active Directory"** csempét, és kattintson rá.

    ![Pingboard – egyetlen Sign-On integráció](./media/pingboard-tutorial/Pingboard_aad.png)

4. Kattintson a **"Konfigurálás"** gombra a következő modálisban:

    ![Pingboard-konfiguráció gomb](./media/pingboard-tutorial/Pingboard_configure.png)

5. A következő oldalon láthatja, hogy az "Azure SSO-integráció engedélyezve van". Nyissa meg a letöltött metaadatok XML-fájlját egy Jegyzettömbben, és illessze be a tartalmat a **identitásszolgáltató-metaadatokba**.

    ![Pingboard SSO konfigurációs képernyő](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. A fájl érvényesítése megtörtént, és ha minden helyes, az egyszeri bejelentkezés engedélyezve lesz.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Pingboard hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Pingboard** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Pingboard** lehetőséget.

    ![Az Pingboard hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-pingboard-test-user"></a>Pingboard-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Pingboard-ben. A Pingboard támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](pingboard-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Pingboard vállalati webhelyre rendszergazdaként.

2. Kattintson az **"alkalmazott hozzáadása"** gombra a **könyvtár** lapon.

    ![Alkalmazott hozzáadása](./media/pingboard-tutorial/create_testuser_add.png)

3. Az **"alkalmazott hozzáadása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Személyek meghívása](./media/pingboard-tutorial/create_testuser_name.png)

    a. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a **Britta Simon** nevet.

    b. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet **brittasimon@contoso.com** .

    c. A **beosztás** szövegmezőbe írja be a Britta Simon nevű feladatot.

    d. A **hely** legördülő menüben válassza ki a Britta Simon helyét.

    e. Kattintson a **Hozzáadás** parancsra.

4. Egy megerősítő képernyő jelenik meg, amely megerősíti a felhasználó hozzáadását.

    ![Ellenőrizze](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve megerősíti a fiókját, mielőtt az aktívvá válna.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Pingboard csempére kattint, automatikusan be kell jelentkeznie arra a Pingboard, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [A felhasználók üzembe helyezésének konfigurálása](./pingboard-provisioning-tutorial.md)