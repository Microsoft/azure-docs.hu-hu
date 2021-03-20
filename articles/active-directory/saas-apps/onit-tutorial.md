---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a onit | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és onit között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 40ed33021a892e140dfff47417137046557c0aef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522359"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a onit

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a onit a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az onit-t az Azure AD-vel, a következőket teheti:

* A onit-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a onit az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Onit egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A onit támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-onit-from-the-gallery"></a>Onit hozzáadása a gyűjteményből

A onit Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a onit a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **onit** kifejezést a keresőmezőbe.
1. Válassza ki a **onit** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a onit

Konfigurálja és tesztelje az Azure AD SSO-t a onit a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a onit-ben.

Az Azure AD SSO és a onit konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ONIT SSO konfigurálása](#configure-onit-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre onit-teszt felhasználót](#create-onit-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-onit rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **onit** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<sub-domain>.onit.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a onit ügyfélszolgálati [csapatához](https://www.onit.com/support) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **onit beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a onit.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **onit** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-onit-sso"></a>Onit SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a onit vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson az **Adminisztráció** elemre.
   
    ![Képernyőfelvétel: az "M s s s O test" oldal tetején található menü, a "felügyelet" művelettel kiválasztva.](./media/onit-tutorial/IC791174.png "Felügyelet")

3. Kattintson a **vállalat szerkesztése** elemre.
   
    ![Vállalat szerkesztése](./media/onit-tutorial/IC791175.png "Vállalat szerkesztése")
   
4. Kattintson a **Biztonság** fülre.
    
    ![Vállalati adatok szerkesztése](./media/onit-tutorial/IC791176.png "Vállalati adatok szerkesztése")

5. A **Biztonság** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/onit-tutorial/IC791177.png "Egyszeri bejelentkezés")

    a. **Hitelesítési stratégiaként** válassza az **egyszeri bejelentkezés és a jelszó** lehetőséget.
    
    b. A **identitásszolgáltató cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    c. A **identitásszolgáltató kijelentkezési URL-címe** szövegmezőben illessze be a  **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    d. A **identitásszolgáltató Tanúsítvány ujjlenyomata (SHA1)** szövegmezőbe illessze be a tanúsítvány  **ujjlenyomatának** értékét, amelyet a Azure Portalból másolt.

### <a name="create-onit-test-user"></a>Onit-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a onit, a onit-ben kell kiépíteni őket. Onit esetén a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **onit** vállalati webhelyre rendszergazdaként.

2. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

    ![Felügyelet](./media/onit-tutorial/IC791180.png "Felügyelet")

3. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felhasználó hozzáadása](./media/onit-tutorial/IC791181.png "Felhasználó hozzáadása")

    a. Írja be egy érvényes Azure AD-fiók **nevét** és **e-mail-címét** , amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

    b. Kattintson a **Létrehozás** lehetőségre.

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve megerősíti a fiókját, mielőtt az aktívvá válna.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a onit csempére kattint, automatikusan be kell jelentkeznie arra a onit, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A onit kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)