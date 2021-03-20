---
title: 'Oktatóanyag: Azure Active Directory integráció a PagerDuty-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és PagerDuty között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 9a3117b64c516120f8556b7b63b24e5ef906f973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PagerDuty

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PagerDuty a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az PagerDuty-t az Azure AD-vel, a következőket teheti:

* A PagerDuty-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a PagerDuty az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* PagerDuty egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ha MFA-vagy jelszó-nélküli hitelesítést használ az Azure AD-ben, akkor kapcsolja ki a AuthnContext értéket az SAML-kérelemben. Ellenkező esetben az Azure AD a hibát nem egyezik a AuthnContext, és nem küldi vissza a jogkivonatot az alkalmazásnak.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A PagerDuty támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-pagerduty-from-the-gallery"></a>PagerDuty hozzáadása a gyűjteményből

A PagerDuty Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PagerDuty a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PagerDuty** kifejezést a keresőmezőbe.
1. Válassza ki a **PagerDuty** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a PagerDuty

Konfigurálja és tesztelje az Azure AD SSO-t a PagerDuty a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a PagerDuty-ben.

Az Azure AD SSO és a PagerDuty konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[PAGERDUTY SSO konfigurálása](#configure-pagerduty-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre PagerDuty-teszt felhasználót](#create-pagerduty-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-PagerDuty rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **PagerDuty** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.pagerduty.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.pagerduty.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a PagerDuty ügyfélszolgálati [csapatához](https://www.pagerduty.com/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PagerDuty beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a PagerDuty.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **PagerDuty** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-pagerduty-sso"></a>PagerDuty SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Pagerduty vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások** lehetőségre.

    ![Fiókbeállítások](./media/pagerduty-tutorial/ic778535.png "Fiókbeállítások")

3. Kattintson az **egyszeri bejelentkezés** elemre.

    ![Egyszeri bejelentkezés](./media/pagerduty-tutorial/ic778536.png "Egyszeri bejelentkezés")

4. Az **egyszeri bejelentkezés engedélyezése (SSO)** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés engedélyezése](./media/pagerduty-tutorial/ic778537.png "Egyszeri bejelentkezés engedélyezése")

    a. Nyissa meg Azure Portal a Jegyzettömbben letöltött Base-64 kódolású tanúsítványt, másolja ki a tartalmát a vágólapra, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.
  
    b. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .
  
    c. A **kijelentkezési URL** szövegmezőbe illessze be az Azure Portalból másolt **KIJELENTKEZÉSI URL-címet** .

    d. Válassza a **Felhasználónév/jelszó bejelentkezés engedélyezése** lehetőséget.

    e. Jelölje be a **hitelesítési környezet pontos összehasonlításának megkövetelése** jelölőnégyzetet.

    f. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-pagerduty-test-user"></a>PagerDuty-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a PagerDuty, a PagerDuty kell kiépíteni őket. PagerDuty esetén a kiépítés manuális feladat.

> [!NOTE]
> A Pagerduty által biztosított egyéb Pagerduty-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Pagerduty** -bérlőbe.

2. A felső menüben kattintson a **felhasználók** elemre.

3. Kattintson a **felhasználók hozzáadása** elemre.
   
    ![Felhasználók hozzáadása](./media/pagerduty-tutorial/ic778539.png "Felhasználók hozzáadása")

4.  A **csapat meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![A csapat meghívása](./media/pagerduty-tutorial/ic778540.png "A csapat meghívása")

    a. Írja be a felhasználó vezetéknevét **és vezetéknevét** , például **B. Simon**. 
   
    b. Adja meg a felhasználó **e-mail-** címét, például: **b. Simon \@ contoso.com**.
   
    c. Kattintson a **Hozzáadás**, majd a **Meghívók küldése** elemre.
   
    > [!NOTE]
    > Az összes hozzáadott felhasználó meghívást kap egy PagerDuty-fiók létrehozásához.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a PagerDuty bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a PagerDuty bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások PagerDuty csempére kattint, a rendszer átirányítja a PagerDuty bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A PagerDuty konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).