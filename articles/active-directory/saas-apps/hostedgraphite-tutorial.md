---
title: 'Oktatóanyag: Azure Active Directory-integráció üzemeltetett grafittal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és üzemeltetett grafitok között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 786c6309f685af6b8e42108c490ce86c7844bc81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443156"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Oktatóanyag: Azure Active Directory-integráció az üzemeltetett grafittal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a futtatott grafitot Azure Active Directory (Azure AD) használatával.
Az üzemeltetett grafit és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja az üzemeltetett Grafithoz való hozzáférést.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az üzemeltetett Grafitba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció üzemeltetett grafittal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Üzemeltetett grafit egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az üzemeltetett grafit támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A szolgáltatott grafit **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-hosted-graphite-from-the-gallery"></a>Üzemeltetett grafit hozzáadása a katalógusból

Az üzemeltetett grafit Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia az üzemeltetett grafitot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépéseket követve adhat hozzá szolgáltatott grafitot a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **futtatott grafit** kifejezést, válassza az eredmény panel **szolgáltatott grafit** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Tárolt grafit az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a üzemeltetett grafittal egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az üzemeltetett grafitban lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés üzemeltetett grafittal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[szolgáltatott grafit egyszeri bejelentkezésének konfigurálása](#configure-hosted-graphite-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Üzemeltetett grafit-tesztelési felhasználó létrehozása](#create-hosted-graphite-test-user)** – ha a Britta Simon-t olyan üzemeltetett grafitban szeretné tárolni, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés üzemeltetett grafittal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az **üzemeltetett grafit** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.hostedgraphite.com/metadata/<user id>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem valódi értékek. Ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel kell frissíteni. Ezeknek az értékeknek a beszerzéséhez nyissa meg a hozzáférés->SAML-telepítést az alkalmazás oldalán, vagy lépjen kapcsolatba a [tárolt grafit támogatási csapatával](mailto:help@hostedgraphite.com).

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **tárolt grafit beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-hosted-graphite-single-sign-on"></a>Üzemeltetett grafit-Sign-On konfigurálása

1. Jelentkezzen be a futtatott grafit-bérlőre rendszergazdaként.

2. Nyissa meg az **SAML-beállítások lapot** az oldalsávon (**Access-> SAML-beállítás**).

    ![A képernyőfelvételen a kiválasztott SAML-beállításhoz tartozó hozzáférési menü látható.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Erősítse meg, hogy ezek az URL-címek egyeznek a Azure Portal **alapszintű SAML-konfiguráció** szakaszának konfigurálásával.

    ![A képernyőfelvétel alapszintű SAML-konfigurációját mutatja be.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Az  **entitás vagy a kiállító azonosítója** és az **egyszeri bejelentkezés bejelentkezési URL-címe** szövegmezőben illessze be az **Azure ad-azonosító** és a **bejelentkezési url-cím** értékét, amelyet a Azure Portal másolt.

    ![A képernyőképen az Identitáskezelő bejegyzései láthatók.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Válassza az **írásvédett** lehetőséget **alapértelmezett felhasználói szerepkörként**.

    ![A képernyőképen az alapértelmezett felhasználói szerepkör látható, amely csak olvasható.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Nyissa meg az alap-64 kódolású tanúsítványt a Jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    ![A képernyőképen az X dot 509 tanúsítvány látható.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Kattintson a **Save (Mentés** ) gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az üzemeltetett grafit elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **szolgáltatott grafit** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **szolgáltatott grafit** elemet.

    ![Az alkalmazások listájában az üzemeltetett grafit hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-hosted-graphite-test-user"></a>Üzemeltetett grafit tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a futtatott grafitban. Az üzemeltetett grafit támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az üzemeltetett grafitban, akkor a hitelesítés után létrejön egy újat.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie az üzemeltetett grafit támogatási csapatával <mailto:help@hostedgraphite.com> .

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a futtatott grafit csempére kattint, automatikusan be kell jelentkeznie az üzemeltetett Grafitba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)