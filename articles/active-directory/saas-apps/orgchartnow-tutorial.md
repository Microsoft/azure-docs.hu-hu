---
title: 'Oktatóanyag: Azure Active Directory integráció a szervezeti diagrammal most | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a szervezeti diagram között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522325"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Oktatóanyag: Azure Active Directory integráció a szervezeti diagrammal most

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja most a szervezeti diagramot Azure Active Directory (Azure AD) használatával.
A szervezeti diagram most az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy kik férhessenek hozzá a szervezeti diagramhoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a szervezeti diagramba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha most szeretné konfigurálni az Azure AD-integrációt a szervezeti diagrammal, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* A szervezeti diagram most egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A szervezeti diagram mostantól támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>A szervezeti diagram felvétele most a gyűjteményből

A szervezeti diagramnak az Azure AD-be való integrálásának konfigurálásához most hozzá kell adnia a diagramot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha most a katalógusból szeretné hozzáadni a szervezeti diagramot, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **szervezeti diagram most** kifejezést, válassza a **szervezeti diagram most** az eredmény panelről elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A szervezeti diagram most az eredmények listájában szerepel](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a szervezeti diagrammal a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a szervezeti diagramhoz kapcsolódó felhasználó közötti kapcsolati kapcsolat létrehozása szükséges.

Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a szervezeti diagrammal most a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a szervezeti diagramot most egyszeri bejelentkezéssel](#configure-orgchart-now-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításának konfigurálásához.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[szervezeti diagram létrehozása most tesztelheti a felhasználót](#create-orgchart-now-test-user)** – hogy a Britta Simon partnere legyen a szervezeti egységben, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a szervezeti diagrammal szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **szervezeti diagram most** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    ![A szervezeti diagram most tartományi és URL-címek egyszeri bejelentkezési adatai](common/idp-identifier.png)

    Az **azonosító** szövegmezőbe írja be az URL-címet:  `https://sso2.orgchartnow.com`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![image](common/both-preintegrated-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` az **Azure ad-azonosító** a következő oktatóanyagban ismertetett, a **szervezeti diagram létrehozása most** szakaszban leírtak szerint lett átmásolva.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **szervezeti diagram létrehozása most** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-orgchart-now-single-sign-on"></a>A szervezeti diagram konfigurálása most egy Sign-On

Ha most szeretné beállítani az egyszeri bejelentkezést a **szervezeti diagramon** , el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portalról a [szervezeti diagramra](mailto:ocnsupport@officeworksoftware.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a szervezeti diagramhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **szervezeti diagram most** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **szervezeti diagram most** lehetőséget.

    ![A szervezeti diagram most hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-orgchart-now-test-user"></a>Szervezeti diagram létrehozása most test User

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a szervezeti diagramba, most a szervezeti diagramon kell kiépíteni őket. 

1. A szervezeti egység mostantól támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Új felhasználó jön létre a szervezeti diagram elérésére tett kísérlet során, ha még nem létezik. Az igény szerinti felhasználó-kiépítési szolgáltatás csak **olvasható** felhasználót hoz létre, ha egy egyszeri bejelentkezésre vonatkozó kérelem egy felismert identitásszolgáltató származik, és az SAML-kijelentésben szereplő e-mail-cím nem található a felhasználói listán. Ehhez az automatikus kiépítési szolgáltatáshoz létre kell hoznia egy **általános** nevű hozzáférési csoportot a szervezeti diagramban. Hozzáférési csoport létrehozásához kövesse az alábbi lépéseket:

    a. A felhasználói felület jobb felső sarkában található **fogaskerékre** kattintva lépjen a **csoportok kezelése** lehetőségre.

    ![Szervezeti diagram most csoportok](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Válassza a **Hozzáadás** ikont, és nevezze el a csoport **általános** elemét, majd kattintson az **OK** gombra. 

    ![A szervezeti diagram most hozzáadva](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Válassza ki azokat a mappákat, amelyekhez az általános vagy a csak olvasási jogosultsággal rendelkező felhasználók hozzáférhetnek:

    ![Szervezeti diagram most mappák](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zárolja** a mappákat, hogy csak a rendszergazda felhasználók módosíthassák azokat. Ezután nyomja meg **az OK** gombot.

    ![A szervezeti diagram most zárolva van](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Ha **rendszergazdai** felhasználókat szeretne létrehozni, és **olvasási/írási** felhasználókat szeretne használni, manuálisan kell létrehoznia egy felhasználót, hogy a jogosultsági SZINThez hozzáférjen az SSO-n keresztül. Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

    a. Jelentkezzen be a szervezeti diagramba most egy biztonsági rendszergazdaként.

    b.  Kattintson a jobb felső sarokban található **Beállítások** elemre, majd navigáljon a **felhasználók kezeléséhez**.

    ![A szervezeti diagram most beállításai](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kattintson a **Hozzáadás** gombra, és hajtsa végre a következő lépéseket:

    ![A szervezeti diagram most már felügyelve van](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * A **felhasználói azonosító** szövegmezőbe írja be a felhasználói azonosítót, például **brittasimon \@ contoso.com**.

    * Az **e-mail-cím** szövegmezőbe írja be a felhasználó, például a **brittasimon \@ contoso.com**-e-mail-címét.

    * Kattintson a **Hozzáadás** parancsra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a szervezeti diagram most csempére kattint, automatikusan be kell jelentkeznie a szervezeti diagramba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)