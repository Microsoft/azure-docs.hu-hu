---
title: 'Oktatóanyag: Azure Active Directory integráció az értékeléssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és értékelt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457860"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Oktatóanyag: az értékelés integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az értékelést Azure Active Directory (Azure AD) használatával. Az Azure AD-vel való értékelésének integrálásakor a következőket teheti:

* A kiértékeléshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való értékeléshez.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kiértékelt egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. **Az értékelés az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-appraisd-from-the-gallery"></a>Értékelés hozzáadása a katalógusból

Az Azure AD-ba való kiértékelésének konfigurálásához hozzá kell adnia az értékelést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **értékelés** kifejezést.
1. Válassza az **értékelés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a kiértékeléssel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az értékelés során.

Az Azure AD SSO az értékeléssel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja az értékelést](#configure-appraisd)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy értékelt tesztelési felhasználót](#create-appraisd-test-user)** , hogy a B. Simon egy, a felhasználó Azure ad-beli képviseletéhez kapcsolódó.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **kiértékelt** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a Save (Mentés) gombra kattintva, és végre kell hajtania a következő lépéseket:

    a. Kattintson a **további URL-címek beállítása** elemre.

    b. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `<TENANTCODE>`

    c. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, a **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > A tényleges bejelentkezési URL-címet és a továbbítási állapot értékét az oktatóanyag későbbi részében ismertetett SSO-konfiguráció lapon olvashatja.

1. Az értékelt alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. Az értékelt alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és módosítja az attribútum-hozzárendelést.

    ![Képernyőfelvétel: a felhasználói attribútumok ablaktábla, ahol a Szerkesztés ikon ki van emelve.](common/edit-attribute.png)

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **beállított értékelési** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Értékelés konfigurálása

1. Az értékelésen belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, a **telepítő által kiértékelt** lehetőségre kattintva a rendszer átirányítja Önt az értékelt alkalmazásra. Itt adja meg a rendszergazdai hitelesítő adatokat az értékelésbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az értékelést, nyisson meg egy új böngészőablakot, és jelentkezzen be az értékelt vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső részén kattintson a **Beállítások** ikonra, majd navigáljon a **konfiguráció** elemre.

    ![A képernyőképen a konfigurációs hivatkozás látható.](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. A menü bal oldalán kattintson az **SAML egyszeri bejelentkezés** elemre.

    ![A képernyőfelvételen az SAML egyszeri bejelentkezés lehetőségre Kiemelt konfigurációs beállítások láthatók.](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Az **SAML 2,0 Single Sign-On Configuration** lapon hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az SAML 2,0-alapú Sign-On konfigurációs oldal látható, ahol szerkesztheti az alapértelmezett Relay-állapotot és a szolgáltatás által kezdeményezett bejelentkezés U R L-t.](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Másolja az **alapértelmezett Relay State** értéket, és illessze be a **továbbítási állapot** szövegmezőbe a Azure Portal **alapszintű SAML-konfigurációjában** .

    b. Másolja a **szolgáltatás által kezdeményezett bejelentkezési URL-címet** , és illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal **alapszintű SAML-konfigurációjában** .

7. Görgessen le ugyanazon az oldalon a **felhasználók azonosítása** területen, hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználók azonosítására szolgáló hely, ahol megadhatja a lépés értékeit.](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Az **Identitáskezelő egyetlen Sign-On URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt, majd kattintson a Save ( **Mentés**) gombra.

    b. Az **Identity Provider kiállító URL-címe** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt, majd kattintson a Save ( **Mentés**) gombra.

    c. Nyissa meg a Jegyzettömbben a Azure Portal letöltött Base-64 kódolású tanúsítványt, másolja a tartalmát, majd illessze be az **X. 509 tanúsítvány** mezőbe, és kattintson a **Save (Mentés**) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B. Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az értékeléshez való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **értékelés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-appraisd-test-user"></a>Értékelt tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az értékelésbe, azokat kiértékeléssel kell kiépíteni. A kiértékelés során a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a biztonsági rendszergazdaként való kiértékeléshez.

2. A lap jobb felső részén kattintson a **Beállítások** ikonra, majd navigáljon a **felügyeleti központ** elemre.

    ![Képernyőfelvétel: a beállítások lehetőség, ahol kiválaszthatja a felügyeleti központ elemet.](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. A lap tetején található eszköztáron kattintson a **People (személyek**) elemre, majd navigáljon **új felhasználó hozzáadásához**.

    ![Képernyőfelvétel: a kiértékelt lap az emberekkel, és új felhasználó hozzáadása.](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Az **új felhasználó hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![A képernyőképen az új felhasználó hozzáadása lap látható.](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    c. Az **e-mail** szövege szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `B. Simon@contoso.com` .

    d. Kattintson a **felhasználó hozzáadása** elemre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen kijelöli a kiértékelt csempét, akkor automatikusan be kell jelentkeznie arra a kiértékelésre, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)