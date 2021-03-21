---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SKYSITE | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SKYSITE között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 9d4413c47e40a611fb1559b32aac1e32a71d1998
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515984"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SKYSITE

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SKYSITE a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SKYSITE-t az Azure AD-vel, a következőket teheti:

* A SKYSITE-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SKYSITE az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SKYSITE egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SKYSITE támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A SKYSITE **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-skysite-from-the-gallery"></a>SKYSITE hozzáadása a gyűjteményből

A SKYSITE Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SKYSITE a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SKYSITE** kifejezést a keresőmezőbe.
1. Válassza ki a **SKYSITE** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a SKYSITE

Konfigurálja és tesztelje az Azure AD SSO-t a SKYSITE a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SKYSITE-ben.

Az Azure AD SSO és a SKYSITE konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SKYSITE SSO konfigurálása](#configure-skysite-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre SKYSITE-teszt felhasználót](#create-skysite-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-SKYSITE rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SKYSITE** alkalmazás-integráció lapján kattintson a **Tulajdonságok fülre** , és hajtsa végre a következő lépést: 

    ![Egyszeri bejelentkezési tulajdonságok](./media/skysite-tutorial/config05.png)

    * Másolja a **felhasználói hozzáférési URL-címet** , és illessze be a **SKYSITE SSO konfigurálása szakaszba**, amelyet az oktatóanyag későbbi részében ismertet.

1. Az **SKYSITE** -alkalmazás integrációja lapon navigáljon az **egyszeri bejelentkezés elemre**.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. A SKYSITE alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

1. A fentieken kívül a SKYSITE alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **csoport jogcímek (előzetes verzió)** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

    a. Kattintson a **kérelemben visszaadott csoportok** melletti **tollra** .

    ![Képernyőfelvétel: a felhasználói jogcímek új jogcím hozzáadására szolgáló lehetőséggel jelennek meg.](./media/skysite-tutorial/config01.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/skysite-tutorial/config02.png)

    b. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

    c. Válassza ki a **csoport azonosítója** **forrás attribútumát** .

    d. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **SKYSITE beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a SKYSITE.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SKYSITE** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-skysite-sso"></a>SKYSITE SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a SKYSITE vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a lap jobb felső sarkában található **Beállítások** elemre, majd keresse meg a **fiók beállítást**.

    ![A képernyőfelvételen a beállítások közül kiválasztott Fiókbeállítások láthatók.](./media/skysite-tutorial/config03.png)

5. Váltson át az **egyszeri bejelentkezés (SSO)** lapra, hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyszeri bejelentkezés lap, amelyen megadhatja a leírt értékeket.](./media/skysite-tutorial/config04.png)

    a. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be a **felhasználói hozzáférési URL-cím** értékét, amelyet a Azure Portal **Tulajdonságok** lapján másolt.

    b. A **tanúsítvány feltöltése** lehetőségre kattintva töltse fel a Azure Portalból letöltött Base64-kódolású tanúsítványt.

    c. Kattintson a **Mentés** gombra.

### <a name="create-skysite-test-user"></a>SKYSITE-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a SKYSITE-ben. A SKYSITE támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a SKYSITE-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SKYSITE csempére kattint, automatikusan be kell jelentkeznie arra a SKYSITE, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A SKYSITE kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)