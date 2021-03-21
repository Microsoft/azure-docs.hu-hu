---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Watch by Colors használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a színek közötti megtekintéssel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636782"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Watch by Colors használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Watch by Colors szolgáltatást Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Watch by Colors szolgáltatást, a következőket teheti:

* Az Azure AD-ben való vezérlés, amely a színek megtekintésével fér hozzá.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával megtekintse a színeket.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Tekintse meg a színek egyszeri bejelentkezés (SSO) engedélyezett előfizetését.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Watch by Colors az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja

## <a name="adding-watch-by-colors-from-the-gallery"></a>A megtekintés színekkel való hozzáadása a gyűjteményből

Ha a Watch by Colors az Azure AD-be való integrálását szeretné beállítani, akkor a katalógusban szereplő színeket a felügyelt SaaS-alkalmazások listájára kell felvennie.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Megtekintés szín szerint** kifejezést.
1. Válassza a **Megtekintés szín** alapján az eredmények panelen, majd az alkalmazás hozzáadása lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Watch by Colors használatával

Konfigurálja és tesztelje az Azure AD SSO-t a Watch by Colors használatával, a " **B. Simon**" nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Watch by Colors használatával.

Ha az Azure AD SSO-t a Watch by Colors használatával szeretné konfigurálni és tesztelni, hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Watch by Colors SSO konfigurálása](#configure-watch-by-colors-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Tekintse meg a Watch by Colors test User](#create-watch-by-colors-test-user)** -to have a "B. Simon in Watch" című részt, amely a felhasználó Azure ad-képviseletéhez kapcsolódó színeket tartalmaz.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Watch by Colors** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://app.colorscorporation.com/login`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Watch by Colors szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Megtekintés szín alapján** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-watch-by-colors-sso"></a>A Watch by Colors SSO konfigurálása

1. Ha a színek alapján szeretné automatizálni a konfigurációt, akkor a **bővítmény telepítése** lehetőségre kattintva telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, a **Coloring Watch by Colors** (megtekintés a Colors alkalmazásban) lehetőségre kattintva megnyithatja az órát. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy bejelentkezzen a Watch by Colors értékre. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az órát, nyisson meg egy új böngészőablakot, és jelentkezzen be a Watch by Colors vállalati webhely rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső sarkában kattintson a **profil**  >  **Fiókbeállítások**  >  **egyszeri bejelentkezés (egyszeri bejelentkezés)** elemre.

    ![Képernyőfelvétel: a Fiókbeállítások oldal, ahol az S S O le van tiltva.](./media/watch-by-colors-tutorial/config01.png)

5. Az **egyszeri bejelentkezés (SSO)** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML beállítása lap, amelyen engedélyezheti az SAML-t.](./media/watch-by-colors-tutorial/config02.png)

    a. Az **SAML engedélyezése** bekapcsolva **értékre**.

    b. Az **URL-cím** szövegmezőbe illessze be az **összevonási metaadatok URL-címét**, amelyet a Azure Portalból másolt.

    c. Kattintson az **Importálás** elemre, majd az alábbi mezők automatikusan kitöltik a lapon lévő automatikus kitöltést.

    d. Kattintson a **Mentés** gombra.

### <a name="create-watch-by-colors-test-user"></a>Tekintse meg a Watch by Colors test User

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a színek megtekintésére, a színek szerint kell azokat kiépíteni. A Watch by Colors szolgáltatásban a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. A színek biztonsági rendszergazdaként való megtekintéséhez jelentkezzen be.

1. A lap jobb felső sarkában kattintson a **profil**  >  **felhasználók**  >  **felhasználó hozzáadása** elemre.

    ![Képernyőfelvétel: a felhasználók lapot jeleníti meg.](./media/watch-by-colors-tutorial/config03.png)

1. A **felhasználó adatai** oldalon hajtsa végre a következő lépéseket:

    ![A képernyőképen a felhasználói adatok láthatók, ahol megadhatja a leírt értékeket.](./media/watch-by-colors-tutorial/config04.png)

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail** szövege szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `B.Simon@contoso.com` .

    d. A **jelszó** szövegmezőbe írja be a jelszót.

    e. Válassza ki a **fiók engedélyeit** a szervezete számára.

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a megtekintés a színek csempére kattint, a rendszer automatikusan bejelentkezik a Figyelésbe olyan színekkel, amelyekhez az egyszeri bejelentkezést beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a színeket az Azure AD-vel](https://aad.portal.azure.com/)