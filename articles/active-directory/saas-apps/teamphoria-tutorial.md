---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Teamphoria | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Teamphoria között.
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
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92504745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Teamphoria

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Teamphoria a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Teamphoria-t az Azure AD-vel, a következőket teheti:

* A Teamphoria-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Teamphoria az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Teamphoria egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Teamphoria támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria hozzáadása a gyűjteményből

A Teamphoria Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Teamphoria a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Teamphoria** kifejezést a keresőmezőbe.
1. Válassza ki a **Teamphoria** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Teamphoria

Konfigurálja és tesztelje az Azure AD SSO-t a Teamphoria a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Teamphoria-ben.

Az Azure AD SSO és a Teamphoria konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[TEAMPHORIA SSO konfigurálása](#configure-teamphoria-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Teamphoria-teszt felhasználót](#create-teamphoria-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Teamphoria rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Teamphoria** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a Teamphoria ügyfélszolgálati [csapatához](https://www.teamphoria.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Teamphoria beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Teamphoria.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Teamphoria** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-teamphoria-sso"></a>Teamphoria SSO konfigurálása

1. A Teamphoria belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Teamphoria beállítása** elemre, majd a Teamphoria alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Teamphoria való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Teamphoria, nyisson meg egy új böngészőablakot, és jelentkezzen be a Teamphoria vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A bal oldali eszköztáron kattintson a **rendszergazdai beállítások** lehetőségre, majd a konfigurálás lapon az **egyszeri bejelentkezés** elemre kattintva nyissa meg az egyszeri bejelentkezés konfigurációs ablakát.

    ![A képernyőképen a rendszergazdai beállítások láthatók, ahol kiválaszthatja az egyszeri bejelentkezés lehetőséget.](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Kattintson a jobb felső sarokban található **új identitás-szolgáltató hozzáadása** lehetőségre, hogy megnyissa az SSO beállításainak hozzáadására szolgáló űrlapot.

    ![Képernyőfelvétel: Itt választhatja ki az új identitás-szolgáltató hozzáadása lehetőséget.](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Adja meg a részleteket a mezőkben az alább leírtak szerint:

    ![Képernyőfelvétel: az a lap, amelyen megadhatja a leírt értékeket.](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Megjelenítendő név**: adja meg a beépülő modul megjelenítendő nevét a rendszergazda lapon.

    b. **Gomb neve**: annak a lapnak a neve, amely a bejelentkezési oldalon fog megjelenni az SSO-n keresztül történő bejelentkezéshez.

    c. **Tanúsítvány**: Nyissa meg a korábban letöltött tanúsítványt a Azure Portal a Jegyzettömbben, másolja a tartalmát, és illessze be ide a mezőbe.

    d. **Belépési pont**: illessze be a korábban a Azure Portalból másolt **bejelentkezési URL-címet** .

    e. Váltson **a be lehetőségre, majd kattintson** a **Mentés** gombra.

### <a name="create-teamphoria-test-user"></a>Teamphoria-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Teamphoria, a Teamphoria-ben kell kiépíteni őket. Teamphoria esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Teamphoria vállalati webhelyre rendszergazdaként.

1. Kattintson a **rendszergazdai** beállítások elemre a bal oldali eszköztáron, majd a **kezelés** lapon kattintson a **felhasználók** elemre a felhasználók rendszergazdai oldalának megnyitásához.

    ![Alkalmazott hozzáadása](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kattintson a **manuális meghívó** lehetőségre.

    ![A képernyőképen a manuális Meghívási lehetőség látható.](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Ezen az oldalon hajtsa végre a következő műveletet.

    ![Képernyőfelvétel: a felhasználó manuális meghívására szolgáló oldal, ahol megadhatja a név és az e-mail címet.](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Az **E-mail cím** szövegmezőbe írja be annak a felhasználónak az **e-mail címét** , mint a B. Simon.

    b. Az **Utónév** szövegmezőbe írja be a (z) " **B**" nevű felhasználó utónevét.

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    d. Kattintson az **1. meghívás felhasználó** elemre. A felhasználónak el kell fogadnia a meghívást, hogy létre lehessen hozni a rendszerből.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Teamphoria csempére kattint, automatikusan be kell jelentkeznie arra a Teamphoria, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Teamphoria kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)