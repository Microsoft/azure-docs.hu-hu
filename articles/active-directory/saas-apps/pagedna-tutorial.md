---
title: 'Oktatóanyag: Azure Active Directory integráció a PageDNA-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és PageDNA között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993924"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Oktatóanyag: Azure Active Directory integráció a PageDNA

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PageDNA a Azure Active Directory (Azure AD) szolgáltatással.

A PageDNA és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a PageDNA.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a PageDNA (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való szolgáltatott szoftverek (SaaS) alkalmazással kapcsolatos részletekért tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció PageDNA való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Egyszeri bejelentkezést használó PageDNA-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az PageDNA-t az Azure AD-vel.

A PageDNA a következő funkciókat támogatja:

* Az SP által kezdeményezett egyszeri bejelentkezés (SSO).

* Igény szerinti felhasználó üzembe helyezése.

## <a name="add-pagedna-from-the-azure-marketplace"></a>PageDNA hozzáadása az Azure Marketplace-ről

A PageDNA Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PageDNA az Azure Marketplace-ből a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **+ új alkalmazás** elemet a panel tetején.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **PageDNA** kifejezést. A keresési eredmények között válassza a **PageDNA** lehetőséget, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![PageDNA az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az PageDNA-mel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a PageDNA-ben.

Az Azure AD egyszeri bejelentkezés PageDNA való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on)** engedélyezheti a felhasználók számára a funkció használatát.
1. **[Konfigurálja az PageDNA egyszeri bejelentkezést](#configure-pagedna-single-sign-on)** az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
1. **[Hozzon létre egy PageDNA-teszt felhasználót](#create-a-pagedna-test-user)** , hogy a Britta Simon nevű felhasználó PageDNA, aki a Britta Simon nevű Azure ad-felhasználóhoz van társítva.
1. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés PageDNA való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PageDNA** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépéseket:

    ![PageDNA tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    1. A **bejelentkezési URL** -cím mezőben adjon meg egy URL-címet a következő minták egyikének használatával:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. Az **azonosító (entitás azonosítója)** mezőben adja meg az URL-címet a következő minták egyikének használatával:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez forduljon a [PageDNA támogatási csapatához](mailto:success@pagedna.com). A Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintázatokat is megtekintheti.

1. Az **egyszeri Sign-On az SAML-panellel beállításnál** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (RAW)** letöltéséhez a megadott beállítások közül, és mentse a számítógépre.

    ![A tanúsítvány (RAW) letöltési lehetősége](common/certificateraw.png)

1. A **PageDNA beállítása** szakaszban másolja ki a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezési URL-cím**
   * **Azure AD-azonosító**
   * **Kijelentkezési URL-cím**

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a PageDNA oldalon, küldje el a letöltött tanúsítványt (RAW) és a megfelelő másolt URL-címeket a Azure Portal a [PageDNA támogatási csapatához](mailto:success@pagedna.com). A PageDNA-csoport gondoskodik arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**    >  **felhasználók**  >  **minden felhasználó** lehetőséget.

    ![A felhasználók és a "minden felhasználó" lehetőség](common/users.png)

1. A képernyő felső részén válassza az **+ új felhasználó** lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    ![A felhasználó panel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BrittaSimon \@ \<yourcompanydomain> . \<extension>**. Például **BrittaSimon \@ contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure egyszeri bejelentkezés használatára engedélyezi a felhasználó számára, hogy Britta hozzáférést biztosít a PageDNA.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **PageDNA** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **PageDNA** lehetőséget.

    ![PageDNA az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktábla **kezelés** területén válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a **felhasználók** listában, majd válassza a **kiválasztás** elemet a panel alján.

1. Ha az SAML-állításban a szerepkör értékét várja, akkor a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. A panel alján válassza a **kiválasztás** lehetőséget.

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-pagedna-test-user"></a>PageDNA-teszt felhasználó létrehozása

A rendszer most létrehoz egy Britta Simon nevű felhasználót a PageDNA-ben. A felhasználó létrehozásához semmit nem kell tennie. A PageDNA támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ha a Britta Simon nevű felhasználó még nem létezik a PageDNA-ben, akkor a hitelesítés után létrejön egy újat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a **PageDNA** lehetőséget a saját alkalmazások portálján, automatikusan be kell jelentkeznie a PageDNA-előfizetésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](./tutorial-list.md)

* [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../manage-apps/what-is-single-sign-on.md)

* [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)