---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshservice | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freshservice között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651752"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshservice

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freshservice a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Freshservice-t az Azure AD-vel, a következőket teheti:

* A Freshservice-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Freshservice az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Freshservice egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Freshservice támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-freshservice-from-the-gallery"></a>Freshservice hozzáadása a gyűjteményből

A Freshservice Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshservice a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Freshservice** kifejezést a keresőmezőbe.
1. Válassza ki a **Freshservice** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Azure AD SSO konfigurálása és tesztelése a Freshservice-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Freshservice a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freshservice-ben.

Az Azure AD SSO és a Freshservice konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[FRESHSERVICE SSO konfigurálása](#configure-freshservice-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Freshservice-teszt felhasználót](#create-freshservice-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Freshservice rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Freshservice** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company-name>.freshservice.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company-name>.freshservice.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a Freshservice ügyfélszolgálati [csapatához](https://support.freshservice.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Azure Portal **Freshservice beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Freshservice.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Freshservice** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-freshservice-sso"></a>Freshservice SSO konfigurálása

1. A Freshservice belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Freshservice beállítása** elemre, majd a Freshservice alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Freshservice való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Freshservice, jelentkezzen be a Freshservice vállalati webhelyre rendszergazdaként.

1. A bal oldali menüben kattintson a **rendszergazda** elemre, és válassza az **ügyfélszolgálati biztonság** lehetőséget az **általános beállítások** területen.

    ![Felügyelet](./media/freshservice-tutorial/configure-1.png "Rendszergazda")

1. A **Biztonság** területen kattintson az **ugrás a Freshservice 360 biztonságra** elemre.

    ![Biztonság](./media/freshservice-tutorial/configure-2.png "Biztonság")

1. A **Biztonság** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/freshservice-tutorial/configure-3.png "Egyszeri bejelentkezés")
  
    a. Az **egyszeri bejelentkezéshez** válassza **a be** lehetőséget.

    b. A **login metódusban** válassza az **SAML egyszeri bejelentkezés** lehetőséget.

    c. Az identitásszolgáltató szövegmezőben **megadott entitás-azonosítóban** illessze be az **entitás-azonosító** értékét, amelyet a Azure Portal másolt.

    d. Az **SAML SSO URL** szövegmezőbe illessze be a **bejelentkezési URL** értékét, amelyet a Azure Portal másolt.

    e. Az **aláírási beállítások** területen jelölje be a **csak az aláírt érvényesítések** lehetőséget a legördülő listából.

    f. A **kijelentkezési URL** szövegmezőben illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    : A **biztonsági tanúsítvány** szövegmezőbe illessze be a korábban beszerzett **tanúsítvány (Base64)** értékét.
  
    h. Kattintson a **Mentés** gombra.


## <a name="create-freshservice-test-user"></a>Freshservice-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a FreshService, a FreshService kell kiépíteni őket. FreshService esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **FreshService** vállalati webhelyre rendszergazdaként.

2. A bal oldali menüben kattintson a **rendszergazda** elemre.

3. A **felhasználói kezelés** szakaszban kattintson a **kérelmezők** elemre.

    ![Kérelmezők](./media/freshservice-tutorial/create-user-1.png "Kérelmezők")

4. Kattintson az **új kérelmező** elemre.

    ![Új kérők](./media/freshservice-tutorial/create-user-2.png "Új kérők")

5. Az **új kérelmező** szakaszban adja meg a kötelező mezőket, majd kattintson a **Save (Mentés**) gombra.
    ![Új kérelmező](./media/freshservice-tutorial/create-user-3.png "Új kérelmező")  

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítését, mielőtt az aktívvá válna.
    >  

    > [!NOTE]
    > Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a FreshService által biztosított FreshService felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Freshservice bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Freshservice bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Freshservice csempére kattint, automatikusan be kell jelentkeznie arra a Freshservice, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

 A Freshservice konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).