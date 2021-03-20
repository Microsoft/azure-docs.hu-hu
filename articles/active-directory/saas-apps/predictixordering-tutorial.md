---
title: 'Oktatóanyag: Azure Active Directory integráció a Predictix rendeléssel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Predictix rendezése között.
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
ms.openlocfilehash: f48f7cf9507afae31e3c36aef517aab7b7d77ccf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515372"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Oktatóanyag: Azure Active Directory integráció a Predictix-rendezéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Predictix rendezését Azure Active Directory (Azure AD) használatával.
Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Predictix rendezéséhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Predictix rendelésbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Predictix rendeléssel való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/)kérhet.
* Az egyszeri bejelentkezést engedélyező Predictix-előfizetések.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Predictix rendezése támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="add-predictix-ordering-from-the-gallery"></a>Predictix-rendezés hozzáadása a katalógusból

A Predictix az Azure AD-be való beépítésének beállításához hozzá kell adnia a Predictix rendelést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**  >  **minden alkalmazás**:

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Predictix rendezése** kifejezést. A keresési eredmények között válassza a **Predictix rendezése** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Predictix rendeléssel egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a Predictix-rendezésben.

Az Azure AD egyszeri bejelentkezés Predictix rendeléssel való konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja az Predictix rendelés egyszeri bejelentkezést](#configure-predictix-ordering-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy Predictix rendezési teszt felhasználót](#create-a-predictix-ordering-test-user)** , amely a felhasználó Azure ad-képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Az Azure AD egyszeri bejelentkezés Predictix rendeléssel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Predictix megrendelése** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/sp-identifier.png)

    1. A **bejelentkezési URL** -cím mezőben adjon meg egy URL-címet ebben a mintában:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet ebben a mintában:

        ```https
        https://<companyname-pricing>.dev.ordering.predictix.com
        https://<companyname-pricing>.ordering.predictix.com
        ```

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges bejelentkezési URL-címet és azonosítót kell használnia. Az értékek beszerzéséhez vegye fel a kapcsolatot a [Predictix rendezési támogatási csapatával](https://www.predix.io/support/) . A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **tanúsítvány (Base64)** melletti **letöltési** hivatkozást, és az igényeinek megfelelő beállítást, és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Predictix-Rendezés beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    2. **Azure ad-azonosító**.

    3. **Kijelentkezési URL-cím**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Predictix-rendezés egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a Predictix rendezési oldalon szeretné konfigurálni, el kell küldenie a letöltött tanúsítványt és a Azure Portalról a [Predictix-rendezési támogatási csapatnak](https://www.predix.io/support/)átmásolt URL-címeket. Ez a csapat biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure AD egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Predictix rendezéséhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Predictix rendezése** lehetőséget:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Predictix rendezése** elemet.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-predictix-ordering-test-user"></a>Predictix megrendelés tesztelési felhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Predictix-rendezésben. A felhasználók hozzáadásához a [Predictix rendezési támogatási csapatával](https://www.predix.io/support/) dolgozhat. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Amikor kiválasztja a Predictix rendezése csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Predictix megrendelési példányba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)