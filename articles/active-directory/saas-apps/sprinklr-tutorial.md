---
title: 'Oktatóanyag: Azure Active Directory-integráció a Sprinklerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a sprinkler között.
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
ms.openlocfilehash: e76386c8c276c6c8c07751474b3718f98af18f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519452"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Oktatóanyag: Azure Active Directory integráció a Sprinklerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sprinklert Azure Active Directory (Azure AD) használatával.
A sprinkler az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Sprinklerhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Sprinklerbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Sprinklerrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Egyszeri bejelentkezésre engedélyezett permetező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A sprinkler támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinkler hozzáadása a katalógusból

A sprinkler Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy Sprinklert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával veheti fel a Sprinklert a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **sprinkler** kifejezést, válassza ki a **sprinkler** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A kipermetező az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az **Britta Simon** nevű teszt felhasználója konfigurálja és teszteli a sprinklertel.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a permetező kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a sprinkler használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[sprinkler egyszeri bejelentkezésének beállítása](#configure-sprinklr-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy sprinkler test User](#create-sprinklr-test-user)** -to have a Britta Simon a sprinklerben, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a sprinkler használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **sprinkler** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A sprinkler tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.sprinklr.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez vegye fel a kapcsolatot a [sprinkler](https://www.sprinklr.com/contact-us/) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Kipermetező beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-sprinklr-single-sign-on"></a>A permetező egyszeri Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a sprinkler vállalati webhelyre rendszergazdaként.

1. Nyissa meg az **Adminisztráció \> beállításait**.

    ![Felügyelet](./media/sprinklr-tutorial/ic782907.png "Felügyelet")

1. Nyissa meg a **partner \> egyszeri bejelentkezés kezelése** elemet a bal oldali ablaktáblán.

    ![Partner kezelése](./media/sprinklr-tutorial/ic782908.png "Partner kezelése")

1. Kattintson az **+ egyszeri bejelentkezések hozzáadása** lehetőségre.

    ![A képernyőképen az egyszeri bejelentkezések hozzáadása gomb látható.](./media/sprinklr-tutorial/ic782909.png "Egyetlen Sign-Ons")

1. Az **egyszeri bejelentkezés** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyszeri bejelentkezési oldal, amelyen megadhatja a leírt értékeket.](./media/sprinklr-tutorial/ic782910.png "Egyetlen Sign-Ons")

    a. A **név** szövegmezőbe írja be a konfiguráció nevét (például: *WAADSSOTest*).

    b. Válassza az **Engedélyezve** lehetőséget.

    c. Válassza **az új SSO-tanúsítvány használata** lehetőséget.

    d. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    e. Illessze be azt az **Azure ad-azonosító** értéket, amelyet az Azure Portalról az **entitás-azonosító** szövegmezőbe másolt.

    f. Illessze be a **bejelentkezési URL-címet** , amelyet az Azure Portalról másolt az **Identity Provider bejelentkezési URL-címe** szövegmezőbe.

    : Illessze be a **kijelentkezési URL-címet** , amelyet az Azure Portalról másolt a **személyazonosság-szolgáltató kijelentkezési URL-címe** szövegmezőbe.

    h. Az **SAML felhasználói azonosító típusa** beállításnál válassza az **állítás a felhasználó sprinklr.com felhasználónevét tartalmazza** lehetőséget.

    i. Az **SAML felhasználói azonosító helyeként** válassza a **felhasználói azonosító elemet a tulajdonos utasítás név azonosító elemében**.

    j. Kattintson a **Mentés** gombra.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Sprinklerhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **sprinkler** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **sprinkler** elemet.

    ![Az alkalmazások listán szereplő sprinkler hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sprinklr-test-user"></a>Sprinkler tesztelési felhasználó létrehozása

1. Jelentkezzen be a sprinkler vállalati webhelyre rendszergazdaként.

1. Nyissa meg az **Adminisztráció \> beállításait**.

    ![Felügyelet](./media/sprinklr-tutorial/ic782907.png "Felügyelet")

1. Lépjen a bal oldali ablaktábla **ügyfél- \> felhasználók kezelése** elemére.

    ![Képernyőfelvétel: a felhasználó hozzáadása gomb a beállítások/felhasználók elemben.](./media/sprinklr-tutorial/ic782914.png "Beállítások")

1. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

    ![Képernyőfelvétel: a felhasználó szerkesztése párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/sprinklr-tutorial/ic782915.png "Beállítások")

1. A **felhasználó szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felhasználó szerkesztése](./media/sprinklr-tutorial/ic782916.png "Felhasználó szerkesztése")

    a. Az **e-mail**, **Utónév** és **vezetéknév** szövegmezőbe írja be a kiépíteni kívánt Azure ad-felhasználói fiók adatait.

    b. Válassza a **jelszó letiltva** lehetőséget.

    c. Válassza a **Language (nyelv**) lehetőséget.

    d. Válassza a **felhasználó típusa** lehetőséget.

    e. Kattintson a **Frissítés** parancsra.

    > [!IMPORTANT]
    > A **jelszó letiltva** beállítás kiválasztásával engedélyezhető, hogy a felhasználó bejelentkezzen egy identitás-szolgáltatón keresztül. 

1. Nyissa meg a **szerepkört**, majd hajtsa végre a következő lépéseket:

    ![Partneri szerepkörök](./media/sprinklr-tutorial/ic782917.png "Partneri szerepkörök")

    a. A **globális** listából válassza a **ALL_Permissions** lehetőséget.  

    b. Kattintson a **Frissítés** parancsra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a sprinkler által biztosított felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a sprinkler csempére kattint, automatikusan be kell jelentkeznie arra a Sprinklerre, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)