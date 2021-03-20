---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Sonarqube | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Sonarqube között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182332"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Sonarqube

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sonarqube a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Sonarqube-t az Azure AD-vel, a következőket teheti:

* A Sonarqube-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Sonarqube az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Sonarqube egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Sonarqube támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-sonarqube-from-the-gallery"></a>Sonarqube hozzáadása a gyűjteményből

A Sonarqube Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Sonarqube a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Sonarqube** kifejezést a keresőmezőbe.
1. Válassza ki a **Sonarqube** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Azure AD SSO konfigurálása és tesztelése a Sonarqube-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Sonarqube a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Sonarqube-ben.

Az Azure AD SSO és a Sonarqube konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SONARQUBE SSO konfigurálása](#configure-sonarqube-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Sonarqube-teszt felhasználót](#create-sonarqube-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Sonarqube rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Sonarqube** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:

    * **Éles környezetben**

        `https://servicessonar.corp.microsoft.com/`

    * **Fejlesztői környezet esetén**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sonarqube beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Sonarqube.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Sonarqube** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sonarqube-sso"></a>Sonarqube SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a Sonarqube vállalati webhelyre rendszergazdaként.

1. Kattintson a **felügyelet > konfiguráció > biztonság** elemre, és lépjen az **SAML beépülő modulra** az alábbi lépések végrehajtásával.

1. Másolja a következő adatokat a identitásszolgáltató metaadataiból, és illessze be őket a SonarQube beépülő modul megfelelő szövegmezőbe.
    1. Identitásszolgáltató-entitás azonosítója
    2. Bejelentkezési URL
    3. X. 509 tanúsítvány 
1. Mentse az összes adatot.
    ![SAML beépülő modul IDENTITÁSSZOLGÁLTATÓ](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Az **SAML** oldalon hajtsa végre a következő lépéseket:

    ![Sonarqube-konfiguráció](./media/sonarqube-tutorial/config01.png)

    a. Az **engedélyezett** beállítás váltása **Igen** értékre.

    b. Az **alkalmazás azonosítója** szövegmezőbe írja be a nevet, például **sonarqube**.

    c. A **szolgáltató neve** szövegmezőbe írja be az **SAML** nevet.

    d. A **szolgáltató azonosítója** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    e. Az **SAML bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    f. Nyissa meg a Base64 kódolású tanúsítványt a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **szolgáltatói tanúsítvány** szövegmezőbe.

    : Az **SAML felhasználói bejelentkezési attribútum** szövegmezőben adja meg az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    h. Az **SAML-Felhasználónév attribútuma** szövegmezőbe írja be az értéket `http://schemas.microsoft.com/identity/claims/displayname` .

    i. Az **SAML-felhasználó e-mail-attribútuma** szövegmezőbe írja be az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    j. Kattintson a **Mentés** gombra.

### <a name="create-sonarqube-test-user"></a>Sonarqube-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Sonarqube-ben. A [Sonarqube ügyfél-támogatási csapattal](https://www.sonarsource.com/support/) együttműködve veheti fel a felhasználókat a Sonarqube-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Sonarqube bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen közvetlenül a Sonarqube bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Sonarqube csempére kattint, a rendszer átirányítja a Sonarqube bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

* A Sonarqube konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).