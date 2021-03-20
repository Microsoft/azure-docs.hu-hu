---
title: 'Oktatóanyag: Azure Active Directory-integráció a LockPath-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a LockPath-jelzőfény között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459084"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Oktatóanyag: Azure Active Directory integráció az LockPath-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LockPath-fényt Azure Active Directory (Azure AD) használatával.
A LockPath-szolgáltatás Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a LockPath-hez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával a LockPath (az egyszeri bejelentkezéshez).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció LockPath-vel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* LockPath-alapú, egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A LockPath-jelzõfény támogatja az **SP** által kezdeményezett egyszeri bejelentkezést
* A LockPath-világítás **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-lockpath-keylight-from-the-gallery"></a>LockPath-jelzőfény hozzáadása a gyűjteményből

Ha az LockPath-t az Azure AD-be szeretné konfigurálni, akkor a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a LockPath-fényt.

**A következő lépések végrehajtásával adhat hozzá LockPath-fényt a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LockPath-jelzõfény** kifejezést, válassza a **LockPath** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![LockPath-jelzőfény az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a LockPath-fénnyel a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az LockPath-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés LockPath-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[LockPath-alapú egyszerű bejelentkezés konfigurálása](#configure-lockpath-keylight-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításának konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy LockPath](#create-lockpath-keylight-test-user)** , amely a felhasználó Azure ad-Britta kapcsolódik, és a LockPath.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés LockPath-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **LockPath** -alapú alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![LockPath és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.keylightgrc.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.keylightgrc.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a LockPath-hez tartozó [ügyfél-támogatási csoporthoz](https://www.lockpath.com/contact/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (RAW)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **LockPath-világítás beállítása** szakaszban adja meg a megfelelő URL-címeket (ek) a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-lockpath-keylight-single-sign-on"></a>LockPath egyszerű Sign-On konfigurálása

1. Ha engedélyezni szeretné az egyszeri bejelentkezést az LockPath-ben, hajtsa végre a következő lépéseket:

    a. Jelentkezzen be rendszergazdaként a LockPath.

    b. A felső menüben kattintson a **személy** elemre, majd válassza a **minitelepítés beállítása** lehetőséget.

    ![Képernyőfelvétel: a "személy" ikon kiválasztva és "a" hangjelzés beállítása "beállítás a legördülő menüből.](./media/keylight-tutorial/401.png)

    c. A bal oldali TreeView vezérlőben kattintson az **SAML** elemre.

    ![Képernyőkép, amely a fanézetben kiválasztott "S A M L" jelölést jeleníti meg.](./media/keylight-tutorial/402.png)

    d. A **SAML-beállítások** párbeszédpanelen kattintson a **Szerkesztés** elemre.

    ![A "szerkesztés" gomb kiválasztásával megjelenő "S M L beállítások" ablak](./media/keylight-tutorial/404.png)

1. Az **SAML-beállítások szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása](./media/keylight-tutorial/405.png)

    a. **SAML-hitelesítés** beállítása **aktívra**.

    b. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Az **Identitáskezelő kijelentkezési URL-címe** szövegmezőbe illessze be azt a **KIjelentkezési URL** -értéket, amelyet a Azure Portal másolt.

    d. Kattintson a **fájl kiválasztása** lehetőségre a letöltött LockPath-tanúsítvány kiválasztásához, majd kattintson a **Megnyitás** gombra a tanúsítvány feltöltéséhez.

    e. Az **SAML felhasználói azonosító helyének** beállítása a **tárgyi utasítás NameIdentifier elemére**.

    f. Adja meg a **Fényszolgáltatási szolgáltatót** a következő minta használatával: `https://<CompanyName>.keylightgrc.com` .

    : Állítsa be az **automatikus kiépítés a felhasználók** számára **aktív** lehetőséget.

    h. Az **automatikus kiépítés fiók típusának** beállítása a **teljes felhasználó** számára.

    i. Állítsa be az **automatikus kiépítés biztonsági szerepkört**, és válassza **az SAML-t használó általános jogú felhasználó** lehetőséget.

    j. Állítsa be az **automatikus kiépítés biztonsági konfigurációját**, majd válassza a **normál felhasználói konfiguráció** lehetőséget.

    k. Az **e-mail attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. Az **Utónév attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. A **vezetéknév attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az LockPath-hez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **LockPath-jelzõfény** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **LockPath-világítás** elemet.

    ![Az LockPath-jelzőfény hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-lockpath-keylight-test-user"></a>LockPath-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a LockPath-ben. A LockPath-alapú rendszerindítási funkció támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a LockPath-ben, akkor a hitelesítés után létrejön egy újat. Ha manuálisan kell létrehoznia egy felhasználót, forduljon a LockPath-alapú [ügyfélszolgálathoz](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a LockPath-világítás csempére kattint, automatikusan be kell jelentkeznie a LockPath, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)