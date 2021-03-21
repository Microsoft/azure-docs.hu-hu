---
title: 'Oktatóanyag: Azure Active Directory-integráció a dicsőséggel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és dicsőség között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 656c7467507c105f492f266f06c05a9f41e8f76a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458858"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Oktatóanyag: Azure Active Directory-integráció a dicsőséggel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kudost Azure Active Directory (Azure AD) használatával.
A dicsőség az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* A dicsőséghez hozzáférő Azure AD-t is vezérelheti.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a dicsőség (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Dicsőségtel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kudos egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Kudos támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-kudos-from-the-gallery"></a>A dicsőség hozzáadása a katalógusból

A dicsőség az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy dicsőséget a galériából a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhat hozzá dicsőséget a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Kudos** kifejezést, válassza a **dicsőség** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A dicsőség az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést és a Kudost a **Britta Simon** nevű tesztelési felhasználó alapján konfigurálja és teszteli.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Kudos-beli kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Kudos használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Kudos egyszeri bejelentkezés konfigurálása](#configure-kudos-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[Kudos test User létrehozása](#create-kudos-test-user)** – hogy a Britta Simon in Kudos partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Kudos használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Kudos** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A Kudos domain és az URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<company>.kudosnow.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a [dicsőség ügyfél-támogatási csapatához](http://success.kudosnow.com/home) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **dicsőség beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kudos-single-sign-on"></a>A Kudos Single Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a dicsőséges céges webhelyre rendszergazdaként.

1. A felső menüben kattintson a **Beállítások ikonra**.

    ![Beállítások](./media/kudos-tutorial/ic787806.png "Beállítások")

1. Kattintson az **integrációk > SSO** elemre, és hajtsa végre a következő lépéseket:

    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")

    a. A **bejelentkezési URL** szövegmezőben illessze be a  **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    b. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    c. Az **URL-cím szövegmezőbe való kijelentkezéskor** illessze be a  **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    d. A **saját dicsőség URL-címe** szövegmezőbe írja be a cég nevét.

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
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a dicsőséghez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **dicsőség** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **dicsőség** elemet.

    ![A dicsőség hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kudos-test-user"></a>Kudos-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a dicsőségbe, a dicsőségbe kell azokat kiépíteni. A Kudos esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **dicsőséges** céges webhelyre rendszergazdaként.

1. A felső menüben kattintson a **Beállítások ikonra**.

   ![Beállítások](./media/kudos-tutorial/ic787806.png "Beállítások")

1. Kattintson a **felhasználói rendszergazda** elemre.

1. Kattintson a **felhasználók** lapra, majd a **felhasználó hozzáadása** elemre.

   ![Felhasználói rendszergazda](./media/kudos-tutorial/ic787809.png "Felhasználói rendszergazda")

1. A **felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználó hozzáadása](./media/kudos-tutorial/ic787810.png "Felhasználó hozzáadása")

    a. Adja meg egy érvényes Azure Active Directory- **fiók vezetéknevét,** **vezetéknevét**, **e-mail-címét** és egyéb részleteit a kapcsolódó szövegmezőbe.

    b. Kattintson a **felhasználó létrehozása** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a Kudos által biztosított bármely más Kudos felhasználói fiók létrehozása eszközt vagy API-t használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a dicsőség csempére kattint, automatikusan be kell jelentkeznie arra a dicsőségre, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)