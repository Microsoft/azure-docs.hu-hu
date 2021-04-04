---
title: 'Oktatóanyag: Azure Active Directory integráció kis fejlesztési funkciókkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a kis-és nagyvállalati változások között.
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
ms.openlocfilehash: 6eced120a05ddaca8d8cf426fd2a977891b3e36b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997137"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Oktatóanyag: Azure Active Directory integráció kis mértékű Tökéletesítésekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a kisméretű fejlesztési funkciókat Azure Active Directory (Azure AD) használatával.
Az Azure AD kisméretű fejlesztése az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy a kis mértékben javuljon a hozzáférés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a kis-és nagyszámú (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció kis mértékű Tökéletesítésekkel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Kisméretű, egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A kis mértékű tökéletesítések támogatják az **SP** által KEZDEMÉNYEZett SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Kis mértékű tökéletesítések hozzáadása a katalógusból

A kis fejlesztéseknek az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógus kis fejlesztéseit a felügyelt SaaS-alkalmazások listájához.

**Ha kisebb fejlesztéseket szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **kis fejlesztéseket**, válassza az eredmények panel **kis fejlesztések** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az eredmények listájának kisméretű fejlesztése](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését végezheti el kis fejlesztésekkel a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség.

Az Azure AD egyszeri bejelentkezés kis fejlesztésekkel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Kis teljesítményű, **[egyszeri bejelentkezés konfigurálása](#configure-small-improvements-single-sign-on)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre kis fejlesztéseket vizsgáló felhasználót](#create-small-improvements-test-user)** – hogy a Britta Simon partnere legyen a kis fejlesztésekben, amelyek a felhasználó Azure ad-képviseletéhez kapcsolódnak.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés kis mértékű Tökéletesítésekkel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **kis** teljesítményű alkalmazások integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Kis mértékű fejlesztési tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.small-improvements.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez vegye fel a kapcsolatot a [kisméretű fejlesztési](mailto:support@small-improvements.com) ügyfélszolgálattal. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A kis-és **nagyvállalati beállítások beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-small-improvements-single-sign-on"></a>Kis-és nagyszámú javítást konfigurálhat egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be a kisméretű fejlesztési céges webhelyre rendszergazdaként.

1. A fő irányítópult lapon kattintson a bal oldali **Adminisztráció** gombra.

    ![Képernyőfelvétel: a kiválasztott adminisztráció gomb.](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kattintson az **integrációk** részben az **SAML SSO** gombra.

    ![A képernyőképen az integrációs szolgáltatásban kiválasztott SAML S S O ikon látható.](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:

    ![A képernyőképen az S S O Setup (S S O Setup) oldal jelenik meg, ahol megadhatja a leírt értékeket.](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. A **http-végpont** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    b. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja ki a tartalmat, majd illessze be a **x509-tanúsítvány** szövegmezőbe. 

    c. Ha szeretné, hogy az SSO és a bejelentkezési űrlap hitelesítése lehetőség elérhető legyen a felhasználók számára, jelölje be a **hozzáférés engedélyezése a bejelentkezéshez vagy a jelszóhoz** lehetőséget.  

    d. Adja meg a megfelelő értéket, hogy az **SAML-prompt** szövegmezőben nevezze el az SSO bejelentkezés gombot.  

    e. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban lehetővé teszi, hogy Simon Britta az Azure egyszeri bejelentkezés használatát azáltal, hogy a kis mértékű Tökéletesítésekhez hozzáférést biztosít.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **kis mértékű javítást**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **kis mértékű tökéletesítések** lehetőséget.

    ![Az alkalmazások listájának kis fejlesztési hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-small-improvements-test-user"></a>Kis fejlesztéseket vizsgáló felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók kis javításokban jelentkezzenek be, kis javításokkal kell kiépíteni őket. Kis javítások esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a kisméretű fejlesztési céges webhelyre rendszergazdaként.

1. A kezdőlapon lépjen a bal oldali menüre, majd kattintson az **Adminisztráció** elemre.

1. Kattintson a felhasználói **könyvtár** gombra a felhasználói kezelés szakaszban.

    ![A képernyőképen a felügyeleti áttekintésből kiválasztott felhasználói könyvtár látható.](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kattintson a **felhasználók hozzáadása** elemre.

    ![Képernyőfelvétel: a felhasználók hozzáadása gomb.](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. A **felhasználók hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket: 

    ![Képernyőfelvétel: a felhasználók hozzáadása párbeszédpanel, ahol megadhatja a leírt értékeket.](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Adja meg a felhasználó utónevét, például a **Britta** **nevet** .

    b. Adja meg a felhasználó **vezetéknevét** (például **Simon**).

    c. Adja meg a felhasználóhoz hasonló **E-mail címet** **brittasimon@contoso.com** .

    d. Azt is megteheti, hogy a személyes üzenetet az **értesítő E-mail küldése** mezőben adja meg. Ha nem szeretné elküldeni az értesítést, törölje a jelet a jelölőnégyzetből.

    e. Kattintson a **felhasználók létrehozása** lehetőségre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a kis mértékű tökéletesítések csempére kattint, automatikusan be kell jelentkeznie az egyszeri bejelentkezéshez beállított kis méretekre. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)