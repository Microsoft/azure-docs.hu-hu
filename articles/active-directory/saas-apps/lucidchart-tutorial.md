---
title: 'Oktatóanyag: Azure Active Directory integráció a LucidChart-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és LucidChart között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LucidChart

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LucidChart a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az LucidChart-t az Azure AD-vel, a következőket teheti:

* A LucidChart-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a LucidChart az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* LucidChart egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A LucidChart támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A LucidChart **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="add-lucidchart-from-the-gallery"></a>LucidChart hozzáadása a gyűjteményből

A LucidChart Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LucidChart a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LucidChart** kifejezést a keresőmezőbe.
1. Válassza ki a **LucidChart** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Azure AD SSO konfigurálása és tesztelése a LucidChart-hez

Konfigurálja és tesztelje az Azure AD SSO-t a LucidChart a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a LucidChart-ben.

Az Azure AD SSO és a LucidChart konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[LUCIDCHART SSO konfigurálása](#configure-lucidchart-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre LucidChart-teszt felhasználót](#create-lucidchart-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-LucidChart rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **LucidChart** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

   A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következőként:  `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **LucidChart beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a LucidChart.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LucidChart** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-lucidchart-sso"></a>LucidChart SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LucidChart vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **csapat** elemre.

    ![Team](./media/lucidchart-tutorial/ic791190.png "Csoport")

3. Kattintson az **alkalmazások az \> SAML kezelése** lehetőségre.

    ![SAML kezelése](./media/lucidchart-tutorial/ic791191.png "SAML kezelése")

4. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Jelölje be az **SAML-hitelesítés engedélyezése** jelölőnégyzetet, majd kattintson a nem **kötelező** elemre.

    ![SAML-hitelesítési beállítások](./media/lucidchart-tutorial/ic791192.png "SAML-hitelesítési beállítások")

    b. A **tartomány** szövegmezőbe írja be a tartományt, majd kattintson a **tanúsítvány módosítása** elemre.

    ![Tanúsítvány módosítása](./media/lucidchart-tutorial/ic791193.png "Tanúsítvány módosítása")

    c. Nyissa meg a letöltött metaadat-fájlt, másolja a tartalmat, majd illessze be a **metaadatok feltöltése** szövegmezőbe.

    ![Metaadatok feltöltése](./media/lucidchart-tutorial/ic791194.png "Metaadatok feltöltése")

    d. Válassza **az új felhasználók automatikus hozzáadása a csoporthoz** lehetőséget, majd kattintson a **módosítások mentése** gombra.

    ![Módosítások mentése](./media/lucidchart-tutorial/ic791195.png "Módosítások mentése")

### <a name="create-lucidchart-test-user"></a>LucidChart-tesztelési felhasználó létrehozása

Nincs olyan műveleti tétel, amellyel a felhasználó kiépíthető a LucidChart.  Amikor egy hozzárendelt felhasználó megpróbál bejelentkezni a LucidChart a hozzáférési panel használatával, a LucidChart ellenőrzi, hogy a felhasználó létezik-e.  

Ha még nem érhető el felhasználói fiók, a rendszer automatikusan létrehozza a LucidChart.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a LucidChart bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a LucidChart bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások LucidChart csempére kattint, automatikusan be kell jelentkeznie arra a LucidChart, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

 A LucidChart konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)