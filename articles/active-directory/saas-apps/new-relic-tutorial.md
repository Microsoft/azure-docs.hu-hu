---
title: 'Oktatóanyag: Azure Active Directory integráció az új ereklye fiókkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az új ereklye között fiókkal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: cd438609a4ba2c3eb025226c98eec79fd855c075
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390257"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye fiókkal

Ez az oktatóanyag azt ismerteti, hogyan integrálható az új ereklye a Azure Active Directory (Azure AD) fiókkal. Ha az Azure AD-vel fiókkal integrálja az új ereklye-t, a következőket teheti:

* Vezérlés az Azure AD-ben, amely fiókkal fér hozzá az új ereklye szolgáltatáshoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az új Ereklyebe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Új ereklye a fiók egyszeri bejelentkezés (SSO) engedélyezett előfizetése alapján.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az új ereklye fiók támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-new-relic-by-account-from-the-gallery"></a>Új ereklye hozzáadása fiók alapján a katalógusból

Az új ereklye az Azure AD-be való integrálásának konfigurálásához új Ereklyeet kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az új ereklye a következőt **: fiók** .
1. Válassza ki az **új ereklye a fiókból** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Az Azure AD SSO konfigurálása és tesztelése az új ereklye számára fiók alapján

Konfigurálja és tesztelje az Azure AD SSO-t új ereklye fiókkal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az új ereklye fiókkal.

Az Azure AD SSO az új ereklye fiókkal való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Új ereklye konfigurálása fiók egyszeri bejelentkezéssel](#configure-new-relic-by-account-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    * **[Hozzon létre egy új ereklyeet a fiók tesztelése felhasználóval](#create-new-relic-by-account-test-user)** , hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó fiókkal rendelkezzen az új ereklye alapján.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **új ereklye a fiók** alkalmazás-integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)
   
1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` -Ne felejtse el helyettesíteni az `acc_id` új ereklye fiók azonosítóját.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `rpm.newrelic.com`

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **új ereklye fiók beállítása alapján** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az új ereklye fiókhoz való hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **új ereklye a fiók alapján** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-new-relic-by-account-sso"></a>Új ereklye konfigurálása fiók egyszeri bejelentkezéssel

1. Egy másik böngészőablakban jelentkezzen be az **új ereklye** helyre a fiók vállalati webhelyén rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások** lehetőségre.
   
    ![Képernyőfelvétel: a kiválasztott fiók beállításait tartalmazó üdvözlő oldal.](./media/new-relic-tutorial/settings.png "Fiókbeállítások")

3. Kattintson a **Biztonság és hitelesítés** lapra, majd kattintson az **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés](./media/new-relic-tutorial/single-sign-on-tab.png "Egyszeri bejelentkezés")

4. Az SAML párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. A letöltött Azure Active Directory-tanúsítvány feltöltéséhez kattintson a **fájl kiválasztása** lehetőségre.

    b. A **távoli bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.
   
    c. A **kijelentkezési URL-cím** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    d. Kattintson **a módosítások mentése** gombra.

### <a name="create-new-relic-by-account-test-user"></a>Új ereklye létrehozása fiók tesztelése felhasználó alapján

1. Jelentkezzen be az **új ereklyebe a fiók** vállalati webhelyén rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások** lehetőségre.
   
    ![A képernyőképen az üdvözlőlapon kiválasztott Fiókbeállítások láthatók.](./media/new-relic-tutorial/account.png "Fiókbeállítások")

3. A bal oldali **fiók** ablaktáblán kattintson az **Összefoglalás**, majd a **felhasználó hozzáadása** elemre.
   
    ![Képernyőfelvétel: az összefoglalás panel, ahol kiválaszthatja a felhasználó hozzáadása elemet.](./media/new-relic-tutorial/add.png "Fiókbeállítások")

4. Az **aktív felhasználók** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Aktív felhasználók](./media/new-relic-tutorial/user.png "Aktív felhasználók")
   
    a. Az **e-mail** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory-felhasználó e-mail-címét.

    b. A **szerepkört** válassza a **felhasználó** lehetőségre.

    c. Kattintson **a felhasználó hozzáadása** elemre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a fiókhoz tartozó felhasználói fiók létrehozása vagy az új ereklye által biztosított API-k segítségével bármilyen más új ereklye is használható.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja az új ereklye értékre a fiók bejelentkezési URL-címével, amelyen kezdeményezheti a bejelentkezési folyamatot. 

* Az új ereklye a fiók bejelentkezési URL-címe közvetlenül, a bejelentkezési folyamat elindítása innen.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások fiókjában az új ereklye lehetőségre kattint, ez a fiók bejelentkezési URL-címén át lesz irányítva az új ereklye értékre. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

Az új ereklye fiókkal való konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
