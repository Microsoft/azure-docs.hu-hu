---
title: 'Oktatóanyag: Azure Active Directory integráció a Jitbit ügyfélszolgálattal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Jitbit helpdesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581804"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Oktatóanyag: Azure Active Directory integráció a Jitbit ügyfélszolgálattal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Jitbit helpdesket Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Jitbit ügyfélszolgálatot, a következőket teheti:

* A Jitbit ügyfélszolgálathoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Jitbit segélyszolgálat számára az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Jitbit ügyfélszolgálati egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Jitbit segélyszolgálat támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Jitbit segélyszolgálat hozzáadása a katalógusból

A Jitbit segélyszolgálat Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Jitbit segélyszolgálat szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Jitbit segélyszolgálat** kifejezést a keresőmezőbe.
1. Válassza az **Jitbit helpdesk** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Az Azure AD SSO konfigurálása és tesztelése a Jitbit segélyszolgálat számára

Konfigurálja és tesztelje az Azure AD SSO-t a Jitbit ügyfélszolgálattal a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Jitbit Helpdeskben.

Az Azure AD SSO és a Jitbit helpdesk konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Jitbit segélyszolgálat egyszeri bejelentkezésének konfigurálása](#configure-jitbit-helpdesk-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Jitbit helpdesk-teszt felhasználó létrehozása](#create-jitbit-helpdesk-test-user)** – ha a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-partneri jogosultsággal rendelkezik, a Jitbit segélyszolgálat tagja.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Jitbit helpdesk** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az egyik URL-címet a következő minta használatával:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Jitbit helpdesk](https://www.jitbit.com/support/) ügyfélszolgálatával.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `https://www.jitbit.com/web-helpdesk/`

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Jitbit-segélyszolgálat beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Jitbit ügyfélszolgálatához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Jitbit segélyszolgálat** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-jitbit-helpdesk-sso"></a>A Jitbit segélyszolgálat egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Jitbit segélyszolgálat vállalati webhelyére.

1. A felső eszköztáron kattintson az **Adminisztráció** elemre.

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/settings.png "Felügyelet")

1. Kattintson az **általános beállítások** elemre.

    ![A képernyőképen az általános beállítások hivatkozás látható.](./media/jitbit-helpdesk-tutorial/general.png "Felhasználók, vállalatok és engedélyek")

1. A **hitelesítési beállítások** konfigurálása szakaszban hajtsa végre a következő lépéseket:

    ![Hitelesítési beállítások](./media/jitbit-helpdesk-tutorial/authentication.png "Hitelesítési beállítások")

    a. Válassza az **SAML 2,0 egyszeri bejelentkezés engedélyezése** lehetőséget, ha egyetlen Sign-On (SSO) használatával szeretne bejelentkezni a **OneLogin**.

    b. A **végpont URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. Nyissa meg a **Base-64** kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    d. Kattintson a **módosítások mentése** gombra.

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit segélyszolgálat-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Jitbit Segélyszolgálatba, ki kell építeni őket a Jitbit helpdesk szolgáltatásba. Jitbit segélyszolgálat esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Jitbit helpdesk** -bérlőbe.

1. A felső menüben kattintson az **Adminisztráció** elemre.

    ![Felügyelet](./media/jitbit-helpdesk-tutorial/settings.png "Felügyelet")

1. Kattintson **a felhasználók, vállalatok és engedélyek** elemre.

    ![Felhasználók, vállalatok és engedélyek](./media/jitbit-helpdesk-tutorial/users.png "Felhasználók, vállalatok és engedélyek")

1. Kattintson a **felhasználó hozzáadása** elemre.

    ![Felhasználói csoportok](./media/jitbit-helpdesk-tutorial/add.png "Felhasználó hozzáadása")

1. A létrehozás szakaszban adja meg az Azure AD-fiók által a következőképpen kiépített adatmennyiséget:

    ![Létrehozás](./media/jitbit-helpdesk-tutorial/create-section.png "Létrehozás")

   a. A **Felhasználónév** szövegmezőbe írja be annak a felhasználónak a felhasználónevét, mint a **BrittaSimon**.

   b. Az **e-mail** szövegmezőbe írja be a felhasználó e-mail címét, például: **BrittaSimon@contoso.com** .

   c. Az **Utónév** szövegmezőbe írja be a felhasználó nevét (például **Britta**).

   d. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

   e. Kattintson a **Létrehozás** lehetőségre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Jitbit helpdesk által biztosított Jitbit-segélyszolgálat felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Jitbit helpdesk bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Jitbit helpdesk bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Jitbit helpdesk csempére kattint, a rendszer átirányítja a Jitbit helpdesk bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Jitbit segélyszolgálat konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).