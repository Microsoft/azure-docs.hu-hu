---
title: 'Oktatóanyag: Azure Active Directory-integráció a Veritas Enterprise Vault.cloud SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Veritas Enterprise Vault.cloud SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5e777a27e793b1f4ef5489248c170824d69d615c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517684"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Oktatóanyag: Azure Active Directory-integráció a Veritas Enterprise Vault.cloud SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Veritas Enterprise Vault.cloud SSO-t Azure Active Directory (Azure AD-val).
A Veritas Enterprise Vault.cloud SSO és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Veritas Enterprise Vault.cloud SSO-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Veritas Enterprise Vault.cloud SSO-ba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Veritas Enterprise Vault.cloud SSO-val való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* A Veritas Enterprise Vault.cloud egyszeri bejelentkezéses egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Veritas Enterprise Vault.cloud SSO támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>A Veritas Enterprise Vault.cloud SSO hozzáadása a katalógusból

A Veritas Enterprise Vault.cloud SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Veritas Enterprise Vault.cloud SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Veritas Enterprise Vault.cloud SSO a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Veritas enterprise Vault.Cloud SSO** kifejezést, válassza a **Veritas Enterprise Vault.Cloud SSO** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Veritas Enterprise Vault.cloud SSO az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Veritas Enterprise Vault.cloud SSO-val egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Veritas Enterprise Vault.cloud SSO-beli kapcsolódó felhasználó közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés a Veritas Enterprise Vault.cloud SSO-val való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Veritas Enterprise Vault.Cloud SSO egyszeri bejelentkezés beállítása](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[Veritas enterprise Vault.Cloud SSO-teszt felhasználójának létrehozása](#create-veritas-enterprise-vaultcloud-sso-test-user)** – a felhasználó Azure ad-Britta kapcsolódó, a veritas Enterprise Vault.Cloud SSO-hoz tartozó "
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Veritas Enterprise Vault.cloud SSO-val szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Veritas Enterprise Vault.Cloud SSO** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A Veritas Enterprise Vault.cloud SSO-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Az **azonosító** mezőben használja az URL-címet az adatközpontként:

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|

    c. A **Válasz URL-címe** szövegmezőben használja az URL-címet az adatközpontként:

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a [Veritas Enterprise Vault.Cloud SSO ügyfél-támogatási csapatához](https://www.veritas.com/support/.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Veritas Enterprise Vault.Cloud SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>A Veritas Enterprise Vault.cloud egyszeri bejelentkezéses egyszeri Sign-On konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést a **Veritas enterprise Vault.Cloud SSO** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket Azure Portalról a [Veritas Enterprise Vault.Cloud SSO támogatási csapatának](https://www.veritas.com/support/.html). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Veritas Enterprise Vault.cloud SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Veritas Enterprise Vault.Cloud SSO** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Veritas Enterprise Vault.Cloud SSO** elemet.

    ![A Veritas Enterprise Vault.cloud SSO hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>A Veritas Enterprise Vault.cloud SSO-teszt felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Veritas Enterprise Vault.cloud SSO-ban. A [Veritas enterprise Vault.Cloud SSO támogatási csapatának](https://www.veritas.com/support/.html) használata a felhasználók hozzáadásához a veritas Enterprise Vault.Cloud SSO platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Veritas Enterprise Vault.cloud SSO csempére kattint, automatikusan be kell jelentkeznie a Veritas vállalati Vault.cloud SSO-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)