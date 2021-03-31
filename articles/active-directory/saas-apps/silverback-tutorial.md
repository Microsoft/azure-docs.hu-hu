---
title: 'Oktatóanyag: Azure Active Directory integráció a Silverback-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Silverback között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004177"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Oktatóanyag: Azure Active Directory integráció a Silverback

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Silverback a Azure Active Directory (Azure AD) szolgáltatással.
A Silverback és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Silverback.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Silverback (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Silverback való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Silverback egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Silverback támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-silverback-from-the-gallery"></a>Silverback hozzáadása a gyűjteményből

A Silverback Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Silverback a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Silverback szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Silverback** kifejezést, válassza a **Silverback** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Silverback az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Silverback-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Silverback kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Silverback való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Silverback egyszeri bejelentkezés konfigurálása](#configure-silverback-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Silverback-teszt felhasználót](#create-silverback-test-user)** – hogy a Silverback Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Silverback való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Silverback** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Silverback tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `<YOURSILVERBACKURL>.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a Silverback ügyfélszolgálati [csapatához](mailto:helpdesk@matrix42.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Silverback egyetlen Sign-On konfigurálása

1. Egy másik böngészőben jelentkezzen be a Silverback-kiszolgálóra rendszergazdaként.

2. Navigáljon a **rendszergazdai**  >  **hitelesítésszolgáltatóhoz**.

3. A **hitelesítési szolgáltató beállításai** oldalon hajtsa végre a következő lépéseket:

    ![A rendszergazda](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Kattintson az **Importálás URL-** címről elemre.

    b.  Illessze be a másolt metaadatok URL-címét, és kattintson **az OK** gombra.

    c.  Erősítse meg az **OK gombot** , és a rendszer automatikusan kitölti az értékeket.

    d.  **A Megjelenítés engedélyezése a bejelentkezési oldalon**.

    e.  Engedélyezze a **dinamikus felhasználói létrehozást** , ha az Azure ad-ben engedélyezett felhasználók számára automatikusan szeretne hozzáadni (nem kötelező).

    f.  Hozzon létre egy **címet** a gombhoz az önkiszolgáló portálon.

    :  A **fájl kiválasztása** elemre kattintva töltsön fel egy **ikont** .

    h.  Válassza ki a **gomb háttérszínét** .

    i.  Kattintson a **Mentés** gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Silverback hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Silverback** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Silverback** lehetőséget.

    ![Az Silverback hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-silverback-test-user"></a>Silverback-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Silverback, a Silverback kell kiépíteni őket. A Silverback-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Silverback-kiszolgálóra rendszergazdaként.

2. Navigáljon a **felhasználókhoz** , és **adjon hozzá egy új eszközt használó felhasználóhoz**.

3. Az **alapszintű** oldalon hajtsa végre a következő lépéseket:

    ![A felhasználó](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználó nevét (például **Britta**).

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    d. Az **E-mail cím** szövegmezőbe írja be a felhasználó, például a **Brittasimon \@ contoso.com**-e-mail-címét.

    e. A **jelszó** szövegmezőbe írja be a jelszavát.

    f. A **Jelszó megerősítése** szövegmezőbe írja be újra a jelszót, és erősítse meg.

    : Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha nem kívánja manuálisan létrehozni az egyes felhasználókat, engedélyezze a **dinamikus felhasználói létrehozás** jelölőnégyzetet a **rendszergazdai**  >  **hitelesítés szolgáltató** területen.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Silverback csempére kattint, automatikusan be kell jelentkeznie arra a Silverback, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)