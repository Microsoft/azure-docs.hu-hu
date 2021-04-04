---
title: 'Oktatóanyag: Azure Active Directory integráció az észlelési Egyesült Államokokkal (nem UltiPro) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és észlelési Egyesült Államok (nem UltiPro) között.
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
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515525"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Oktatóanyag: Azure Active Directory integráció az észlelési Egyesült Államok (nem UltiPro)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az észlelési Egyesült Államok (nem UltiPro) a Azure Active Directory (Azure AD) szolgáltatással.
Az észlelési Egyesült Államok (nem UltiPro) integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az észlelési Egyesült Államokhoz (nem UltiPro).
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Egyesült Államok (nem UltiPro) (egyszeri bejelentkezés) Azure AD-fiókjával való észlelésére.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az észlelési Egyesült Államokával (nem UltiPro) való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Észlelési Egyesült Államok (nem UltiPro) egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Észlelési Egyesült Államok (nem UltiPro) támogatja a **identitásszolgáltató** kezdeményezett SSO-t

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Észlelési Egyesült Államok hozzáadása (nem UltiPro) a katalógusból

Az észlelési Egyesült Államok (nem UltiPro) Azure AD-ba való integrálásának konfigurálásához fel kell vennie az észlelési Egyesült Államok (nem UltiPro) a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha az észlelési Egyesült Államok (nem UltiPro) szeretné hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **észlelési Egyesült Államok (nem UltiPro)**, válassza az **észlelés Egyesült Államok (nem UltiPro)** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Észlelés Egyesült Államok (nem UltiPro) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést úgy konfigurálja és teszteli, hogy az észlelési Egyesült Államok (nem UltiPro) egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az észlelési Egyesült Államok (nem UltiPro) közötti kapcsolati kapcsolat létrehozása szükséges.

Az Azure AD egyszeri bejelentkezés és az észlelési Egyesült Államok (nem UltiPro) konfigurálásához és teszteléséhez a következő építőelemeket kell elvégeznie:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[észlelési Egyesült Államok konfigurálása (nem UltiPro) egyszeri bejelentkezés](#configure-perception-united-states-non-ultipro-single-sign-on)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy észlelési Egyesült Államok (nem UltiPro) tesztelési felhasználót](#create-perception-united-states-non-ultipro-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, az észlelési Egyesült Államok (nem UltiPro).
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés és az észlelési Egyesült Államok (nem UltiPro) konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **észlelési Egyesült Államok (nem UltiPro)** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![Észlelési Egyesült Államok (nem UltiPro) tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `https://perception.kanjoya.com/sp`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Az **észlelési Egyesült Államok (nem UltiPro)** alkalmazáshoz az **Azure ad-azonosító** értékének <entity_id>nek kell lennie, amelyet a **set up Perception Egyesült Államok (nem UltiPro)** szakaszból kap, amely URI-kódolású lesz. Az URI-kódolású érték beszerzéséhez használja a következő hivatkozást: **http://www.url-encode-decode.com/** .

    d. Az URI-kódolású érték beolvasása után a **Válasz URL-címével** kombinálja azt a lent említett módon:

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Illessze be a fenti értéket a **Válasz URL-címe** szövegmezőbe.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **észlelési Egyesült Államok beállítása (nem UltiPro)** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Észlelési Egyesült Államok konfigurálása (nem UltiPro) egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be az észlelési Egyesült Államok (nem UltiPro) vállalati webhelyre rendszergazdaként.

2. A fő eszköztáron kattintson a **Fiókbeállítások** lehetőségre.

    ![A fő eszköztáron kiválasztott "Fiókbeállítások" megjelenítő képernyőkép.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. A **Fiókbeállítások** oldalon hajtsa végre a következő lépéseket:

    ![Észlelési Egyesült Államok (nem UltiPro) felhasználó](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. A **Vállalat neve** szövegmezőbe írja be a **vállalat** nevét.
    
    b. A **fiók neve** szövegmezőbe írja be a **fiók** nevét.

    c. Az **alapértelmezett Reply-To e-mail** szövegmezőbe írja be az érvényes **e-mailt**.

    d. Válassza ki az **SSO-identitás szolgáltatóját** **SAML 2,0**-ként.

4. Az **egyszeri bejelentkezés konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![Észlelési Egyesült Államok (nem UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Válassza ki az **SAML-NameID típust** **e-mailben**.

    b. Az **SSO-konfiguráció neve** szövegmezőbe írja be a **konfiguráció** nevét.
    
    c. Az **identitás-szolgáltató neve** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt. 

    d. Az **SAML-tartomány szövegmezőbe** írja be a tartományt, például: @contoso.com .

    e. Kattintson a **feltöltés** gombra a **metaadatok XML-** fájljának feltöltéséhez.

    f. Kattintson a **Frissítés** parancsra.

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

Ebben a szakaszban az Azure-beli egyszeri bejelentkezést az észlelési Egyesült Államokhoz (nem UltiPro) való hozzáférés biztosításával engedélyezi a Britta Simon számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **észlelési Egyesült Államok (nem UltiPro)** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **észlelés Egyesült Államok (nem UltiPro)** lehetőséget.

    ![Az észlelési Egyesült Államok (nem UltiPro) hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Észlelési Egyesült Államok (nem UltiPro) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az észlelési Egyesült Államok (nem UltiPro). Az észlelési [Egyesült Államok (nem UltiPro) támogatási csapatával](https://www.ultimatesoftware.com/Contact/ContactUs) felveheti a felhasználókat az észlelési Egyesült Államok (nem UltiPro) platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a észlelési Egyesült Államok (nem UltiPro) csempére kattint, automatikusan be kell jelentkeznie az észlelési Egyesült Államok (nem UltiPro), amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)