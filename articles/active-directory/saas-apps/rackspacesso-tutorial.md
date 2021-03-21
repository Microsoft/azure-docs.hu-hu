---
title: 'Oktatóanyag: Azure Active Directory integráció a Rackspace SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Rackspace SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: eed0ea075c5e9eb39810259be2aa6781a59f2f22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511647"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Oktatóanyag: Azure Active Directory integráció a Rackspace SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Rackspace SSO-t Azure Active Directory (Azure AD-val).
A Rackspace SSO Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Rackspace SSO-hoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az egyszeri bejelentkezés (SSO) Rackspace az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Rackspace SSO-vel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Rackspace egyszeri bejelentkezéses egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Rackspace SSO támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-rackspace-sso-from-the-gallery"></a>Rackspace SSO hozzáadása a katalógusból

A Rackspace SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Rackspace SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Rackspace SSO-t szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **RACKSPACE SSO** kifejezést, válassza a **Rackspace SSO** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Rackspace SSO az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Rackspace SSO-val egy **Britta Simon** nevű teszt felhasználó alapján.
Ha egyszeri bejelentkezést használ a Rackspace-ben, a rendszer automatikusan létrehozza a Rackspace-felhasználókat az első alkalommal, amikor bejelentkeznek a Rackspace-portálra. 

Az Azure AD egyszeri bejelentkezés Rackspace SSO-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[RACKSPACE SSO egyszeri bejelentkezés konfigurálása](#configure-rackspace-sso-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[Attribútum-hozzárendelés beállítása a Rackspace Vezérlőpultján](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – Rackspace szerepkörök hozzárendelése az Azure ad-felhasználókhoz.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Rackspace SSO-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Rackspace SSO** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban töltse fel a **szolgáltatói metaadat-fájlt** , amelyet letölt az [URL](https://login.rackspace.com/federate/sp.xml) -címről, és hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![Képernyőfelvétel: alapszintű SAML-konfiguráció a metaadatok feltöltése hivatkozással.](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![Képernyőfelvétel: egy párbeszédpanel, amelyen kiválaszthatja és feltöltheti a fájlokat.](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a szükséges URL-címek automatikus feltöltése automatikusan megtörténik.

    d. A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://login.rackspace.com/federate/`

    ![Rackspace SSO-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)   

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

A rendszer feltölti a fájlt a Rackspace-be, hogy feltöltse a szükséges identitás-összevonási konfigurációs beállításokat.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace egyszeri bejelentkezéses egyszeri Sign-On konfigurálása

Egyszeri bejelentkezés konfigurálása a **RACKSPACE SSO** oldalon:

1. Tekintse meg az [identitás-szolgáltató hozzáadása a Vezérlőpulton](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/) című dokumentációt
1. A következő lépéseket hajtja végre:
    1. Új identitás-szolgáltató létrehozása
    1. Adja meg azt az e-mail-tartományt, amelyet a felhasználók a vállalat azonosítására fognak használni a bejelentkezéskor.
    1. Töltse fel a korábban az Azure Vezérlőpultról letöltött **összevonási metaadatokat tartalmazó XML-fájlt** .

Ez helyesen konfigurálja az Azure-hoz és a Rackspace-hoz való kapcsolódáshoz szükséges alapszintű egyszeri bejelentkezési beállításokat.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Rackspace SSO hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Rackspace egyszeri bejelentkezés** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Rackspace egyszeri bejelentkezés** lehetőséget.

    ![Az Rackspace SSO-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Attribútumok leképezésének beállítása a Rackspace Vezérlőpultján

A Rackspace egy **attribútum-hozzárendelési házirend** használatával rendel hozzá Rackspace-szerepköröket és-csoportokat az egyszeri bejelentkezési felhasználókhoz. Az **attribútum-hozzárendelési házirend** lefordítja az Azure ad SAML-jogcímeket a Rackspace felhasználói konfigurációs mezőibe. További dokumentáció a Rackspace [attribútum-hozzárendelés alapjairól szóló dokumentációban](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)található. Néhány szempont:

* Ha az Azure AD-csoportokkal különböző szintű Rackspace-hozzáféréseket szeretne hozzárendelni, engedélyeznie kell a csoportok jogcímet az Azure **RACKSPACE egyszeri** bejelentkezéses egyszeri bejelentkezés beállításaiban. A rendszer ezután az **attribútum-hozzárendelési házirendet** fogja használni a csoportoknak a kívánt Rackspace-szerepkörökhöz és-csoportokhoz való megfeleltetéséhez:

    ![A csoportok jogcímek beállításai](common/sso-groups-claim.png)

* Alapértelmezés szerint az Azure AD az SAML-jogcímben az Azure AD-csoportok UID-át küldi el, a csoport nevével szemben. Ha azonban a helyszíni Active Directory az Azure AD-be szinkronizálja, lehetősége van a csoportok tényleges nevének elküldésére:

    ![A groups jogcím neve beállítások](common/sso-groups-claims-names.png)

A következő példa **attribútum-hozzárendelési házirend** a következőket mutatja be:
1. A Rackspace-felhasználó nevének beállítása SAML- `user.name` jogcímként. Bármely jogcím használható, de ez a leggyakoribb beállítás a felhasználó e-mail-címét tartalmazó mezőre.
1. A Rackspace szerepköreinek `admin` és `billing:admin` felhasználóinak beállítása egy Azure ad-csoportnak megfelelő csoport neve vagy csoport UID azonosítója alapján. A  `"{0}"` mezőhöz való helyettesítést `roles` használja a rendszer, és a szabály kifejezései eredményei lesznek lecserélve `remote` .
1. Az `"{D}"` *alapértelmezett helyettesítéssel* a Rackspace további SAML-mezőket kérhet le, ha szabványos és jól ismert SAML-jogcímeket keres az SAML-Exchange-ben.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Győződjön meg arról, hogy olyan szövegszerkesztőt használ, amely ellenőrzi a YAML szintaxisát a házirend fájljának szerkesztésekor.

További példákért tekintse meg a Rackspace [attribútum-hozzárendelés alapjairól szóló dokumentációt](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) .

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Rackspace SSO csempére kattint, automatikusan be kell jelentkeznie a Rackspace SSO-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

A **RACKSPACE SSO** egyszeri bejelentkezés beállításaiban a **validate (érvényesítés** ) gombot is használhatja:

   ![SSO-érvényesítés gomb](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)