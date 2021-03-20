---
title: 'Oktatóanyag: Azure Active Directory integráció a beépített SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a alapszintű egyszeri bejelentkezés között.
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
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515287"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Azure Active Directory integráció a beépített SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) használatával a Core SSO-t.
A beépített egyszeri bejelentkezés az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a alapszintű egyszeri bejelentkezéshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az egyszeri bejelentkezésre (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció alapszintű egyszeri bejelentkezéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Egymagos egyszeri bejelentkezéses egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Core SSO támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-procore-sso-from-the-gallery"></a>A Core SSO hozzáadása a katalógusból

A beépített egyszeri bejelentkezés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy alapszintű egyszeri bejelentkezést a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhatja hozzá a alapszintű egyszeri bejelentkezést a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **alapszintű egyszeri bejelentkezés**, válassza a **alapszintű egyszeri bejelentkezés** az eredmény panelről elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Alapszintű SSO az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés a következő építőelemeket kell végrehajtania és tesztelni:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[alapszintű SSO egyszeri bejelentkezésének konfigurálása](#configure-procore-sso-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy alapszintű SSO-tesztet használó felhasználót](#create-procore-sso-test-user)** – hogy rendelkezzen a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó, a Core SSO-ben.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezéses egyszeri bejelentkezéssel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **ALAPszintű SSO** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Alapszintű SSO-tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **alapszintű egyszeri bejelentkezés beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-procore-sso-single-sign-on"></a>Alapszintű egyszeri bejelentkezés egyszeri Sign-On konfigurálása

1. Ha az egyszeri bejelentkezést **az egyszeri bejelentkezési oldalon szeretné** konfigurálni, jelentkezzen be a főkiszolgálói webhelyre rendszergazdaként.

2. Az eszközkészlet legördülő menüben kattintson a **rendszergazda** elemre az SSO-beállítások lap megnyitásához.

    ![Képernyőfelvétel: a vállalati webhely, amelyen a címtár van kiválasztva.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Illessze be a mezőkben szereplő értékeket az alább leírtak szerint:

    ![Képernyőfelvétel: a személy hozzáadása párbeszédpanel.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. Az **egyszeri bejelentkezés kiállítójának URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. Az **SAML-bejelentkezési cél URL-címe** mezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    c. Most nyissa meg az **összevonási metaadatok fent letöltött fájlját** a Azure Portalból, és másolja a tanúsítványt a **x509** nevű címkébe. Illessze be a másolt értéket az **egyszeri bejelentkezési x509-tanúsítvány** mezőbe.

4. Kattintson a **Save Changes** gombra.

5. Ezen beállítások után el kell küldenie a **tartománynevet** (például **contoso.com**), amelyen keresztül bejelentkezik a Core [támogatási csapatba](https://support.procore.com/) , és aktiválnia kell az adott tartomány összevont egyszeri bejelentkezését.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a alapszintű egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **alapszintű egyszeri bejelentkezés** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **alapszintű egyszeri bejelentkezés** lehetőséget.

    ![Az alkalmazások listájában található alapszintű egyszeri bejelentkezés hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-procore-sso-test-user"></a>Alapszintű SSO-teszt felhasználó létrehozása

Az alábbi lépések végrehajtásával hozzon létre egy alapszintű, egyszeri bejelentkezést használó felhasználói tesztet.

1. Jelentkezzen be a alapszintű vállalati webhelyre rendszergazdaként.    

2. Az eszközkészlet legördülő menüjében kattintson a **könyvtár** elemre a vállalati könyvtár lap megnyitásához.

    ![Képernyőfelvétel: a vállalat webhelye, amely az eszközkészletből kiválasztott könyvtárat tartalmazza.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kattintson a **személy hozzáadása** lehetőségre az űrlap megnyitásához, és adja meg a következő beállítások végrehajtását –

    ![Képernyőfelvétel: az a személy hozzáadása a Boylan-konstrukcióhoz, ahol megadhatja a felhasználói adatokat.](./media/procoresso-tutorial/Procore_user_add.png)

    a. Az **Utónév** szövegmezőbe írja be a felhasználó vezetéknevét (például **Britta**).

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például: BrittaSimon@contoso.com .

    d. Válasszon **engedélyt** sablonként az **alkalmazási engedély sablonnal később**.

    e. Kattintson a **Létrehozás** lehetőségre.

4. Keresse meg és frissítse az újonnan hozzáadott partner adatait.

    ![A képernyőképen egy szerkesztési oldal látható, ahol ellenőrizheti a felhasználói beállításokat.](./media/procoresso-tutorial/Procore_user_check.png)

5. A felhasználó regisztrációjának befejezéséhez kattintson a **Mentés gombra, és küldje el a meghívót** (ha meg kell adni az e-mail meghívását), vagy **mentenie** kell a (Save közvetlenül)
    
    ![Képernyőfelvétel: az aktuális Project-beállítások, ahol a meghívót mentheti és küldheti el.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a alapszintű egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie a alapszintű SSO-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)