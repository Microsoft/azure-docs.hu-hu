---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Helpshift | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Helpshift között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: b20d47962bf5467b9a5d69327e7fc152215edaa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445050"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Helpshift

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Helpshift a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Helpshift-t az Azure AD-vel, a következőket teheti:

* A Helpshift-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Helpshift az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Helpshift egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Helpshift támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-helpshift-from-the-gallery"></a>Helpshift hozzáadása a gyűjteményből

A Helpshift Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Helpshift a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Helpshift** kifejezést a keresőmezőbe.
1. Válassza ki a **Helpshift** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Helpshift

Konfigurálja és tesztelje az Azure AD SSO-t a Helpshift a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Helpshift-ben.

Az Azure AD SSO és a Helpshift konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[HELPSHIFT SSO konfigurálása](#configure-helpshift-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Helpshift-teszt felhasználót](#create-helpshift-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Helpshift rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Helpshift** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourDOMAIN>.helpshift.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépéseket, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    d. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, a bejelentkezési URL-címmel és a továbbítási állapottal. Az értékek lekéréséhez forduljon a Helpshift ügyfélszolgálati [csapatához](mailto:support@helpshift.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Helpshift beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Helpshift.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Helpshift** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-helpshift-sso"></a>Helpshift SSO konfigurálása

1. Egy másik böngészőben jelentkezzen be a Helpshift alkalmazásba rendszergazdaként.

1. Nyissa meg a Helpshift **irányítópultját** , és kattintson a **Beállítások ikonra**.

    ![A képernyőképen a Helpshift beállítások ikon látható.](./media/helpshift-tutorial/configuration01.png)

1. Kattintson az **integrációk** fülre, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az integráció lap, amelyen elvégezheti a leírt lépéseket.](./media/helpshift-tutorial/configuration02.png)

    a. Kapcsolja be az **egyszeri bejelentkezést (SAML – SSO)**.

    b. **Azure Active Directoryként** válassza az **Identity Provider (identitásszolgáltató)** elemet.

    c. Az **SAML 2,0-végpont URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    d. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben, másolja át a fájl tartalmát (a "– – Begin Certificate –-" és a "– End Certificate –" sorok használata nélkül), és illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    e. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    f. Kattintson a **módosítások alkalmazása** lehetőségre.

### <a name="create-helpshift-test-user"></a>Helpshift-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Helpshift-ben. A [Helpshift ügyfél-támogatási csapattal](mailto:support@helpshift.com) együttműködve veheti fel a felhasználókat a Helpshift-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Helpshift csempére kattint, automatikusan be kell jelentkeznie arra a Helpshift, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Helpshift kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)