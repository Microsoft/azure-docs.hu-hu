---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a CloudGuard Dome9-Arcmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a CloudGuard Dome9 ív között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732196"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CloudGuard Dome9 Arcmal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CloudGuard Dome9-ívet a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a CloudGuard Dome9-ívet, a következőket teheti:

* Vezérlés az Azure AD-ben, hogy ki férhet hozzá a CloudGuard Dome9-ív eléréséhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Point CloudGuard Dome9 arc és az Azure AD-fiókjaik használatával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A jelölőnégyzetes CloudGuard Dome9 arc egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Point CloudGuard Dome9 ív támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Ellenőrzési pont CloudGuard Dome9 ív hozzáadása a katalógusból

A CloudGuard Dome9-ív Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a CloudGuard Dome9 ív elemet a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CloudGuard Dome9 ív** kifejezést a keresőmezőbe.
1. Válassza a **jelölőnégyzet CloudGuard Dome9 ív** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a CloudGuard Dome9-ív esetében

Konfigurálja és tesztelje az Azure AD SSO-t a CloudGuard Dome9-ív használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a CloudGuard Dome9-ív.

Az Azure AD SSO konfigurálásához és teszteléséhez a CloudGuard Dome9-ív használatával hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[CloudGuard Dome9 arc SSO konfigurálása](#configure-check-point-cloudguard-dome9-arc-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Ellenőrző pont létrehozása a CloudGuard Dome9-ív tesztelési felhasználóval](#create-check-point-cloudguard-dome9-arc-test-user)** – ha a CloudGuard Dome9-ív egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli párja van.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **jelölőnégyzet CloudGuard Dome9 arc** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Ekkor megjelenik az `<company name>` érték a **CloudGuard Dome9 arc egyszeri bejelentkezésének beállítása** szakaszban, amelyet az oktatóanyag későbbi részében talál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A CloudGuard Dome9 arc alkalmazás az SAML-jogcímeket egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a CloudGuard Dome9 arc alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | ---------------| --------------- |
    | memberOf | User. assignedroles |

    >[!NOTE]
    >Ide [kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan hozhat létre szerepköröket az Azure ad-ben.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **CloudGuard Dome9-ív beállítása** szakaszban másolja be a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a CloudGuard Dome9-ív számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **jelölőnégyzet CloudGuard Dome9 ív** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Ellenőrzési pont CloudGuard Dome9 arc egyszeri bejelentkezésének konfigurálása

1. Ha a CloudGuard Dome9 ív használatával szeretné automatizálni a konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés-ellenőrzési pont CloudGuard Dome9 ív** lehetőségre a CloudGuard Dome9 arc alkalmazásra irányítva. A bejelentkezéshez adja meg a rendszergazdai hitelesítő adatokat, hogy bejelentkezzen a CloudGuard Dome9-ívbe. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a CloudGuard Dome9-ívet, nyisson meg egy új böngészőablakot, és jelentkezzen be a CloudGuard Dome9 arc vállalati webhelyén rendszergazdaként, és hajtsa végre a következő lépéseket:

2. Kattintson a jobb felső sarokban található **Profilbeállítások** lehetőségre, majd a **Fiókbeállítások** lehetőségre. 

    ![Képernyőkép, amely megjeleníti a "Fiókbeállítások" menüt a "Fiókbeállítások" beállítással.](./media/dome9arc-tutorial/configure1.png)

3. Navigáljon az **egyszeri bejelentkezéshez** , majd kattintson az **Engedélyezés** elemre.

    ![Képernyőkép, amely megjeleníti a "S S O" fület és az "engedélyezés" lehetőséget.](./media/dome9arc-tutorial/configure2.png)

4. Az SSO-konfiguráció szakaszban hajtsa végre a következő lépéseket:

    ![Ellenőrzési pont CloudGuard Dome9 ív konfigurációja](./media/dome9arc-tutorial/configure3.png)

    a. Adja meg a vállalat nevét a **fiókazonosító** szövegmezőben. Ezt az értéket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában említett **Válasz** és **Bejelentkezés** URL-címében kell használni.

    b. A **kiállító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet másolt a Azure Portal.

    c. A **identitásszolgáltató-végpont URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet másolt a Azure Portal.

    d. Nyissa meg a letöltött Base64-kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Ellenőrzési pont CloudGuard Dome9 ív tesztelési felhasználójának létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a CloudGuard Dome9-ívbe, az alkalmazásba kell azokat kiépíteni. A jelölőnégyzetes CloudGuard Dome9-ív támogatja az igény szerinti üzembe helyezést, de ahhoz, hogy megfelelően működjön, a felhasználónak ki kell választania egy adott **szerepkört** , és hozzá kell rendelnie azt a felhasználóhoz.

   >[!Note]
   >A **szerepkörök** létrehozásához és egyéb részletekért forduljon a következőhöz: [CloudGuard Dome9 arc ügyfél-támogatási csoport](mailto:Dome9@checkpoint.com).

**Felhasználói fiók manuális üzembe helyezéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a CloudGuard Dome9 arc vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználók & szerepkörök** elemre, majd kattintson a **felhasználók** elemre.

    ![Képernyőfelvétel: "felhasználók & szerepkörök" a kiválasztott "felhasználók" művelettel.](./media/dome9arc-tutorial/user1.png)

3. Kattintson a **felhasználó hozzáadása** elemre.

    ![Képernyőfelvétel: "felhasználók & szerepkörök" a "felhasználó hozzáadása" gomb kiválasztásával.](./media/dome9arc-tutorial/user2.png)

4. A **felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user3.png)

    a. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet B.Simon@contoso.com .

    b. Az **Utónév** szövegmezőbe írja be a (z) "B" nevű felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.

    d. **Egyszeri bejelentkezéses felhasználó** **bekapcsolása**.

    e. Kattintson a **Létrehozás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a pipát a CloudGuard Dome9 ív bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a CloudGuard Dome9 arc bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Dome9-ív CloudGuard, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások területén a CloudGuard Dome9 ív csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a CloudGuard Dome9-ív, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A CloudGuard Dome9-ív konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
