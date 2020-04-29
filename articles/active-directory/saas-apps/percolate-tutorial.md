---
title: 'Oktatóanyag: Azure Active Directory integráció a átszivárog-szel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és átszivárog között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094598"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Oktatóanyag: Azure Active Directory integráció a átszivárog

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a átszivárog a Azure Active Directory (Azure AD) szolgáltatással.

Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a átszivárog.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a átszivárog (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció átszivárog való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést engedélyező átszivárog-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A átszivárog támogatja az SP által kezdeményezett és a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.

## <a name="add-percolate-from-the-gallery"></a>Átszivárog hozzáadása a gyűjteményből

A átszivárog Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a átszivárog a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **átszivárog**kifejezést. A keresési eredmények között válassza a **átszivárog** lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a átszivárog használatával egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a átszivárog-ben.

Az Azure AD egyszeri bejelentkezés átszivárog való konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja az átszivárog egyszeri bejelentkezést](#configure-percolate-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy átszivárog-teszt felhasználót](#create-a-percolate-test-user)** , amely a felhasználó Azure ad-képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Az Azure AD egyszeri bejelentkezés átszivárog való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **átszivárog** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon a **Szerkesztés** ikonra kattintva nyissa meg az **alapszintű SAML-konfiguráció** párbeszédpanelt:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen nem kell végrehajtania semmilyen műveletet az alkalmazás identitásszolgáltató-kezdeményezésű módban való konfigurálásához. Az alkalmazás már integrálva van az Azure-ba.

    ![Átszivárog tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, majd a **bejelentkezési URL-cím** mezőbe **https://percolate.com/app/login**írja be a következőt:

   ![Átszivárog tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)
6. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** ikont az **alkalmazás-összevonási metaadatok URL-címének**másolásához. Mentse ezt az URL-címet.

    ![Az alkalmazás-összevonás metaadat-URL-címének másolása](common/copy-metadataurl.png)

7. A **átszivárog beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Azure ad-azonosító**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-percolate-single-sign-on"></a>Átszivárog egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a átszivárog rendszergazdaként.

2. A Kezdőlap bal oldalán válassza a **Beállítások**lehetőséget:
    
    ![Beállítások kiválasztása](./media/percolate-tutorial/configure01.png)

3. A bal oldali ablaktáblán válassza az **SSO** elemet a **szervezet**területen:

    ![Egyszeri bejelentkezés kiválasztása a szervezet alatt](./media/percolate-tutorial/configure02.png)

    1. A **bejelentkezési URL-cím** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL-** értéket.

    1. Az **entitás azonosítója** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    1. A Jegyzettömbben nyissa meg a Azure Portalból letöltött Base-64 kódolású tanúsítványt. Másolja ki a tartalmát, és illessze be a **x509-tanúsítványok** mezőbe.

    1. Az **e-mail-attribútum** mezőbe írja be az **EmailAddress**értéket.

    1. Az **Identity Provider metaadatok URL-címe** mező nem kötelező. Ha a Azure Portalból másolt egy **alkalmazás-összevonási metaadatok URL-címét** , akkor beillesztheti ebbe a mezőbe.

    1. A **AuthNRequests alá kell írni?** listáról válassza a **nem**lehetőséget.

    1. Az **SSO automatikus kiépítés engedélyezése** listában válassza a **nem**lehetőséget.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**lehetőséget, majd válassza a **minden felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza ki a képernyő felső részén található **új felhasználó** elemet:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be **BrittaSimon@\<yourcompanydomain>\< . bővítmény>**. (Például: BrittaSimon@contoso.com.)

    1. Válassza a **jelszó megjelenítése**lehetőséget, majd írja le a **jelszó** mezőben található értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure AD egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a átszivárog.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **átszivárog**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **átszivárog**lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználók és csoportok kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-percolate-test-user"></a>Átszivárog-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a átszivárog, hozzá kell adnia őket a átszivárog-hez. Ezeket manuálisan kell felvennie.

Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a átszivárog rendszergazdaként.

2. A bal oldali ablaktáblán válassza a **felhasználók** a **szervezet**alatt lehetőséget. **Új felhasználók**kiválasztása:

    ![Új felhasználók kiválasztása](./media/percolate-tutorial/configure03.png)

3. A **felhasználók létrehozása** oldalon hajtsa végre a következő lépéseket.

    ![Felhasználók létrehozása lap](./media/percolate-tutorial/configure04.png)

    1. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét. Például: brittasimon@contoso.com.

    1. A **teljes név** mezőben adja meg a felhasználó nevét. Például: **Brittasimon**.

    1. Válassza a **felhasználók létrehozása**lehetőséget.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Amikor kiválasztja a átszivárog csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a átszivárog-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ: [alkalmazások elérése és használata a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)