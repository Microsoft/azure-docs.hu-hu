---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a hevederrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a hám között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: d6a6c8b49582b34c2603e0ddf78b76736f97c183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a hevederrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a hevedert Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a hevedert, a következőket teheti:

* Az Azure AD-ben való hozzáférés-vezérlési szolgáltatás.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való kihasználása érdekében.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A hám támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO

## <a name="adding-harness-from-the-gallery"></a>Hám hozzáadása a gyűjteményből

A heveder Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **hám** a keresőmezőbe.
1. Válassza ki a **hám** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a hám számára

Konfigurálja és tesztelje az Azure AD SSO-t a hám használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között.

Az Azure AD SSO és a hám használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[HÁM SSO konfigurálása](#configure-harness-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy felhasználói hám-tesztelési felhasználót](#create-harness-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-ügyfélként.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **hám** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://app.harness.io/`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A tényleges válasz URL-címét a következő oktatóanyagban ismertetett, a **hám egyszeri bejelentkezésének konfigurálása** című szakaszban találja. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A készlet beállítása szakaszban másolja **ki** a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a hám számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **hám** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-harness-sso"></a>A hám SSO konfigurálása

1. Ha automatizálni szeretné a konfigurációt a hevederen belül, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítő hám** lehetőségre a hám alkalmazásban. Itt adja meg a rendszergazdai hitelesítő adatokat a rendszerbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a hevedert, nyisson meg egy új böngészőablakot, és jelentkezzen be a vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső részén kattintson a **folyamatos biztonsági**  >  **hozzáférés-kezelési**  >  **hitelesítési beállítások** elemre.

    ![A "hozzáférés-kezelés" és a "hitelesítési beállítások" beállítással rendelkező "folyamatos biztonság" menüt megjelenítő képernyőkép.](./media/harness-tutorial/configure01.png)

5. Az **SSO-szolgáltatók** szakaszban kattintson az SSO- **szolgáltatók**  >  **SAML** hozzáadása lehetőségre.

    ![Képernyőfelvétel: "S S O Providers" a "+ Add S S O Providers-S A M L" kiválasztva.](./media/harness-tutorial/configure03.png)

6. Az **SAML-szolgáltató** előugró ablakában hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely megjeleníti az "U R L" és a "megjelenítendő név" mezőket, valamint a "fájl kiválasztása" és a "submit" gombokat.](./media/harness-tutorial/configure02.png)

    a. Másolja be az **SSO-szolgáltatót, engedélyezze az SAML-alapú bejelentkezést, majd adja meg a következő URL-példányt,** és illessze be a válasz URL-címe szövegmezőbe a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

    b. A **megjelenítendő név** szövegmezőbe írja be a megjelenítendő nevet.

    c. Kattintson a **fájl kiválasztása** lehetőségre az összevonási metaadatok XML-fájljának feltöltéséhez, amelyet az Azure ad-ből töltött le.

    d. Kattintson a **Submit (Küldés**) gombra.

### <a name="create-harness-test-user"></a>Hám tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a kihasználni, ki kell építeni őket a kiépítés során. A-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a kihasználni rendszergazdaként.

1. A lap jobb felső részén kattintson a **folyamatos biztonsági**  >  **hozzáférés-kezelő**  >  **felhasználók** elemre.

    ![Képernyőkép, amely a "hozzáférés-kezelés" és a "felhasználók" beállítású "folyamatos biztonság" menüt jeleníti meg.](./media/harness-tutorial/configure04.png)

1. A lap jobb oldalán kattintson a **+ felhasználó hozzáadása** lehetőségre.

    ![Képernyőkép, amely a "felhasználók" lapot jeleníti meg a "+ felhasználó hozzáadása" művelet kiválasztásával.](./media/harness-tutorial/configure05.png)

1. A **felhasználó hozzáadása** előugró ablakban végezze el a következő lépéseket:

    ![A hám konfigurálása](./media/harness-tutorial/configure06.png)

    a. Az **e-mail-cím (ek)** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `B.simon@contoso.com` .

    b. Válassza ki a **felhasználói csoportokat**.

    c. Kattintson a **Submit (Küldés**) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a hám csempére kattint, automatikusan be kell jelentkeznie arra a hevederre, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A hám kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)