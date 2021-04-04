---
title: 'Oktatóanyag: Azure Active Directory integráció a SkyDesk e-mailekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a SkyDesk e-mailek között.
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
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516028"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Oktatóanyag: Azure Active Directory integráció a SkyDesk e-mail-címmel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SkyDesk e-maileket Azure Active Directory (Azure AD) használatával.
A SkyDesk e-mailek Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a SkyDesk e-mailekhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az e-mailek (egyszeri bejelentkezés) SkyDesk az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SkyDesk e-mailekkel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SkyDesk E-mail egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A SkyDesk E-mail támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk-e-mailek hozzáadása a katalógusból

A SkyDesk e-mailek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SkyDesk e-maileket a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha SkyDesk-e-mailt szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SkyDesk e-mailt**, válassza a **SkyDesk e-mailt** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SkyDesk e-mailek az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SkyDesk e-mailben konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a SkyDesk e-mailben a kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az SkyDesk e-mailben való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[SkyDesk e-mailes egyszeri bejelentkezés konfigurálása](#configure-skydesk-email-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SkyDesk-alapú e-mail-tesztelési felhasználó létrehozása](#create-skydesk-email-test-user)** – a SkyDesk e-mail-címével, amely a felhasználó Azure ad-Britta van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az SkyDesk e-mailben való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **SkyDesk e-mail alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![SkyDesk e-mail-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [SkyDesk E-mail ügyfélprogram ügyfélszolgálatával](https://www.skydesk.jp/apps/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **SkyDesk E-mail beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-skydesk-email-single-sign-on"></a>SkyDesk e-mailes Sign-On konfigurálása

1. Egy másik böngészőben jelentkezzen be a SkyDesk e-mail-fiókjába rendszergazdaként.

1. A felső menüben kattintson a **telepítés** elemre, majd válassza a **szervezeti** elemet.

    ![Képernyőfelvétel: a beállítás menüből kiválasztva látható.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Kattintson a **tartományok** elemre a bal oldali panelen.

    ![A képernyőképen a Vezérlőpulton kiválasztott tartományok láthatók.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Kattintson a **tartomány hozzáadása** lehetőségre.

    ![Képernyőfelvétel: a kijelölt tartomány hozzáadása.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Adja meg a tartomány nevét, majd ellenőrizze a tartományt.

    ![Képernyőfelvétel: a tartomány hozzáadása lap, amelyen megadhatja a tartományt.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Kattintson az **SAML-hitelesítés** elemre a bal oldali panelen.

    ![A képernyőfelvételen a Vezérlőpulton kiválasztott SAML-hitelesítés látható.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Az **SAML-hitelesítés** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML-hitelesítés részletei párbeszédpanel, ahol megadhatja a leírt értékeket.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Az SAML-alapú hitelesítés használatához ellenőriznie kell a **tartomány** vagy a **portál URL-címének** beállítását. Megadhatja a portál URL-címét az egyedi névvel.

    ![A képernyőképen az U R L portálon látható, ahol megadhatja a nevet.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. A **kijelentkezési** URL szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portalból másolt.

    c. A **jelszó módosítása URL-cím** megadása nem kötelező, ezért hagyja üresen.

    d. Kattintson a **kulcs lekérése fájlból** lehetőségre a letöltött tanúsítvány Azure Portal való kiválasztásához, majd kattintson a **Megnyitás** gombra a tanúsítvány feltöltéséhez.

    e. **Algoritmusként** válassza az **RSA** lehetőséget.

    f. A módosítások mentéséhez kattintson **az OK** gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a SkyDesk e-mailekhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **SkyDesk e-mailt**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **SkyDesk e-mailt**.

    ![Az SkyDesk E-mail hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-skydesk-email-test-user"></a>SkyDesk E-mail tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a SkyDesk e-mailben.

Kattintson a SkyDesk e-mailben a bal oldali panelen a **felhasználói hozzáférés** elemre, majd írja be a felhasználónevét.

![A képernyőfelvételen a Vezérlőpultról kiválasztott felhasználói hozzáférés látható.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Ha tömeges felhasználókat kell létrehoznia, lépjen kapcsolatba a [SkyDesk E-mail ügyfélprogram ügyfélszolgálatával](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SkyDesk E-mail csempére kattint, automatikusan be kell jelentkeznie arra a SkyDesk-e-mailbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)