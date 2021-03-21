---
title: 'Oktatóanyag: Azure Active Directory integráció az autotask Endpoint Backup szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az automatikus feladat-végpont biztonsági mentése között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/19/2019
ms.author: jeedes
ms.openlocfilehash: 349a77d2f2cd5e1c492de63919d84efb436a51a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673698"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-endpoint-backup"></a>Oktatóanyag: Azure Active Directory integráció az automatikus feladat-végpont biztonsági mentésével

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az automatikus feladat-végpontok biztonsági mentését Azure Active Directory (Azure AD) használatával.
Az automatikus feladat-végpont biztonsági mentésének az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az automatikus feladat-végpont biztonsági mentéséhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az automatikus feladat-végpont biztonsági mentésére (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció automatikus feladat-végponti biztonsági mentéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Automatikus feladat-végponti biztonsági mentés egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az automatikus feladat-végpont biztonsági mentése támogatja a **identitásszolgáltató** KEZDEMÉNYEZett SSO-t

## <a name="adding-autotask-endpoint-backup-from-the-gallery"></a>Automatikus feladat-végpont biztonsági mentésének hozzáadása a gyűjteményből

Az automatikus feladat-végpont biztonsági mentésének az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az automatikus feladat-végpont biztonsági mentését a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az automatikus feladat-végpont biztonsági mentésének a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az automatikus **feladat végpontjának biztonsági mentését**, válassza az automatikus **feladat végpontjának biztonsági mentése** az eredmény panelről lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Az automatikus feladat-végpont biztonsági mentése az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli az automatikus feladat-végpont biztonsági mentésével a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az automatikus feladat-végpont biztonsági mentésében szereplő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés automatikus feladat-végponti biztonsági mentéssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az automatikus **[feladat-végponti biztonsági mentés egyszeri bejelentkezésének beállítása](#configure-autotask-endpoint-backup-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításának konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Automatikus feladat-végpont biztonsági mentési tesztelési felhasználójának létrehozása](#create-autotask-endpoint-backup-test-user)** – a Britta Simon-t tartalmazó, az automatikus feladat-végpont biztonsági másolatában található, a felhasználó Azure ad-beli megjelenítéséhez csatolt ügyfél.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés automatikus feladat-végponti biztonsági mentéssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az automatikus feladat- **végpont biztonsági mentése** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![Automatikus feladat-végpont biztonsági mentési tartománya és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.backup.autotask.net/singlesignon/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.backup.autotask.net/singlesignon/saml/SSO`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek megszerzéséhez lépjen kapcsolatba az automatikus [feladat-végpont biztonsági mentési ügyfelének ügyfélszolgálatával](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az automatikus **feladat-végpont biztonsági mentésének beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-autotask-endpoint-backup-single-sign-on"></a>Az automatikus feladat-végpont biztonsági mentésének konfigurálása Sign-On

Az egyszeri bejelentkezés automatikus **feladat-végponton történő biztonsági mentésének** konfigurálásához el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalról az automatikus [feladat végpontjának biztonsági mentési támogatási csapatához](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson az automatikus feladat-végpont biztonsági mentéséhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az automatikus **feladat végpontjának biztonsági mentése** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az automatikus **feladat végpontjának biztonsági mentése** elemet.

    ![Az automatikus feladat végpontjának biztonsági mentési hivatkozása az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-autotask-endpoint-backup-test-user"></a>Automatikus feladat-végpont biztonsági mentési tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az autotask Endpoint Backup szolgáltatásban. Az automatikus feladat- [végpont biztonsági mentési támogatási csapatának](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) használata a felhasználók hozzáadásához az automatikus feladat-végpont biztonsági mentési platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen rákattint az automatikus feladat-végpont biztonsági mentése csempére, automatikusan be kell jelentkeznie az automatikus feladat-végpont biztonsági másolatából, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)