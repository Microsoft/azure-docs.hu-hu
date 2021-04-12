---
title: 'Oktatóanyag: Azure Active Directory-integráció a Mozy Enterprise-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Mozy Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: dfeb99fdbd5cf2b0784982b385d46c3a16901ec8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567338"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a Mozy Enterprise-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mozy Enterprise-t Azure Active Directory (Azure AD) használatával.
A Mozy Enterprise és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben ellenőrizheti, hogy ki férhet hozzá a Mozy Enterprise-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Mozy Enterprise-ba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Mozy Enterprise-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Mozy Enterprise egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Mozy Enterprise támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy Enterprise hozzáadása a katalógusból

A Mozy Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mozy Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Mozy Enterprise-t szeretne hozzáadni a gyűjteményhez, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Mozy Enterprise** kifejezést, válassza a **Mozy Enterprise** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Mozy Enterprise az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Mozy Enterprise szolgáltatással konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Mozy Enterprise-beli kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Mozy Enterprise használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Mozy Enterprise egyszeri bejelentkezés konfigurálása](#configure-mozy-enterprise-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Mozy Enterprise test User](#create-mozy-enterprise-test-user)** -to have a Britta Simon in Mozy Enterprise, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Mozy Enterprise-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Mozy Enterprise** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Mozy vállalati tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot a [Mozy Enterprise ügyfél-támogatási csapatával](https://www.safenames.net/about-us/contact-us) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Mozy Enterprise beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-mozy-enterprise-single-sign-on&quot;></a>A Mozy Enterprise Single Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mozy Enterprise vállalati webhelyre rendszergazdaként.

2. A **konfiguráció** szakaszban kattintson a **hitelesítési házirend** elemre.
   
    ![A képernyőképen a konfiguráció alapján kiválasztott hitelesítési házirend látható.](./media/mozy-enterprise-tutorial/ic777314.png &quot;Hitelesítési házirend")

3. A **hitelesítési házirend** szakaszban hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: a hitelesítési házirend szakasza, ahol megadhatja a leírt értékeket.](./media/mozy-enterprise-tutorial/ic777315.png "Hitelesítési házirend")
   
    a. Válassza a **Címtárszolgáltatások** **szolgáltatóként** lehetőséget.
   
    b. Válassza **az LDAP-leküldés használata** lehetőséget.
   
    c. Kattintson az **SAML-hitelesítés** fülre.
   
    d. Illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portal a **hitelesítési URL** szövegmezőbe másolt.
   
    e. Illessze be az **Azure ad-azonosítót**, amelyet a Azure Portal az SAML- **végpont** szövegmezőbe másolt.
   
    f. Nyissa meg a letöltött Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmat, majd illessze be a teljes tanúsítványt az **SAML-tanúsítvány** szövegmezőbe.
   
    : Jelölje be az **egyszeri bejelentkezés engedélyezése rendszergazdák számára a hálózati hitelesítő adatokkal való bejelentkezéshez** jelölőnégyzetet.
   
    h. Kattintson a **Save Changes** (Módosítások mentése) gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Mozy Enterprise-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Mozy Enterprise** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Mozy Enterprise** elemet.

    ![A Mozy Enterprise hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-mozy-enterprise-test-user&quot;></a>Mozy Enterprise test User létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezzenek a Mozy Enterprise-ba, a Mozy vállalatnak kell kiépíteni őket. A Mozy Enterprise esetében a kiépítés manuális feladat.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Mozy Enterprise felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

**Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Mozy Enterprise** -bérlőbe.

2. Kattintson a **felhasználók**, majd az **új felhasználó hozzáadása** lehetőségre.
   
    ![Felhasználók](./media/mozy-enterprise-tutorial/ic777317.png &quot;Felhasználók")
   
    >[!NOTE]
    >Az **új felhasználó hozzáadása** lehetőség csak akkor jelenik meg, ha a (z) **Mozy** van kiválasztva szolgáltatóként a **hitelesítési házirendben**. Ha az SAML-hitelesítés konfigurálva van, akkor a rendszer az egyszeri bejelentkezéssel automatikusan hozzáadja a felhasználókat az első bejelentkezéshez.
    
3. Az új felhasználó párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználók hozzáadása](./media/mozy-enterprise-tutorial/ic777318.png "Felhasználók hozzáadása")
   
    a. A **válasszon csoportot** listából válasszon ki egy csoportot.
   
    b. A **milyen típusú felhasználók** listából válassza ki a kívánt típust.
   
    c. A **Felhasználónév** szövegmezőbe írja be az Azure ad-felhasználó nevét.
   
    d. Az **e-mail** szövegmezőbe írja be az Azure ad-felhasználó e-mail-címét.
   
    e. Válassza a **felhasználói utasítás küldése e-mailt**.
   
    f. Kattintson a **felhasználó (k) hozzáadása** lehetőségre.

     >[!NOTE]
     > A felhasználó létrehozása után a rendszer elküld egy e-mailt az Azure AD-felhasználónak, amely hivatkozást tartalmaz a fiók megerősítéséhez, mielőtt az aktívvá válna.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Mozy Enterprise csempére kattint, automatikusan be kell jelentkeznie a Mozy Enterprise-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)