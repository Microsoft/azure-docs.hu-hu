---
title: 'Oktatóanyag: Azure Active Directory integráció a következőképpen: Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és így.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608935"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Oktatóanyag: a Azure Active Directory integrálásának módja

Ebből az oktatóanyagból megtudhatja, hogyan integrálható a Azure Active Directory (Azure AD) használatával. Ha integrálja az Azure AD-t, a következőket teheti:

* Vezérlés az Azure AD-ben, hogy ki férhet hozzá.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetésének módja.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az **SP** által kezdeményezett egyszeri bejelentkezés támogatása
* A felhasználói üzembe helyezést **csak időben** támogatjuk

## <a name="adding-way-we-do-from-the-gallery"></a>A katalógusból való hozzáadás módja

Az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához való csatlakozás módját.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **keresőmezőbe a kifejezést** .
1. Válassza ki az eredmények **panelen az alkalmazás** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t úgy, hogy egy **B. Simon** nevű teszt felhasználót használ. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a következő módon:.

Az Azure AD SSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. Az egyszeri **[Bejelentkezés](#configure-way-we-do-sso)** konfigurálása – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre módszert a felhasználó teszteléséhez](#create-way-we-do-test-user)** , hogy a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **az alkalmazás-** integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Kapcsolatfelvétel az [ügyfél-támogatási csapattal](mailto:support@waywedo.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **beállítható módon végezze** el a megfelelő URL-cím (eke) t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurálás módja az egyszeri bejelentkezéshez

1. A konfiguráció automatizálása érdekében az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva kell telepítenie.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés lehetőségre** , hogy a rendszer az alkalmazás módját irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a telepítést, nyisson meg egy új böngészőablakot, és jelentkezzen be a céges portált rendszergazdaként, és végezze el a következő lépéseket:

1. Kattintson az egyik oldal jobb felső sarkában található **személy ikonra** , és a legördülő menüben kattintson a **fiók** lehetőségre.

    ![Fiók létrehozása](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. A **menü ikonra** kattintva nyissa meg a leküldéses navigációs menüt, és kattintson az **egyszeri bejelentkezés** lehetőségre.

    ![Egyetlen lehetőség](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Az **egyszeri bejelentkezés beállítása** oldalon hajtsa végre a következő lépéseket:

    ![A Mentés módja](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Az egyszeri bejelentkezés engedélyezéséhez kattintson az **egyszeri bejelentkezés bekapcsolása** **Igen** értékre.

    1. Az **egyszeri bejelentkezés neve** szövegmezőbe írja be a nevét.

    1. Az **entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    1. Az **SAML SSO URL** szövegmezőbe illessze be a **bejelentkezési URL**-címet, amelyet a Azure Portal másolt.

    1. Töltse fel a tanúsítványt a **tanúsítvány** melletti **kiválasztás** gombra kattintva.

    1. **Választható beállítások** -

        * Jelszavak engedélyezése – ha ez a beállítás le van tiltva, a szokásos jelszó-függvények lehetővé teszik, hogy a felhasználók csak az egyszeri bejelentkezést használják.

        * Automatikus kiépítés engedélyezése – ha ez engedélyezve van, a bejelentkezéshez használt e-mail-cím automatikusan össze lesz hasonlítva a felhasználók listájával. Ha az e-mail-cím nem egyezik az aktív felhasználóval, a rendszer automatikusan új felhasználói fiókot ad hozzá a bejelentkezett személyhez, és a hiányzó információkat kéri.

          > [!NOTE]
          > Az egyszeri bejelentkezéssel hozzáadott felhasználók általános felhasználókként lesznek hozzáadva, és a rendszer nem kap szerepkört a rendszeren. A rendszergazdák megtekinthetik és módosíthatják a biztonsági szerepkört szerkesztőként vagy rendszergazdaként, és egy vagy több szervezeti diagram szerepkört is hozzárendelhet.

    1. Kattintson a **Save (Mentés** ) gombra a beállítások megőrzése érdekében.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **módját**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-way-we-do-test-user"></a>Módszer létrehozása a felhasználó teszteléséhez

Ebben a szakaszban a Britta Simon nevű felhasználó létrehozása folyamatban van. Az igény szerinti felhasználói kiépítés támogatása, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik, akkor a hitelesítés után létrejön egy újat.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon az [ügyfél-támogatási csapathoz](mailto:support@waywedo.com).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha bejelöli a csempék bejelölésének módját a hozzáférési panelen, automatikusan be kell jelentkeznie arra, hogy milyen módon állítson be egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)