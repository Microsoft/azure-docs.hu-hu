---
title: 'Oktatóanyag: Azure Active Directory-integráció a vásznon | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és vászon között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653039"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Oktatóanyag: Azure Active Directory-integráció a vásznon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a vásznon Azure Active Directory (Azure AD-val). Ha az Azure AD-vel integrálja a vásznon, a következőket teheti:

* A vászonhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával a vászonra.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:
 
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Vászon egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A vászon támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-canvas-from-the-gallery"></a>Vászon hozzáadása a gyűjteményből

A vászonnak az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **vászon** kifejezést a keresőmezőbe.
1. Válassza ki a **vászon** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Azure AD SSO konfigurálása és tesztelése a vászonhoz

Konfigurálja és tesztelje az Azure AD SSO-t a vásznon egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és egy vásznon lévő kapcsolódó felhasználó között.

Az Azure AD SSO és vászon használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[vászon egyszeri](#configure-canvas-sso)** bejelentkezésének konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Vászon-tesztelési felhasználó létrehozása](#create-canvas-test-user)** – ha a felhasználó Azure ad-beli képviseletéhez csatolt B. Simon-beli munkatársaival rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **vászon** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Vászon tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.instructure.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez vegye fel a kapcsolatot a [Canvas ügyfél-támogatási csapatával](https://community.canvaslms.com/community/help) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **vászon beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a vászonhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **vászon** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-canvas-sso"></a>Vászon egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a vászon vállalati webhelyre rendszergazdaként.

2. Lépjen a **tanfolyamok \> felügyelt fiókok \> Microsoft webhelyre**.

    ![Vászon](./media/canvas-lms-tutorial/ic775990.png "Vászon")

3. A bal oldali navigációs ablaktáblán válassza a **hitelesítés** lehetőséget, majd kattintson az **Új SAML-konfiguráció hozzáadása** lehetőségre.

    ![Hitelesítés](./media/canvas-lms-tutorial/ic775991.png "Hitelesítés")

4. Az aktuális integráció oldalon hajtsa végre a következő lépéseket:

    ![Aktuális integráció](./media/canvas-lms-tutorial/save.png "Aktuális integráció")

    a. A **identitásszolgáltató-entitás azonosítója** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. A bejelentkezési **URL-cím** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    d. A **jelszó módosítása hivatkozás** szövegmezőbe illessze be a **jelszó módosítása URL-cím** értékét, amelyet a Azure Portal másolt.

    e. A **Tanúsítvány ujjlenyomata** szövegmezőbe illessze be a tanúsítvány **ujjlenyomatának** értékét, amelyet a Azure Portal másolt.

    f. A **bejelentkezési attribútum** listából válassza a **NameID** lehetőséget.

    : Az **azonosító formátuma** listából válassza az **emailAddress** lehetőséget.

    h. Kattintson a **hitelesítési beállítások mentése** lehetőségre.

### <a name="create-canvas-test-user"></a>Vászon tesztelési felhasználó létrehozása

Ha engedélyezni szeretné az Azure AD-felhasználók számára, hogy bejelentkezzenek a vászonra, a vásznon kell kiépíteni őket. Vászon esetén a felhasználó kiosztása kézi feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **vászon** bérlőbe.

2. Lépjen a **tanfolyamok \> felügyelt fiókok \> Microsoft webhelyre**.

   ![Vászon](./media/canvas-lms-tutorial/ic775990.png "Vászon")

3. Kattintson a **felhasználók** elemre.

   ![Képernyőfelvétel: a kiválasztott felhasználókkal rendelkező vászon menü.](./media/canvas-lms-tutorial/ic775995.png "Felhasználók")

4. Kattintson az **új felhasználó hozzáadása** lehetőségre.

   ![A képernyőképen az új felhasználó hozzáadása gomb látható.](./media/canvas-lms-tutorial/ic775996.png "Felhasználók")

5. Az új felhasználó hozzáadása párbeszédpanelen hajtsa végre a következő lépéseket:

   ![Felhasználó hozzáadása](./media/canvas-lms-tutorial/ic775997.png "Felhasználó hozzáadása")

   a. A **teljes név** szövegmezőbe írja be a felhasználó nevét (például **BrittaSimon**).

   b. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **brittasimon \@ contoso.com**-e-mail-címét.

   c. A **Bejelentkezés** szövegmezőbe írja be a felhasználó Azure ad-beli e-mail-címét, például **brittasimon \@ contoso.com**.

   d. Válassza ki **a fiók létrehozásával kapcsolatos e-mailt a felhasználónak**.

   e. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a vászon által biztosított egyéb felhasználói fiókok létrehozására szolgáló eszközöket vagy API-kat is használhatja.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a vászon bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a vászon bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások vászon csempére kattint, automatikusan be kell jelentkeznie arra a vászonra, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A vászon konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).