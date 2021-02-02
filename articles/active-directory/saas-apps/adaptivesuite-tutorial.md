---
title: 'Oktatóanyag: Azure Active Directory integráció az adaptív ismeretekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az adaptív adatfelismerések között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: d9710fd37da949f9b3f51db5cda57f582802520c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254694"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Oktatóanyag: adaptív adatáttekintések integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az adaptív információkat Azure Active Directory (Azure AD) használatával. Az adaptív bepillantást az Azure AD-vel integrálva a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér az adaptív adatfelismerésekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való adaptív bepillantást.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Adaptív adatellenőrzési egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az adaptív adatfelismerések támogatják a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="add-adaptive-insights-from-the-gallery"></a>Adaptív adatáttekintések hozzáadása a katalógusból

Az adaptív betekintést az Azure AD-be való integrálásának konfigurálásához adaptív betekintést kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **adaptív bepillantást** .
1. Válassza ki az **adaptív bepillantást** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Az Azure AD SSO konfigurálása és tesztelése adaptív adatfelismerésekhez

Konfigurálja és tesztelje az Azure AD SSO-t az adaptív ismeretekkel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az adaptív megállapításokban.

Az Azure AD SSO és az adaptív adatfelismerések konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Adaptív adatáttekintések konfigurálása](#configure-adaptive-insights-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre adaptív adatellenőrzési tesztet](#create-adaptive-insights-test-user)** , hogy a B. Simon párja legyen a felhasználó Azure ad-képviseletéhez kapcsolódó adaptív adatáttekintésekben.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **adaptív** betekintő alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Az adaptív adatok **SAML SSO-beállításai** lapról lekérheti az azonosító (Entity ID) és a válasz URL-értékeit.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **adaptív adatáttekintések beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az adaptív adatfelismerésekhez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **adaptív** adatellenőrzések lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-adaptive-insights-sso"></a>Adaptív bepillantást igénylő egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az adaptív bepillantást a vállalati webhelyre rendszergazdaként.

2. Nyissa meg az **adminisztrációt**.

    ![A navigációs panelen a felügyeletet kiemelő képernyőkép.](./media/adaptivesuite-tutorial/administration.png "Rendszergazda")

3. A **felhasználók és szerepkörök** szakaszban kattintson az **SAML SSO-beállítások** elemre.

    ![SAML SSO-beállítások kezelése](./media/adaptivesuite-tutorial/settings.png "SAML SSO-beállítások kezelése")

4. Az **SAML SSO-beállítások** oldalon hajtsa végre a következő lépéseket:

    ![SAML SSO-beállítások](./media/adaptivesuite-tutorial/saml.png "SAML SSO-beállítások")

    a. Az **identitás-szolgáltató neve** szövegmezőbe írja be a konfiguráció nevét.

    b. Illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét az **Identity Provider entitás-azonosító** szövegmezőbe.

    c. Illessze be a Azure Portalba másolt **bejelentkezési URL** -címet az **Identity Provider SSO URL** szövegmezőbe.

    d. Illessze be az Azure Portalból másolt **kijelentkezési URL-címet** az **Egyéni kijelentkezési URL-** szövegmezőbe.

    e. A letöltött tanúsítvány feltöltéséhez kattintson a **fájl kiválasztása** lehetőségre.

    f. Válassza ki a következőt:

     * **SAML felhasználói azonosító**, válassza a **felhasználó adaptív elemzése felhasználónevet**.

     * Az **SAML felhasználói azonosító helye**, a **tulajdonos NameID válassza a felhasználói azonosító** elemet.

     * **SAML-NameID formátuma**, válassza az **e-mail cím** elemet.

     * **Engedélyezze az SAML**-t, válassza az **SAML egyszeri bejelentkezés engedélyezése és a közvetlen adaptív** adatellenőrzési bejelentkezés lehetőséget.

    : Másolja át az **adaptív betekintési egyszeri bejelentkezés URL-címét** , és illessze be az **azonosítót (Entity ID)** és a **Válasz URL** -szövegmezőket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    h. Kattintson a **Mentés** gombra.

### <a name="create-adaptive-insights-test-user"></a>Adaptív adatáttekintési teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az adaptív adatfelismerésbe, az adaptív adatfelismeréseknek kell kiépíteni őket. Adaptív adatmennyiség esetén a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **adaptív bepillantást** a vállalati webhelyre rendszergazdaként.

2. Nyissa meg az **adminisztrációt**.

   ![Felügyelet](./media/adaptivesuite-tutorial/administration.png "Rendszergazda")

3. A **felhasználók és szerepkörök** szakaszban kattintson a **felhasználók** elemre.

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/users.png "Felhasználó hozzáadása")

4. Az **új felhasználó** szakaszban hajtsa végre a következő lépéseket:

   ![Küldés](./media/adaptivesuite-tutorial/new.png "Küldés")

   a. Adja meg egy érvényes Azure Active Directory felhasználó **nevét**, **felhasználónevét**, **e-mail-címét** és **jelszavát** , amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

   b. Válasszon egy **szerepkört**.

   c. Kattintson a **Submit (Küldés**) gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, adaptív információkkal rendelkező felhasználói fiók létrehozására szolgáló eszközt vagy API-t is használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra az adaptív adatfelismerésbe, amelyhez be szeretné állítani az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások adaptív bepillantások csempére kattint, automatikusan be kell jelentkeznie az olyan adaptív adatfelismerésekhez, amelyekhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

Az adaptív elemzések konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
