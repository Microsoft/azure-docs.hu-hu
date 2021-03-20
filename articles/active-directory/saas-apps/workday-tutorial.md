---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció munkanapokkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és munkanap között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181373"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció munkanapokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a munkanapokat Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a munkanapokat, a következőket teheti:

* A munkanapokhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkanapokra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A munkaidő egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A munkanap támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

* A munkanap Mobile Application mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. A konfigurálásával kapcsolatos további információkért kövesse [ezt](workday-mobile-tutorial.md) a hivatkozást.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-workday-from-the-gallery"></a>Munkanapok hozzáadása a katalógusból

A munkanapoknak az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a munkaterületet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **munkanap** kifejezést a keresőmezőbe.
1. Válassza ki a **munkanapokat** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Azure AD SSO konfigurálása és tesztelése munkanapokon

Konfigurálja és tesztelje az Azure AD SSO-t munkanapokon egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között munkanapokon.

Az Azure AD SSO munkanapokkal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[munkanap beállítása](#configure-workday)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy munkanap-tesztelési felhasználót](#create-workday-test-user)** , hogy a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli munkatársaival rendelkezzen.
3. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **munkanap** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. A **kijelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, a válasz URL-címével és a kijelentkezési URL-címmel. A válasz URL-címének rendelkeznie kell egy altartománnyal, például: www, WD2, wd3, wd3-Impl, wd5, wd5-Impl).
    > Hasonló, `http://www.myworkday.com` de nem használható `http://myworkday.com` . Az értékek lekéréséhez forduljon a [munkanapokhoz tartozó ügyfél-támogatási csapathoz](https://www.workday.com/en-us/partners-services/services/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A munkanap alkalmazása egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A munkanap-alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail**, **UPN** stb. használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

    > [!NOTE]
    > Itt a name ID-t az UPN (User. userPrincipalName) értékkel rendelte hozzá alapértelmezettként. Az egyszeri bejelentkezés sikeres működéséhez le kell képeznie a tényleges felhasználói azonosító nevét (e-mail-címe, UPN stb.).

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Ha módosítani szeretné az **aláírási** beállításokat a követelmény szerint, kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![Tanúsítvány](common/edit-certificate.png) 

    ![SAML-aláíró tanúsítvány](./media/workday-tutorial/signing-option.png)

    a. Válassza az **SAML-válasz aláírása és** az **aláírási beállítás** lehetőséget.

    b. Kattintson a **Mentés** gombra.

1. A **munkanap beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a munkanapokhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **munkanap** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nincs szerepkör beállítva ehhez az alkalmazáshoz, akkor a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-workday"></a>Munkanapok konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a munkanap vállalati webhelyre rendszergazdaként.

1. A keresőmezőbe keresse meg a Kezdőlap bal felső részén található **bérlő beállítása – biztonság** **jelölőnégyzetet** .

    ![Bérlői biztonság szerkesztése](./media/workday-tutorial/IC782925.png "Bérlői biztonság szerkesztése")


1. Az **SAML beállítása** szakaszban hajtsa végre a következő lépéseket:

    ![SAML-telepítés](./media/workday-tutorial/IC782926.png "SAML-telepítés")

    a.  Válassza az **SAML-hitelesítés engedélyezése** lehetőséget.

    b.  Kattintson a **sor hozzáadása** gombra.

1. Az **SAML Identity Providers** szakaszban hajtsa végre az alábbi műveleteket az újonnan létrehozott sorhoz.

    a. Hajtsa végre a következő műveleteket a mezőkhöz, amelyek alább láthatók.

    ![SAML-identitás szolgáltatók 1](./media/workday-tutorial/IC7829271.png "SAML-identitásszolgáltatók")

    * Az **identitás-szolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO*).

    * A Azure Portal a **munkanap beállítása** szakaszban másolja az **Azure ad-azonosító** értékét, majd illessze be a **kiállító** szövegmezőbe.

    * Nyissa meg a letöltött **tanúsítványt** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat az **x. 509 tanúsítvány** szövegmezőbe.

    b. Hajtsa végre a következő műveleteket a mezőkhöz, amelyek alább láthatók.

    ![SAML-identitás szolgáltatók 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML-identitásszolgáltatók")

    * Kattintson a **identitásszolgáltató kezdeményezett kijelentkezés engedélyezése** jelölőnégyzetre.

    * A **kijelentkezési válasz URL-címe** szövegmezőbe írja be a következőt: **http://www.workday.com** .

    * A **kijelentkezési kérelem URL-címe** szövegmezőbe illessze be a **KIjelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    * Kattintson az **SP által kezdeményezett** jelölőnégyzetre.

    * A **szolgáltató azonosítója** szövegmezőbe írja be a következőt: **http://www.workday.com** .


    * Válassza a ne állítsa le az **SP által kezdeményezett hitelesítési kérelmet**.

    c. Hajtsa végre a következő műveleteket a mezőkhöz, amelyek alább láthatók.

    ![SAML-identitás szolgáltatók 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML-identitásszolgáltatók")

    * A Azure Portal a **munkanap beállítása** szakaszban másolja be a **bejelentkezési URL-címet** , majd ILLESSZE be a **identitásszolgáltató SSO-szolgáltatás URL-címének** szövegmezőbe.

    * A **környezetekhez használt** szövegmezőben válassza ki a megfelelő környezeti neveket a legördülő listából.

1. Hajtsa végre az alábbi lépéseket az alábbi képen.

    ![Workday](./media/workday-tutorial/service-provider.png "SAML-identitásszolgáltatók")

    a. A szolgáltató **azonosítója (elavult)** szövegmezőbe írja be a következőt: **http://www.workday.com** .

    b. A **IDENTITÁSSZOLGÁLTATÓ SSO-szolgáltatás URL-címe (elavult)** szövegmezőbe írja be a **bejelentkezési URL-cím** értékét.

    c. Válassza **a ne állítsa le az SP által kezdeményezett hitelesítési kérelmet (elavult lesz)** jelölőnégyzetet.

    d. A **hitelesítési kérelem aláírása módszernél** válassza a **sha256** lehetőséget.

    e. Kattintson az **OK** gombra.

    > [!NOTE]
    > Győződjön meg arról, hogy helyesen állította be az egyszeri bejelentkezést. Ha az egyszeri bejelentkezést nem megfelelő beállítással engedélyezi, előfordulhat, hogy nem tudja beírni az alkalmazást a hitelesítő adataival, és kizárja a zárolást. Ebben az esetben a munkanap olyan biztonsági mentési bejelentkezési URL-címet biztosít, ahol a felhasználók a szokásos felhasználónévvel és jelszóval jelentkezhetnek be a következő formátumban: [a munkanap URL-címe]/login.Flex? átirányítás = n

### <a name="create-workday-test-user"></a>Munkanapokat vizsgáló felhasználó létrehozása

1. Jelentkezzen be a munkanap vállalati webhelyre rendszergazdaként.

1. Kattintson a **profil** lehetőségre a jobb felső sarokban, válassza a **Home (Kezdőlap** ) lehetőséget, és kattintson az **alkalmazások** lap **könyvtár** elemére. 

1. A **könyvtár** lapon válassza a **munkavégzők keresése** lehetőséget a Nézet lapon.

    ![Feldolgozók keresése](./media/workday-tutorial/user-directory.png)

1.  A **munkavégzők keresése** lapon válassza ki a felhasználót az eredmények közül.

1. A következő lapon válassza a **feladatok > munkavégző biztonság** lehetőséget, és a **munkanap fióknak** egyeznie kell az Azure Active Directoryval, mint a **Name ID** érték.

    ![Feldolgozói biztonság](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Ha szeretne többet megtudni arról, hogyan hozhat létre egy munkanap-tesztelési felhasználót, forduljon a [munkanapokat támogató ügyfélszolgálati csapathoz](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a munkanapokhoz tartozó bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

2. A bejelentkezési URL-címet közvetlenül a munkanapokon is elindíthatja, és onnan kezdeményezheti a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Amikor a hozzáférési panelen a munkanap csempére kattint, automatikusan be kell jelentkeznie arra a munkanapon, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A munkanap konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)