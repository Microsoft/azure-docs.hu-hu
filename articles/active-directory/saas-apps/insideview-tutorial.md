---
title: 'Oktatóanyag: Azure Active Directory integráció a InsideView-szel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és InsideView között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460104"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Oktatóanyag: Azure Active Directory integráció a InsideView

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a InsideView a Azure Active Directory (Azure AD) szolgáltatással.
Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a InsideView.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a InsideView (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció InsideView való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést engedélyező InsideView-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A InsideView támogatja a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.

## <a name="add-insideview-from-the-gallery"></a>InsideView hozzáadása a gyűjteményből

A InsideView Azure AD-be való integrálásának beállításához hozzá kell adnia a InsideView a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**  >  **minden alkalmazás**:

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **InsideView** kifejezést. A keresési eredmények között válassza a **InsideView** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

    ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a InsideView használatával egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a InsideView-ben.

Az Azure AD egyszeri bejelentkezés InsideView való konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja az InsideView egyszeri bejelentkezést](#configure-insideview-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy InsideView-teszt felhasználót](#create-an-insideview-test-user)** , amely a felhasználó Azure ad-képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Az Azure AD egyszeri bejelentkezés InsideView való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)InsideView alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/idp-reply.png)

    A **Válasz URL-címe** mezőbe írjon be egy URL-címet ebben a mintában:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Ez az érték helyőrző. A tényleges válasz URL-címét kell használnia. Az érték beszerzéséhez lépjen kapcsolatba a [InsideView támogatási csapatával](mailto:support@insideview.com) . A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a **tanúsítvány (RAW)** melletti **letöltési** hivatkozást a követelmények alapján, és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **InsideView beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Azure ad-azonosító**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-insideview-single-sign-on"></a>InsideView egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a InsideView vállalati webhelyre rendszergazdaként.

1. Az ablak **tetején válassza a** felügyelet, **SingleSignon beállítások**, majd az **SAML hozzáadása** elemet.
   
   ![SAML egyszeri bejelentkezés beállításai](./media/insideview-tutorial/ic794135.png "SAML egyszeri bejelentkezés beállításai")

1. Az **Új SAML hozzáadása** szakaszban hajtsa végre a következő lépéseket.

    ![Új SAML-szakasz hozzáadása](./media/insideview-tutorial/ic794136.png "Új SAML-szakasz hozzáadása")

    1. Az **STS neve** mezőbe írja be a konfiguráció nevét.

    1. A **SamlP/ws-fed nem kért végpont** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL-** értéket.

    1. Nyissa meg a Azure Portal letöltött nyers tanúsítványt. Másolja a tanúsítvány tartalmát a vágólapra, majd illessze be a tartalmat az **STS-tanúsítvány** mezőbe.

    1. A **CRM felhasználói azonosító leképezése** mezőben adja meg a értéket **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. A **CRM E-mail leképezése** mezőben adja meg a értéket **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. A **CRM utónév leképezése** mezőben adja meg a értéket **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    1. A **CRM-LastName leképezése** mezőbe írja be a értéket **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .  

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** lehetőséget, majd válassza a **minden felhasználó** lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza az **új felhasználó** lehetőséget az ablak tetején:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Például: BrittaSimon@contoso.com .)

    1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben található értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a InsideView.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **InsideView** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **InsideView** lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listában, majd kattintson az ablak alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson az ablak alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-an-insideview-test-user"></a>InsideView-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a InsideView, hozzá kell adnia őket a InsideView-hez. Ezeket manuálisan kell felvennie.

Ha felhasználókat vagy névjegyeket szeretne létrehozni a InsideView-ben, forduljon a [InsideView támogatási csapatához](mailto:support@insideview.com).

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a InsideView által biztosított felhasználói fiók-létrehozási eszközt vagy API-t használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Amikor kiválasztja a InsideView csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a InsideView-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)