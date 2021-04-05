---
title: 'Oktatóanyag: Azure Active Directory integráció a Predictix Pricing szolgáltatással | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Predictix díjszabása között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 801197a171b5be2cfe46c249398381e1b25857ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515355"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Oktatóanyag: Azure Active Directory integráció a Predictix Pricing szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Predictix árát a Azure Active Directory (Azure AD) szolgáltatással.

Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Predictix.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Predictix (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Predictix-díjszabással való konfigurálásához a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, regisztrálhat egy [hónapos próbaverziós](https://azure.microsoft.com/pricing/free-trial/) előfizetésre.
* Egy Predictix előfizetés, amely egyszeri bejelentkezést engedélyez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Predictix ár-jelentéskészítés támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Predictix ár-jelentéskészítés hozzáadása a katalógusból

A Predictix díjszabásának az Azure AD-be való integrálásának beállításához hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához tartozó Predictix.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**  >  **minden alkalmazás**:

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Predictix Price Reporting** kifejezést. Válassza ki a **Predictix** a keresési eredmények között, majd válassza a **Hozzáadás** lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Predictix díjszabásával a Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a Predictix Price Reporting szolgáltatásban.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Predictix díjszabását a következő lépésekkel kell végrehajtania:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja a Predictix-díjszabási egyszeri bejelentkezést](#configure-predictix-price-reporting-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy Predictix](#create-a-predictix-price-reporting-test-user)** , amely a felhasználó Azure ad-beli képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Predictix díjszabásával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Predictix Price Reporting** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/sp-identifier.png)

    1. A **bejelentkezési URL** -cím mezőben adjon meg egy URL-címet ebben a mintában:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet ebben a mintában:

        ```https
        https://<companyname-pricing>.predictix.com
        https://<companyname-pricing>.dev.predictix.com
        ```

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges bejelentkezési URL-címet és azonosítót kell használnia. Az értékek lekéréséhez lépjen kapcsolatba a [Predictix Price Reporting támogatási csapatával](https://www.infor.com/company/customer-center/) . A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **tanúsítvány (Base64)** melletti **letöltési** hivatkozást, és az igényeinek megfelelő beállítást, és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Predictix ár-jelentés beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Azure ad-azonosító**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Predictix-jelentés egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést az Predictix ár-jelentési oldalon szeretné beállítani, el kell küldenie a letöltött tanúsítványt és a Azure Portalról a [Predictix Price Reporting támogatási csapatához](https://www.infor.com/company/customer-center/)másolt URL-címeket. Ez a csapat biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** lehetőséget, majd válassza a **minden felhasználó** lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza ki a képernyő felső részén található **új felhasználó** elemet:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Például: BrittaSimon@contoso.com .)

    1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben található értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure AD egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Predictix.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **Predictix ár-jelentéskészítés** lehetőséget.

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Predictix ár-jelentéskészítés** lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-predictix-price-reporting-test-user"></a>Predictix ár jelentéskészítési teszt felhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Predictix Price Reporting szolgáltatásban. A felhasználók hozzáadásához a [Predictix Price Reporting támogatási csapatával](https://www.infor.com/company/customer-center/) dolgozhat. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Ha a hozzáférési panelen kiválasztja a Predictix díjszabási csempét, akkor automatikusan be kell jelentkeznie arra a Predictix-jelentési példányra, amelyhez be kell állítania az SSO-t. További információ: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)