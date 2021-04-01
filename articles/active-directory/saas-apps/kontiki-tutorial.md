---
title: 'Oktatóanyag: Azure Active Directory integráció a Kontiki-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Kontiki között.
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
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95019840"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Oktatóanyag: Azure Active Directory integráció a Kontiki

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kontiki a Azure Active Directory (Azure AD) szolgáltatással.

A Kontiki és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Kontiki.
* A felhasználók automatikusan bejelentkezhetnek a Kontiki az Azure AD-fiókjával (egyszeri bejelentkezéssel).
* A fiókokat egy központi helyen, a Azure Portal is kezelheti.

További információ az Azure AD-vel való szolgáltatott szoftveres (SaaS) alkalmazások integrálásáról: [egyszeri bejelentkezés a Azure Active Directory lévő alkalmazásokba](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Kontiki való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.
* Egyszeri bejelentkezést használó Kontiki-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az Kontiki-t az Azure AD-vel.

A Kontiki a következő funkciókat támogatja:

* **Az SP által kezdeményezett egyszeri bejelentkezés**
* **Igény szerinti felhasználó üzembe helyezése**

## <a name="add-kontiki-in-the-azure-portal"></a>Kontiki hozzáadása a Azure Portal

A Kontiki és az Azure AD integrálásához hozzá kell adnia a Kontiki a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **Kontiki** kifejezést. A keresési eredmények között válassza a **Kontiki** lehetőséget, majd kattintson a **Hozzáadás** gombra.

    ![Kontiki az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Kontiki-mel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Kontiki-ben.

Az Azure AD egyszeri bejelentkezés Kontiki való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a funkció használatát. |
| **[Kontiki egyszeri bejelentkezés konfigurálása](#configure-kontiki-single-sign-on)** | Az egyszeri bejelentkezési beállításokat konfigurálja az alkalmazásban. |
| **[Azure AD-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** | Lehetővé teszi a Britta Simon számára az Azure AD egyszeri bejelentkezés használatát. |
| **[Kontiki-teszt felhasználó létrehozása](#create-a-kontiki-test-user)** | Létrehoz egy, a felhasználó Azure AD-képviseletéhez kapcsolódó Britta Simon-t a Kontiki-ben. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja a Azure Portal Kontiki.

1. Az [Azure Portal](https://portal.azure.com/) **Kontiki** alkalmazás-integráció paneljén válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. **Az egyszeri bejelentkezés** engedélyezéséhez válassza az **SAML** vagy az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezéshez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** panelen válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** ablaktábla **bejelentkezési URL-címe** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    > [!NOTE]
    > A megfelelő érték beszerzéséhez forduljon a Kontiki ügyfélszolgálati [csapatához](https://kollective.com/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása az SAML** -panellel panelen az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** az **összevonási metaadatok XML-** je mellett lehetőséget. A követelmények alapján válasszon egy letöltési lehetőséget. Mentse a tanúsítványt a számítógépére.

    ![Az összevonási metaadatok XML-tanúsítványának letöltési lehetősége](common/metadataxml.png)

1. A **Kontiki beállítása** szakaszban másolja a következő URL-címeket a követelmények alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL-cím

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a Kontiki oldalon, küldje el a letöltött összevonási metaadatok XML-fájlját és a Azure Portalból a [Kontiki-támogatási csapatba](https://kollective.com/support/)másolt releváns URL-címeket. A Kontiki-támogatási csapat az Ön által küldött adatokat az SAML egyszeri bejelentkezési kapcsolatok mindkét oldalon való megfelelő beállításával biztosítja.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.

    ![A felhasználók és az összes felhasználó lehetőség](common/users.png)

1. Válassza az **új felhasználó** lehetőséget.

    ![Az új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **brittasimon \@ \<your-company-domain> . \<extension>**. Például **brittasimon \@ contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

    ![A felhasználó panel](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban Simon Britta-hozzáférést biztosít a Kontiki, így az Azure egyszeri bejelentkezést is használhatja.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **Kontiki** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **Kontiki** lehetőséget.

    ![Kontiki az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-állításban a szerepkör értékét várja, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Válassza a **Kiválasztás** lehetőséget

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-kontiki-test-user"></a>Kontiki-teszt felhasználó létrehozása

Nincs olyan műveleti tétel, amellyel konfigurálhatja a felhasználók üzembe helyezését a Kontiki-ben. Amikor egy hozzárendelt felhasználó megpróbál bejelentkezni a Kontiki a saját alkalmazások portál használatával, a Kontiki ellenőrzi, hogy a felhasználó létezik-e. Ha nem található felhasználói fiók, a Kontiki automatikusan létrehozza a felhasználói fiókot.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Miután beállította az egyszeri bejelentkezést, a **Kontiki** kiválasztása után a saját alkalmazások portálon automatikusan bejelentkezik a Kontiki. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](./tutorial-list.md)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../manage-apps/what-is-single-sign-on.md)
- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)