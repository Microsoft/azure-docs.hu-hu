---
title: 'Oktatóanyag: Azure Active Directory integráció a dokumentációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a dokumentáció között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: ff5da4b87589889172a9518fd67486fe14451630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454647"
---
# <a name="tutorial-azure-active-directory-integration-with-dossier"></a>Oktatóanyag: Azure Active Directory integráció a dokumentációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a dokumentációt Azure Active Directory (Azure AD) használatával.
A dokumentáció az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a dokumentációhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a dokumentációba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció dokumentációval való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Dokumentáció egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A dokumentáció támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-dossier-from-the-gallery"></a>Dokumentáció hozzáadása a katalógusból

A dokumentáció Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia egy dokumentációt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a katalógusból szeretne felvenni egy dokumentációt, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **dokumentáció** kifejezést, válassza a **dokumentáció** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az eredmények listájában szereplő dokumentáció](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a dokumentációban lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a dokumentációval való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[dokumentáció egyszeri bejelentkezésének konfigurálása](#configure-dossier-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy dokumentációs teszt felhasználót](#create-dossier-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-Britta kapcsolódó dokumentációban található Simon-beli partnerrel.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a következő lépésekkel konfigurálható a dokumentációval:

1. A [Azure Portal](https://portal.azure.com/)a **dokumentáció** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A dokumentációs tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    ```http
    https://<SUBDOMAIN>.dossiersystems.com/azuresso/account/SignIn
    https://dossier.<CLIENTDOMAINNAME>/azuresso/account/SignIn
    ```

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `Dossier/<CLIENTNAME>`

    > [!NOTE]
    > Az azonosító értékének formátuma `Dossier/<CLIENTNAME>` vagy bármely felhasználó személyre szabott értéke lehet.

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    https://<SUBDOMAIN>.dossiersystems.com/azuresso
    https://dossier.<CLIENTDOMAINNAME>/azuresso
    ```


    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Ezekhez az értékekhez forduljon az [ügyfél-támogatási csoporthoz](mailto:support@intellimedia.ca) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** a megadott beállítások alapján történő másolásához, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

6. A **dokumentáció beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-dossier-single-sign-on"></a>A dokumentáció egyetlen Sign-On konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **dokumentációs** oldalon, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [dokumentációs támogatási csapatnak](mailto:support@intellimedia.ca). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a dokumentációhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **dokumentáció** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **dokumentáció** elemet.

    ![A dokumentáció hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-dossier-test-user"></a>Dokumentációs teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a dokumentációban. A [dokumentációs](mailto:support@intellimedia.ca) platformmal felveheti a felhasználókat a dokumentációs platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a dokumentáció csempére kattint, automatikusan be kell jelentkeznie arra a dokumentációba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)