---
title: 'Oktatóanyag: Azure Active Directory integráció a ExpenseIn-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ExpenseIn között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179163"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Oktatóanyag: a ExpenseIn és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ExpenseIn a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ExpenseIn-t az Azure AD-vel, a következőket teheti:

* A ExpenseIn-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ExpenseIn az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ExpenseIn egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 
* A ExpenseIn **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.
* A ExpenseIn konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn hozzáadása a gyűjteményből

A ExpenseIn Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ExpenseIn a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ExpenseIn** kifejezést a keresőmezőbe.
1. Válassza ki a **ExpenseIn** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Azure AD SSO konfigurálása és tesztelése a ExpenseIn-hez

Konfigurálja és tesztelje az Azure AD SSO-t a ExpenseIn a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ExpenseIn-ben.

Az Azure AD SSO és a ExpenseIn konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a EXPENSEIN SSO](#configure-expensein-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre ExpenseIn-tesztelési felhasználót](#create-expensein-test-user)** , hogy a ExpenseIn B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **ExpenseIn** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania a lépéseket, mivel az alkalmazás már előre integrálva van az Azure-ban.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://app.expensein.com/saml`

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához, majd kattintson a **Letöltés** gombra a **tanúsítvány** letöltéséhez, és mentse azt a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. A **ExpenseIn beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ExpenseIn.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ExpenseIn** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.


## <a name="configure-expensein-sso"></a>ExpenseIn SSO konfigurálása

1. A ExpenseIn belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **ExpenseIn beállítása** elemre, majd a ExpenseIn alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a ExpenseIn való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a ExpenseIn, jelentkezzen be a ExpenseIn vállalati webhelyre rendszergazdaként.

1. Kattintson a **rendszergazda** elemre az oldal tetején, majd navigáljon az **egyszeri bejelentkezéshez** , és kattintson a **szolgáltató hozzáadása** parancsra.

     ![A "rendszergazda" lapot és az "egyszeres Sign-On szolgáltatók" lapot és a "szolgáltató hozzáadása" elemet megjelenítő képernyőkép.](./media/expenseIn-tutorial/config01.png)

1. Az **új identitás-szolgáltató** előugró ablakában hajtsa végre a következő lépéseket:

    ![Képernyőkép: az "Identity Provider szerkesztése" előugró ablak a beírt értékekkel.](./media/expenseIn-tutorial/config02.png)

    a. A **szolgáltató neve** szövegmezőbe írja be a nevet; például: Azure.

    b. Válassza az **Igen** lehetőséget a **szolgáltató Intitiated való bejelentkezéshez**.

    c. A **cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    d. A **kiállító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    e. Nyissa meg a tanúsítványt (Base64) a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **tanúsítvány** szövegmezőbe.

    f. Kattintson a **Létrehozás** lehetőségre.

### <a name="create-expensein-test-user"></a>ExpenseIn-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a ExpenseIn, a ExpenseIn kell kiépíteni őket. A ExpenseIn-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ExpenseIn rendszergazdaként.

2. A lap tetején kattintson a **rendszergazda** elemre, majd keresse meg a **felhasználók** lapot, és kattintson az **új felhasználó** lehetőségre.

     ![Képernyőkép: a "rendszergazda" lap és a "felhasználók kezelése" lap, amelynél az "új felhasználó" lehetőség van kiválasztva.](./media/expenseIn-tutorial/config03.png)

3. A **részletek** előugró ablakában hajtsa végre a következő lépéseket:

    ![ExpenseIn-konfiguráció](./media/expenseIn-tutorial/config04.png)

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail** szövege szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `B.Simon@contoso.com` .

    d. Kattintson a **Létrehozás** lehetőségre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a ExpenseIn csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a ExpenseIn, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [A ExpenseIn kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A ExpenseIn és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)