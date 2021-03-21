---
title: 'Oktatóanyag: Azure Active Directory integráció a Replicon-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Replicon között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688501"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Oktatóanyag: a Replicon és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Replicon a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Replicon-t az Azure AD-vel, a következőket teheti:

* A Replicon-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Replicon az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Replicon egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Replicon támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-replicon-from-the-gallery"></a>Replicon hozzáadása a gyűjteményből

A Replicon Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Replicon a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Replicon** kifejezést a keresőmezőbe.
1. Válassza ki a **Replicon** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Replicon a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Replicon-ben.

Az Azure AD SSO és a Replicon konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[REPLICON SSO konfigurálása](#configure-replicon-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre Replicon-teszt felhasználót](#create-replicon-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Replicon rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Replicon** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://global.replicon.com/!/saml2/<client name>`

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a Replicon ügyfélszolgálati [csapatához](https://www.replicon.com/customerzone/contact-support) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A beállítások szerkesztéséhez kattintson az **SAML-aláíró tanúsítvány** szerkesztése/toll ikonjára.

    ![Aláírási algoritmus](common/signing-algorithm.png)

    1. Válassza az **SAML** -jogcímek aláírása **lehetőséget**.

    1. Válassza az **SHA-256** lehetőséget az **aláírási algoritmusként**.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Replicon vállalati webhelyre rendszergazdaként.

2. Az SAML 2,0 konfigurálásához hajtsa végre a következő lépéseket:

    ![SAML-hitelesítés engedélyezése](./media/replicon-tutorial/ic777805.png "SAML-hitelesítés engedélyezése")

    a. A **EnableSAML Authentication2** párbeszédpanel megjelenítéséhez fűzze hozzá a következőt az URL-címhez a vállalati kulcs után: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Az alábbi ábrán a teljes URL-cím sémája látható: `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kattintson a elemre **+** a **v20Configuration** szakasz kibontásához.

   c. Kattintson a elemre **+** a **metaDataConfiguration** szakasz kibontásához.

   d. **Sha256** kiválasztása a xmlSignatureAlgorithm

   e. Kattintson a **fájl kiválasztása** lehetőségre az Identity Provider metaadatainak XML-fájljának kiválasztásához, majd kattintson a **Küldés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Replicon.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Replicon** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-replicon-test-user"></a>Replicon-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy B. Simon nevű felhasználó létrehozása a Replicon-ben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. A webböngésző ablakban jelentkezzen be a Replicon vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **felügyeleti \> felhasználókat**.

    ![Felhasználók](./media/replicon-tutorial/ic777806.png "Felhasználók")

3. Kattintson a **+ felhasználó hozzáadása** lehetőségre.

    ![Felhasználó hozzáadása](./media/replicon-tutorial/ic777807.png "Felhasználó hozzáadása")

4. A **felhasználói profil** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználói profil](./media/replicon-tutorial/ic777808.png "Felhasználói profil")

    a. A **bejelentkezési név** szövegmezőbe írja be azon Azure ad-beli e-mail címet, amelyet szeretne kiépíteni `B.Simon@contoso.com` .

    > [!NOTE]
    > A bejelentkezési névnek egyeznie kell a felhasználó e-mail-címével az Azure AD-ben

    b. **Hitelesítési típusként** válassza az **egyszeri bejelentkezés** lehetőséget.

    c. Állítsa be a hitelesítési azonosítót ugyanarra az értékre, mint a bejelentkezési név (a felhasználó Azure AD e-mail-címe)

    d. A **részleg** szövegmezőbe írja be a felhasználó osztályát.

    e. Az **alkalmazott típusa** beállításnál válassza a **rendszergazda** elemet.

    f. Kattintson a **felhasználói profil mentése** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Replicon által biztosított Replicon felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Replicon csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Replicon, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)