---
title: 'Oktatóanyag: Azure Active Directory integráció a Sage Intacct | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Sage Intacct között.
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
ms.openlocfilehash: 5a216e39ca32b16de405c7924d08da52c6eae4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736958"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Oktatóanyag: a Sage Intacct integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sage Intacct Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Sage Intacct-t, a következőket teheti:

* Az Azure AD-ben a Sage Intacct-hez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Sage Intacct az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Sage Intacct egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Sage Intacct támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage-Intacct hozzáadása a katalógusból

A Sage Intacct Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy Sage Intacct a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Sage Intacct** kifejezést a keresőmezőbe.
1. Válassza ki a **Sage Intacct** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Azure AD SSO konfigurálása és tesztelése a Sage Intacct

Konfigurálja és tesztelje az Azure AD SSO-t a Sage Intacct egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Sage Intacct-ben.

Az Azure AD SSO és a Sage Intacct konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
2. A **[Sage INTACCT SSO konfigurálása](#configure-sage-intacct-sso)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Sage Intacct-teszt felhasználót](#create-sage-intacct-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli Intacct.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **bölcs Intacct** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:  `https://www.intacct.com/ia/acct/sso_response.phtml`

1. A Sage Intacct alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Sage Intacct alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **felhasználói attribútumok & jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket az SAML-jogkivonat attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Attribútum neve  |  Forrás attribútum|
    | ---------------| --------------- |
    | Vállalat neve | **Sage Intacct vállalati azonosító** |
    | name | Az értéknek meg kell egyeznie a Sage Intacct **felhasználói azonosítóval**, amelyet a **create Sage Intacct test User (bölcsek csoport létrehozása) felhasználói szakaszban** adhat meg, amelyet az oktatóanyag későbbi részében talál |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listából írja be vagy válassza ki az adott sorhoz megjelenített attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sage Intacct beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Sage Intacct való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **zsálya Intacct** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a &quot;default Access&quot; szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name=&quot;configure-sage-intacct-sso&quot;></a>A Sage Intacct SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sage Intacct vállalati webhelyre rendszergazdaként.

1. Kattintson a **vállalat** lapra, majd a **vállalati adatok** elemre.

    ![Vállalat](./media/intacct-tutorial/ic790037.png &quot;Vállalat")

1. Kattintson a **Biztonság** fülre, majd a **Szerkesztés** elemre.

    ![Biztonság](./media/intacct-tutorial/ic790038.png "Biztonság")

1. Az **egyszeri bejelentkezés (SSO)** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/intacct-tutorial/ic790039.png "egyszeri bejelentkezés")

    a. Válassza **az egyszeri bejelentkezés engedélyezése** lehetőséget.

    b. **Identitás-szolgáltató típusaként** válassza az **SAML 2,0** lehetőséget.

    c. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    d. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    e. Nyissa meg a **Base-64** kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **tanúsítvány** mezőbe.

    f. Kattintson a **Mentés** gombra.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct-teszt felhasználó létrehozása

Ha be szeretné állítani az Azure AD-felhasználók számára, hogy bejelentkezzenek a Sage Intacct, ki kell építeni a Sage Intacct-be. A Sage Intacct esetében a kiépítés manuális feladat.

**A felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Sage Intacct** -bérlőbe.

1. Kattintson a **vállalat** lapra, majd a **felhasználók** elemre.

    ![Felhasználók](./media/intacct-tutorial/ic790041.png "Felhasználók")

1. Kattintson a **Hozzáadás** fülre.

    ![Hozzáadás](./media/intacct-tutorial/ic790042.png "Hozzáadás")

1. A **felhasználói adatok** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználói adatok szakasz, ahol megadhatja az ebben a lépésben szereplő információkat.](./media/intacct-tutorial/ic790043.png "Felhasználói adatok")

    a. Adja meg a felhasználói **adatok** szakaszban kiépíteni kívánt Azure ad-fiók **felhasználói azonosítóját**, **vezetéknevét** **, utónevét,** **e-mail-címét**, **címét** és **telefonszámát** .

    > [!NOTE]
    > Győződjön meg arról, hogy a fenti **felhasználói azonosító** és a **forrás attribútum** értéke, amely a Azure Portal **felhasználói attribútumok** szakaszának **Name** attribútumával van leképezve, azonosnak kell lennie.

    b. Válassza ki a létrehozni kívánt Azure AD-fiók **rendszergazdai jogosultságait** .

    c. Kattintson a **Mentés** gombra. 
    
    d. Az Azure AD-fiók tulajdonosa egy e-mailt kap, és egy hivatkozást követ, amely megerősíti a fiókját, mielőtt az aktívvá válna.

1. Kattintson az **egyszeri bejelentkezés** fülre, és győződjön meg arról, hogy az **összevont egyszeri bejelentkezés felhasználói azonosítója** az alábbi képernyőképen, valamint a **forrás attribútum** értéke, amely a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` Azure Portal **felhasználói attribútumok** szakaszában van leképezve, a következőnek kell lennie:.

    ![Képernyőfelvétel: a felhasználói adatok szakasz, amelyen megadhatja az összevont S S O-felhasználót.](./media/intacct-tutorial/ic790044.png "Felhasználói adatok")

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez használhatja a Sage Intacct által biztosított más Sage Intacct felhasználói fiókok létrehozására szolgáló eszközöket vagy API-kat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Sage-Intacct, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Sage Intacct csempére kattint, automatikusan be kell jelentkeznie arra a Sage-Intacct, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Sage Intacct konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).