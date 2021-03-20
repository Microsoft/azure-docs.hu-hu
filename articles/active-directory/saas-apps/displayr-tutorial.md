---
title: 'Oktatóanyag: Azure Active Directory integráció a deplayerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a lejátszó között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622491"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Oktatóanyag: a lejátszó integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a lejátszó Azure Active Directory (Azure ad) használatával. Ha az Azure AD-vel integrálja a lejátszhatóságot, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a lejátszó szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek a visszajátszásba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Lejátszó egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A lejátszó az **SP** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-displayr-from-the-gallery"></a>A lejátszó hozzáadása a katalógusból

A lejátszó Azure AD-integrációjának konfigurálásához hozzá kell adnia a megtekintő elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **lejátszó** kifejezést a keresőmezőbe.
1. Válassza ki a **lejátszó** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Az Azure AD SSO konfigurálása és tesztelése a **Britta Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a lejátszó eszközben.

Az Azure AD SSO és a Visszajátszási szolgáltatás konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. A **[lejátszó konfigurálása](#configure-displayr)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. **[Hozzon létre egy visszajátszó tesztelési felhasználót](#create-displayr-test-user)** , hogy a Britta a felhasználó Azure ad-képviseletéhez kapcsolódó, Simon in the deplayer-ügyféllel rendelkezzen.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **lejátszó** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépést:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YOURDOMAIN>.displayr.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`<YOURDOMAIN>.displayr.com`
    
    c. A **Válasz URL-cím** szövegmezőbe írja be a következőt: `https://app.displayr.com/Login/ProcessSamlResponse` .
    
    d. Kattintson a **Mentés** gombra.

    >[!NOTE]
    >Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez vegye fel a kapcsolatot a [lejátszó ügyfél-támogatási csapatával](mailto:support@displayr.com) . Az Azure Portal alapszintű SAML-konfiguráció szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A kijátszó alkalmazás az SAML-jogcímeket egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

   ![A "szerkesztés" ikonnal jelölt "felhasználói attribútumok" szakaszt megjelenítő képernyőkép.](common/edit-attribute.png)

1. A fentieken kívül a kijátszó alkalmazás néhány további attribútumot vár az SAML-válaszban. A **csoport jogcímek (előzetes verzió)** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

   a. Kattintson **a csoport hozzáadása** elemre.

      ![Képernyőkép, amely megjeleníti a "csoport jogcímek (előzetes verzió)" ablakot a kiválasztott beállításokkal.](./media/displayr-tutorial/config05.png)

   b. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

   c. Válassza ki a **csoport azonosítója** **forrás attribútumát** .

   d. Győződjön meg arról **, hogy testreszabja a csoportjogcím nevét**.

   e. **A csoportok szerepkör-jogcímként való kibocsátása**.

   f. Kattintson a **Mentés** gombra.

1. A **set-up deplayer** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>A lejátszó konfigurálása

1. Ha szeretné automatizálni a konfigurációt a lejátszó alkalmazásban, a **bővítmény telepítése** lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítő** megtekintő alkalmazásra. Itt adja meg a rendszergazdai hitelesítő adatokat a visszajátszáshoz való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a lejátszhatóságot, nyisson meg egy új böngészőablakot, és jelentkezzen be a leválasztó céges webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **Beállítások** elemre, majd navigáljon a **fiókhoz**.

    ![Képernyőfelvétel: a "beállítások" ikon és a "fiók" lehetőség van kiválasztva.](./media/displayr-tutorial/config01.png)

5. Váltson a **Beállítások** elemre a felső menüben, majd görgessen le az oldalra, és kattintson az **egyszeri bejelentkezés konfigurálása (SAML)** elemre.

    ![Képernyőfelvétel: a "beállítások" lap van kiválasztva, valamint az "egyszeri bejelentkezés konfigurálása (S L)" művelet be van jelölve.](./media/displayr-tutorial/config02.png)

6. Az **egyszeri bejelentkezés (SAML)** oldalon hajtsa végre a következő lépéseket:

    ![Konfiguráció](./media/displayr-tutorial/config03.png)

    a. Jelölje be az **egyszeri bejelentkezés engedélyezése (SAML)** jelölőnégyzetet.

    b. Másolja a tényleges **azonosító** értéket az Azure ad **alapszintű SAML-konfiguráció** szakaszában, és illessze be a **kiállító** szövegmezőbe.

    c. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    d. Illessze be a **kijelentkezési** URL-cím szövegmezőbe a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    e. Nyissa meg a tanúsítványt (Base64) a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **tanúsítvány** szövegmezőbe.

    f. A **csoportos leképezések** nem kötelezőek.

    : Kattintson a **Mentés** gombra.  

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a lejátszó számára.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **lejátszó** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-displayr-test-user"></a>Lejátszó tesztelési felhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a lejátszó szolgáltatásba. A lejátszó esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a visszajátszásba rendszergazdaként.

2. Kattintson a **Beállítások** elemre, majd navigáljon a **fiókhoz**.

    ![A "fiók" beállítással rendelkező "beállítások (fogaskerék)" ikont megjelenítő képernyőkép.](./media/displayr-tutorial/config01.png)

3. A felső menüben váltson a **Beállítások** elemre, és görgessen le az oldalra, amíg a **felhasználók** szakaszra nem kattint, majd az **új felhasználó** elemre.

    ![Képernyőfelvétel: a "felhasználók" feliratú "Settings" (beállítások) lap, és az "új felhasználó" gomb van kiválasztva.](./media/displayr-tutorial/config07.png)

4. Az **új felhasználó** lapon hajtsa végre a következő lépéseket:

    ![Lejátszó konfigurálása](./media/displayr-tutorial/config06.png)

    a. A **név** szövegmezőbe írja be a felhasználó nevét (például **Brittasimon**).

    b. Az **e-mail** szövege szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `Brittasimon@contoso.com` .

    c. Válassza ki a megfelelő **csoporttagság**.

    d. Kattintson a **Mentés** gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a kiválasztó csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a leválasztó, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
