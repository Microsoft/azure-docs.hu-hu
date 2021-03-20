---
title: 'Oktatóanyag: Azure Active Directory integráció a Brightidea-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Brightidea között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 9967f349011b52a2218681956885c33456ba1d46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672763"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Oktatóanyag: Azure Active Directory integráció a Brightidea

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Brightidea a Azure Active Directory (Azure AD) szolgáltatással.
A Brightidea és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Brightidea.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Brightidea (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Brightidea való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Brightidea egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.


* A Brightidea támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Brightidea **csak időben támogatja a** felhasználók kiépítési folyamatát


## <a name="adding-brightidea-from-the-gallery"></a>Brightidea hozzáadása a gyűjteményből

A Brightidea Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Brightidea a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Brightidea szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Brightidea** kifejezést, válassza a **Brightidea** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Brightidea az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Brightidea-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Brightidea kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Brightidea való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Brightidea egyszeri bejelentkezés konfigurálása](#configure-brightidea-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Brightidea-teszt felhasználót](#create-brightidea-test-user)** – hogy a Brightidea Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Brightidea való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Brightidea** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, és a **identitásszolgáltató** intiated módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan fel lesznek töltve a Brightidea szakasz szövegmezőben:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.brightidea.com`

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Brightidea beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-brightidea-single-sign-on"></a>Brightidea egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Brightidea-be a rendszergazdai hitelesítő adatok használatával.

2. A Brightidea-rendszeren lévő egyszeri bejelentkezés funkció beszerzéséhez navigáljon a **vállalati beállítási**  ->  **hitelesítés lapra**. Itt két allapot láthat: auth Selection & SAML-profilok.

    ![Képernyőfelvétel: a Brightidea webhely, amelyen a hitelesítés lap van kiválasztva.](./media/brightidea-tutorial/configure1.png)

3. Válassza az **Auth kiválasztása** lehetőséget. Alapértelmezés szerint csak két szabványos metódus látható: a Brightidea bejelentkezési & regisztrációja. Az egyszeri bejelentkezési módszer hozzáadásakor megjelenik a listában.

    ![A képernyőképen a Brightidea-hitelesítés lap van kiválasztva.](./media/brightidea-tutorial/configure2.png)

4. Válassza ki az **SAML-profilokat** , és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Brightidea-hitelesítés lap, amely SAML-profilokkal van kiválasztva, és a metaadatok letöltésére és új hozzáadására szolgáló lehetőségeket biztosít.](./media/brightidea-tutorial/configure3.png)

    a. Kattintson a **metaadatok letöltése** elemre, és töltse fel az **alapszintű SAML-konfiguráció** szakaszát a Azure Portal.

    b. Kattintson az **új hozzáadása** gombra az identitás- **szolgáltató beállítás** alatt, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a Brightidea identitás-szolgáltatói beállítás, ahová az adatokat adja meg.](./media/brightidea-tutorial/configure4.png)

   * Adja meg az **SAML-profil nevét** , például: `Azure Ad SSO`

   * A **metaadatok feltöltéséhez** kattintson a fájl kiválasztása lehetőségre, és töltse fel a letöltött metaadat-fájlt a Azure Portal.

     > [!NOTE]
     > A metaadat-fájl feltöltése után a rendszer automatikusan feltölti a többi mezőt az **egyszeri bejelentkezési szolgáltatás, az Identity Provider kiállítója, a nyilvános kulcs feltöltése** .

   * Az **e-mail** szövegmezőbe írja be az értéket a következőképpen: `mail` .

   * A **képernyő neve** szövegmezőbe írja be az értéket `givenName` .

   * Kattintson a **Save Changes** (Módosítások mentése) gombra.  

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .

    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension nevet.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Brightidea hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Brightidea** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Brightidea** lehetőséget.

    ![Az Brightidea hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-brightidea-test-user"></a>Brightidea-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Brightidea-ben. A Brightidea támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Brightidea-ben, a rendszer egy újat hoz létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Brightidea csempére kattint, automatikusan be kell jelentkeznie arra a Brightidea, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)