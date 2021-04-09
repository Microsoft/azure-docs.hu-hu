---
title: 'Oktatóanyag: Azure Active Directory a (z) és a (z) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a legördülő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651008"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Oktatóanyag: Azure Active Directory integráció

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) segítségével. Amikor az Azure AD-vel integrálja a FAA-t, a következőket teheti:

* Hozzáférés az Azure AD-hez, amely hozzáféréssel rendelkezik a levelehoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A az **SP** által kezdeményezett egyszeri bejelentkezést támogatja

* A és a [felhasználók **automatikus** kiépítés támogatása](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>A gyűjtemény felvétele a katalógusból

A famentes integrációjának az Azure AD-be való konfigurálásához fel kell vennie a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a legördülő **listát a keresőmezőbe** .
1. Válassza **ki a** leválasztó elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Azure AD SSO konfigurálása és tesztelése a következőhöz:.

Konfigurálja és tesztelje az Azure AD SSO-t a " **B. Simon**" nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a felhasználókhoz tartozó felhasználó között.

Az Azure AD SSO és a (z) az alábbi lépések végrehajtásával konfigurálhatja és tesztelheti a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A az egyszeri bejelentkezés beállításainak **[konfigurálása](#configure-asana-sso)** az alkalmazás oldalán.
    1. A felhasználó által használt Azure AD-képviselethez kapcsolódó B. Simon-beli felhasználóhoz tartozó "a" felhasználó **[létrehozása](#create-asana-test-user)** .
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a ( **z) a**(z) a (z **) a (** z) **, a (** z), a (z), a (z)
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A legördülő tartományok és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://app.asana.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `https://app.asana.com/`

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A (z) **beállításnál adja** meg a megfelelő URL (eke) t a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a (z)-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások **listában válassza a** leválasztás lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-asana-sso"></a>Az egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a-ba. Az egyszeri bejelentkezés a következőben való konfigurálásához a képernyő jobb felső sarkában lévő munkaterület nevére kattintva nyissa meg a munkaterület beállításait. Ezután kattintson a **\<your workspace name\> Beállítások** elemre.

    ![Az egyszeri bejelentkezés beállításai](./media/asana-tutorial/settings.png)

2. A **szervezeti beállítások** ablakban kattintson az **Adminisztráció** elemre. Ezután kattintson a **tagoknak az SAML használatával történő bejelentkezéshez** az egyszeri bejelentkezés konfigurációjának engedélyezéséhez. A hajtsa végre a következő lépéseket:

    ![Önálló Sign-On szervezeti beállítások konfigurálása](./media/asana-tutorial/save.png)  

    a. A bejelentkezési **oldal URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**.

    b. Kattintson a jobb gombbal a Azure Portal letöltött tanúsítványra, majd nyissa meg a fájlt a Jegyzettömb vagy a kívánt szövegszerkesztő használatával. Másolja a tartalmat a BEGIN és a end Certificate cím között, és illessze be az **X. 509 tanúsítvány** szövegmezőbe.

3. Kattintson a **Mentés** gombra. Ha további segítségre van szüksége, nyissa meg a [következőt: az egyszeri bejelentkezés beállítása](https://asana.com/guide/help/premium/authentication#gl-saml) .

### <a name="create-asana-test-user"></a>A felhasználó létrehozása a felhasználók számára

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása A az alapértelmezés szerint engedélyezve van az automatikus felhasználó-kiépítés. További részletekért tekintse [meg az automatikus](asana-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozását látja el a (m)-ben.

1. A **következő** oldalon lépjen a **csapatok** szakaszra a bal oldali panelen. Kattintson a plusz jelre.

    ![Azure AD-tesztkörnyezet létrehozása](./media/asana-tutorial/teams.png)

2. Írja be a felhasználó e-mail-címét, például **Britta. simon \@ contoso.com** a szövegmezőbe, majd válassza a **meghívás** lehetőséget.

3. Kattintson a **Meghívás küldése** gombra. Az új felhasználó e-mailt fog kapni az e-mail-fiókjába. a felhasználónak létre kell hoznia és ellenőriznie kell a fiókot.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a (z) és a (z) és a (z), amely a bejelentkezési folyamat elindítására használható. 

* Nyissa meg a (z) a (z)-alapú bejelentkezési URL-címet, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások területén található a a legördülő csempére kattint, a rendszer átirányítja a (z)-ra. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a kiszűrése-t, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak védelmét és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).