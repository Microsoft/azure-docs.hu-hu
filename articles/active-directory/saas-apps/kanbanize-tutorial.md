---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Kanbanize | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Kanbanize között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 05ce0d3d3d5c66514edc07446aaf0a879657c10c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459237"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Kanbanize

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kanbanize a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Kanbanize-t az Azure AD-vel, a következőket teheti:

* A Kanbanize-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Kanbanize az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kanbanize egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Kanbanize támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Kanbanize **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-kanbanize-from-the-gallery"></a>Kanbanize hozzáadása a gyűjteményből

A Kanbanize Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Kanbanize a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Kanbanize** kifejezést a keresőmezőbe.
1. Válassza ki a **Kanbanize** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Kanbanize

Konfigurálja és tesztelje az Azure AD SSO-t a Kanbanize a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Kanbanize-ben.

Az Azure AD SSO és a Kanbanize konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[KANBANIZE SSO konfigurálása](#configure-kanbanize-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Kanbanize-teszt felhasználót](#create-kanbanize-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Kanbanize rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Kanbanize** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

     a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.kanbanize.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Kattintson a **további URL-címek beállítása** elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `/ctrl_login/saml_login`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a Kanbanize ügyfélszolgálati [csapatához](mailto:support@ms.kanbanize.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Kanbanize alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a NameIdentifier a **User. userPrincipalName** leképezéssel van leképezve. A Kanbanize alkalmazás a NameIdentifier a **User. mail** használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a Szerkesztés ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Kanbanize beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Kanbanize.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Kanbanize** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-kanbanize-sso"></a>Kanbanize SSO konfigurálása

1. A Kanbanize belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Kanbanize beállítása** elemre, majd a Kanbanize alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Kanbanize való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Kanbanize, nyisson meg egy új böngészőablakot, és jelentkezzen be a Kanbanize vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a lap jobb felső sarkában található **Beállítások** emblémára.

    ![Kanbanize-beállítások](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. A menü bal oldalán található adminisztráció panel oldalon kattintson az **integrációk** elemre, majd engedélyezze **az egyszeri bejelentkezést**.

    ![A képernyőképen az adminisztráció panel látható integráció kiválasztva.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Az integrációk szakaszban kattintson a **Konfigurálás** elemre **egy Sign-On integrációs** oldal megnyitásához.

    ![Kanbanize-konfiguráció](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Az **egyetlen Sign-On integráció** lapon a **konfigurációk** területen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyetlen Sign-On integrációs oldal, ahol megadhatja az ebben a lépésben szereplő értékeket.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Az **identitásszolgáltató entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    b. A **identitásszolgáltató bejelentkezési végpont** szövegmezőben illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    c. A **identitásszolgáltató kijelentkezési végpont** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portal másolt.

    d. Adja meg ezt az értéket az E-mail szövegmezőben az **attribútum neve** mezőben. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Az utónév szövegmezőbe írja be ezt az értéket az **attribútum neve** szövegmezőbe. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Az utónév szövegmezőbe írja be ezt az értéket az **attribútum nevében** . `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Ezeket az értékeket úgy érheti el, hogy a megfelelő attribútum névterét és nevét a Azure Portal felhasználói attribútumok szakaszában egyesíti.

    : Nyissa meg a Jegyzettömbben a Azure Portal letöltött Base-64 kódolású tanúsítványt, másolja a tartalmát (a kezdő és záró jelölő nélkül), majd illessze be a **identitásszolgáltató X. 509 tanúsítvány** mezőbe.

    h. Győződjön meg arról, **hogy az egyszeri bejelentkezés és az Kanbanize is engedélyezve van**

    i. Kattintson a **Beállítások mentése** gombra.

### <a name="create-kanbanize-test-user"></a>Kanbanize-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Kanbanize-ben. A Kanbanize támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Kanbanize-ben, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Kanbanize ügyfélszolgálati csapatához](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kanbanize csempére kattint, automatikusan be kell jelentkeznie arra a Kanbanize, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Kanbanize kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)