---
title: 'Oktatóanyag: Azure Active Directory a | One-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Azure Active Directory One között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516829"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Oktatóanyag: Azure Active Directory A Következővel való integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Azure Active Directory (Azure AD)-t. Ha az Azure AD-val integrálja AZen One-t, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a Következő 1-hez:
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Tokzen One-ba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Egy egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Deszen One támogatja **az idP által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-clarizen-one-from-the-gallery"></a>A Galleryből származó One-fiók hozzáadása a Következő katalógushoz:

Ahhoz, hogy az Azure AD-be integrálni tudja a Ofzen One-t, fel kell vennie a Listába a Listából a Listából a Felügyelt SaaS-alkalmazásokat.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a Keresőmezőbe a Következőt: < A1/2012 mezőbe írja be a Következőt: **<**
1. Az **eredménypanelen válassza a Andzen One** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Az Azure AD SSO konfigurálása és tesztelése a One-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Fogzen One-nal egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Tozen One-ban.

Az Azure AD SSO a Következő lépésekkel konfigurálható és tesztelható a Következő Módon:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az egyszeri bejelentkezési beállítások](#configure-clarizen-one-sso)** alkalmazásoldalon való konfigurálásához állítson be Egy SSO-t a Következőre:
    1. **[Hozzon létre Egy Tesztfelhasználót](#create-clarizen-one-test-user)** , hogy legyen egy megfelelője a B.Simonnak a One-ban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal Lapon keresse meg a **Kezelés** szakaszt,  és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. A Set up Single Sign-On with SAML (Egyetlen Sign-On **beállítása SAML-sel)** lapon hajtsa végre a következő lépéseket:

    a. Az Azonosító **szövegmezőbe** írja be a következő értéket: `Clarizen`

    b. A Válasz **URL-cím** szövegmezőbe írja be az URL-címet: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-sel) lap **SAML** aláíró tanúsítvány szakaszában kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

6. A **Set up [ One (Első** hely beállítása) szakaszban másolja ki a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát úgy, hogy hozzáférést biztosít a Tozen One-hoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Következő: One lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az &quot;Alapértelmezett hozzáférés&quot; szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name=&quot;configure-clarizen-one-sso&quot;></a>A Deszen One SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Vállalati One-webhelyre.

1. Kattintson a felhasználónevére, majd a **Beállítások elemre.**

    ![A felhasználónév alatt kattintson a &quot;Beállítások&quot; elemre](./media/clarizen-tutorial/setting.png &quot;Beállítások")

1. Kattintson **a Globális beállítások fülre.** Ezután az Összevont hitelesítés **mellett kattintson** a **szerkesztés parancsra.**

    !["Globális beállítások" lap](./media/clarizen-tutorial/authentication.png "Globális beállítások")

1. Az **Összevont hitelesítés párbeszédpanelen** hajtsa végre a következő lépéseket:

    !["Összevont hitelesítés" párbeszédpanel](./media/clarizen-tutorial/federated-authentication.png "Összevont hitelesítés")

    a. Válassza **az Összevont hitelesítés engedélyezése lehetőséget.**

    b. Kattintson **a Feltöltés** gombra a letöltött tanúsítvány feltöltéséhez.

    c. A **Bejelentkezési URL-cím** mezőbe írja be a Bejelentkezési **URL-cím** értéket az Azure AD alkalmazáskonfigurációs ablakából.

    d. A **Kijelentkezés URL-címe** mezőbe írja be a Kijelentkezés **URL-címe** értéket az Azure AD alkalmazáskonfigurációs ablakából.

    e. Válassza **a POST használata** lehetőséget.

    f. Kattintson a **Mentés** gombra.

### <a name="create-clarizen-one-test-user"></a>Egy tesztfelhasználó létrehozása a Deszenben

Ennek a szakasznak az a célja, hogy létrehoz egy Britta Simon nevű felhasználót a One-ban.

**Ha manuálisan kell létrehoznia egy felhasználót, hajtsa végre a következő lépéseket:**

Ahhoz, hogy az Azure AD-felhasználók bejelentkezni tudjanak a Tolzen One-ba, felhasználói fiókokat kell kiépítenie. A Kiszen One esetében a kiépítés manuális feladat.

1. Jelentkezzen be a Vállalati One-webhelyre rendszergazdaként.

2. Kattintson a **Személyek elemre.**

    ![A "Személyek" gombra kattintva](./media/clarizen-tutorial/people.png "People")

3. Kattintson **a Felhasználó meghívása elemre.**

    !["Felhasználó meghívása" gomb](./media/clarizen-tutorial/user.png "Felhasználók meghívása")

1. A Személyek **meghívása párbeszédpanelen** hajtsa végre a következő lépéseket:

    !["Személyek meghívása" párbeszédpanel](./media/clarizen-tutorial/invite-people.png "Személyek meghívása")

    a. Az **E-mail mezőbe** írja be Britta Simon-fiók e-mail-címét.

    b. Kattintson **a Meghívás gombra.**

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos e-mailt fog kapni, és egy hivatkozással megerősíti a fiókját, mielőtt az aktívvá válik.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját.

* Kattintson a Test this application in Azure Portal (Az alkalmazás tesztelése a Azure Portal lehetőségre, és automatikusan be kell majd jelentkezve lennie a Azure Portal Zen One-be, amelyhez beállította az SSO-t.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Saját alkalmazások LapkaCsempére kattint, automatikusan be kell jelentkezve lennie arra a Saját alkalmazások Zen One-ra, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a One-t, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)