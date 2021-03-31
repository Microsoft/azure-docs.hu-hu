---
title: 'Oktatóanyag: Azure Active Directory integráció a Fluxx Labs szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Fluxx Labs között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92452600"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Fluxx Labs szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Fluxx Labs-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Fluxx Labs-t, a következőket teheti:

* A Fluxx Labs szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Fluxx Labs szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Fluxx Labs egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Fluxx Labs támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Fluxx Labs konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs hozzáadása a gyűjteményből

A Fluxx Labs Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Fluxx Labs szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **Fluxx Labs** kifejezést.
1. Válassza az **Fluxx Labs** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Fluxx Labs szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t a Fluxx Labs-szel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Fluxx Labs szolgáltatásban.

Az Azure AD SSO az Fluxx Labs szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Fluxx Labs SSO konfigurálása](#configure-fluxx-labs-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Fluxx Labs-tesztkörnyezet létrehozása](#create-fluxx-labs-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli partnert szeretne létrehozni a Fluxx Labs-ben.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Fluxx Labs** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Környezet | URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Környezet | URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek megszerzéséhez forduljon a [Fluxx Labs](https://fluxx.zendesk.com) ügyfélszolgálatához. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Fluxx Labs beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Fluxx Labs elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Fluxx Labs** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-fluxx-labs-sso"></a>Fluxx Labs SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhelyre.

2. Válassza a **rendszergazda** lehetőséget a **Beállítások** szakaszban.

    ![A "rendszergazda" beállítású "beállítások" szakaszt bemutató képernyőkép.](./media/fluxxlabs-tutorial/config1.png)

3. A felügyeleti panelen válassza a **beépülő modulok**  >  **integrációk** elemet, majd válassza az **SAML SSO-(letiltva) lehetőséget.**

    ![Képernyőkép, amely az "integrációk" fület jeleníti meg "S A M L S s O-(letiltva)" jelöléssel.](./media/fluxxlabs-tutorial/config2.png)

4. Az attribútum szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "attribútumok" szakaszt "S A M L S O" értékkel jelölte meg, a mezőkben megadott értékek és a "Mentés" gomb kiválasztásával jelenik meg.](./media/fluxxlabs-tutorial/config3.png)

    a. Jelölje be az **SAML SSO** jelölőnégyzetet.

    b. A **kérelem elérési útja** szövegmezőbe írja be a következőt: **/Auth/SAML**.

    c. A **visszahívási útvonal** szövegmezőbe írja be a következőt: **/Auth/SAML/callback**.

    d. Az **állítás a fogyasztói szolgáltatás URL-címe (egy Sign-On URL-cím)** szövegmezőbe írja be a **Válasz URL-címét** , amelyet a Azure Portal megadott.

    e. A **célközönség (SP-entitás azonosítója)** szövegmezőbe írja be az **azonosító** értékét, amelyet a Azure Portal megadott.

    f. Illessze be a bejelentkezési URL-cím szövegmezőbe a Azure Portalból másolt **bejelentkezési URL** - **címet** .

    : Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    h. A **név azonosítójának formázása** szövegmezőbe írja be az értéket `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A tartalom mentése után a mező üresen jelenik meg a biztonság esetében, az érték azonban a konfigurációban lett mentve.

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs-tesztkörnyezet létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Fluxx Labs szolgáltatásba, azokat a Fluxx Labs szolgáltatásban kell kiépíteni. A Fluxx Labs esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhelyére.

2. Kattintson a lent látható **ikonra**.

    ![Képernyőkép: az irányítópult üres részében kiválasztott "plusz" ikonnal rendelkező rendszergazdai beállítások megjelenítése.](./media/fluxxlabs-tutorial/config6.png)

3. Az irányítópulton kattintson az alábbi megjelenő ikonra az **új személyek** kártya megnyitásához.

    ![Képernyőkép, amely a "felhasználók" ikon melletti "plusz" ikonra kattintva megjeleníti a "kapcsolat kezelése" menüt.](./media/fluxxlabs-tutorial/config4.png)

4. Az **új személyek** szakaszban hajtsa végre a következő lépéseket:

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config5.png)

    a. A Fluxx Labs az egyszeri bejelentkezéses bejelentkezések egyedi azonosítójaként használja az e-maileket. Töltse fel az **SSO UID** mezőt a felhasználó e-mail-címével, amely megfelel az e-mail-címnek, amelyet bejelentkezésként használ az SSO-ban.

    b. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Fluxx Labs csempére kattint, automatikusan be kell jelentkeznie a Fluxx Labs szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Fluxx Labs kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Fluxx Labs védetté tétele fejlett láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)