---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OfficeSpace szoftverrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és OfficeSpace szoftver között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: bb994e154f410fb15b66588b41688326509a8f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OfficeSpace szoftverrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OfficeSpace szoftvert Azure Active Directory (Azure AD) használatával. Ha integrálja a OfficeSpace szoftvert az Azure AD-val, a következőket teheti:

* A OfficeSpace szoftverhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy OfficeSpace a szoftvereket az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* OfficeSpace szoftveres egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A OfficeSpace szoftver támogatja az **SP** által KEZDEMÉNYEZett SSO-t


* A OfficeSpace szoftver **csak időben támogatja a** felhasználók üzembe helyezését


## <a name="adding-officespace-software-from-the-gallery"></a>OfficeSpace-szoftver hozzáadása a katalógusból

A OfficeSpace-szoftverek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a OfficeSpace szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **OfficeSpace Software** kifejezést a keresőmezőbe.
1. Válassza ki a **OfficeSpace szoftvert** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Azure AD-alapú egyszeri bejelentkezés konfigurálása és tesztelése OfficeSpace szoftverhez

Konfigurálja és tesztelje az Azure AD SSO-t OfficeSpace szoftverrel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a OfficeSpace szoftverben.

Az Azure AD SSO OfficeSpace szoftverrel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[OfficeSpace szoftveres egyszeri bejelentkezés konfigurálása](#configure-officespace-software-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre OfficeSpace szoftveres tesztelési felhasználót](#create-officespace-software-test-user)** –, hogy a OfficeSpace-szoftverben található, a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **OfficeSpace Software** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez lépjen kapcsolatba a [OfficeSpace szoftveres](mailto:support@officespacesoftware.com) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A OfficeSpace szoftveres alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A OfficeSpace szoftveralkalmazások azt várja, hogy a **NameIdentifier** a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a OfficeSpace-alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | e-mail | User. mail |
    | name | felhasználó. DisplayName |
    | first_name | User. givenName |
    | last_name | felhasználó. vezetéknév |

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **OfficeSpace szoftver beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a OfficeSpace szoftverhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **OfficeSpace szoftvert**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-officespace-software-sso"></a>OfficeSpace szoftveres egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a OfficeSpace szoftveres bérlőbe.

2. Válassza a **Beállítások** , majd az **Összekötők** lehetőséget.

    ![Képernyőkép, amely a "beállítások" legördülő menü "összekötők" lehetőséget jeleníti meg.](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Kattintson az **SAML-hitelesítés** elemre.

    ![Képernyőkép, amely a "hitelesítés" szakaszt jeleníti meg "S A M L hitelesítés" művelettel.](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Az **SAML hitelesítés** szakaszban hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása az alkalmazás oldalán](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. A **kijelentkezési szolgáltató URL-címe** szövegmezőbe illessze be azt a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    b. Az **ügyfél identitásszolgáltató-cél URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. Illessze be a Azure Portalból másolt **ujjlenyomat** -értéket az ügyfél- **identitásszolgáltató tanúsítványának ujjlenyomat** szövegmezőbe. 

    d. Kattintson a **Beállítások mentése** gombra.

### <a name="create-officespace-software-test-user"></a>OfficeSpace szoftveres tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a OfficeSpace szoftverben. A OfficeSpace szoftver támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a OfficeSpace szoftverben, a hitelesítés után létrejön egy új.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [OfficeSpace szoftver támogatási csoportjával](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a OfficeSpace szoftver csempére kattint, automatikusan be kell jelentkeznie arra a OfficeSpace-szoftverbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [OfficeSpace szoftver kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)