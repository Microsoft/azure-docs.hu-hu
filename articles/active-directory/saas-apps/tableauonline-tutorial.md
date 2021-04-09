---
title: 'Oktatóanyag: Azure Active Directory-integráció a tabló online-nal | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a tabló online között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tabló online-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tablót online Azure Active Directory (Azure AD) szolgáltatással. A tabló online és az Azure AD integrálásával a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a tabló online-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a tabló Online szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Tabló online egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A tabló online támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-tableau-online-from-the-gallery"></a>Tabló online hozzáadása a katalógusból

A tabló online-integrációjának az Azure AD-be való konfigurálásához hozzá kell adnia a tablót az online katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **tabló online** kifejezést a keresőmezőbe.
1. Válassza a **tabló online** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Azure AD SSO konfigurálása és tesztelése a tabló online-hoz

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a tabló Online szolgáltatással konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a tabló-online kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD SSO és a tabló online konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[tabló online SSO konfigurálása](#configure-tableau-online-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[tabló online tesztelési felhasználójának létrehozása](#create-tableau-online-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **tabló online** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > `<entityid>`Ebben az oktatóanyagban megjelenik az érték a **tabló online beállítása** szakaszban. Az entitás-azonosító érték az **Azure ad-azonosító** érték lesz a **tabló online beállítása** szakaszban.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **tabló online beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a tabló online-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **tabló online** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-tableau-online-sso"></a>A tabló online SSO konfigurálása

1. A tabló online-on belüli konfigurációjának automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **tabló beállítása online** lehetőségre, és a tabló online alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a tabló online-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a tablót, egy másik böngészőablakban jelentkezzen be a tabló online céges webhelyére rendszergazdaként.

1. Lépjen a beállítások, majd a **hitelesítés** **menüpontra** .

    ![A képernyőképen a beállítások menüből kiválasztott hitelesítés látható.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Az SAML engedélyezéséhez a **hitelesítési típusok** szakaszban. Jelölje be **a további hitelesítési módszer engedélyezése** jelölőnégyzetet, majd jelölje be az **SAML** jelölőnégyzetet.

    ![Képernyőfelvétel: a hitelesítési típusok szakasz, ahol kiválaszthatja az értékeket.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Görgessen le a **Metaadatok importálása a tabló online** szakaszba.  Kattintson a Tallózás gombra, és importálja a metaadat-fájlt, amelyet az Azure AD-ből töltött le. Ezután kattintson az **alkalmaz** gombra.

   ![Képernyőfelvétel: az a szakasz, ahol importálhatja a metaadat-fájlt.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Az **egyeztetési érvényesítések** szakaszban szúrja be a megfelelő identitás-szolgáltatói **jogcímet az e-mail-cím**, a **keresztnév** és a **vezetéknév** mezőbe. Az információk lekérése az Azure AD-ből: 
  
    a. A Azure Portal lépjen a **tabló online** alkalmazás-integráció oldalára.

    b. A **felhasználói attribútumok & jogcímek** szakaszban kattintson a Szerkesztés ikonra.

   ![Képernyőfelvétel: a felhasználói attribútumok & jogcímek szakasz, ahol kiválaszthatja a Szerkesztés ikont.](./media/tableauonline-tutorial/attributesection.png)

    c. Másolja a névtér értékét ezekhez az attribútumokhoz: givenName, e-mail-cím és vezetéknév a következő lépések segítségével:

   ![A képernyőképen a givenName, a vezetéknév és az EmailAddress attribútum látható.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kattintson a **User. givenName** értékre

    e. Másolja az értéket a **névtér** szövegmezőből.

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése szakasz, ahol megadhatja a névteret.](./media/tableauonline-tutorial/attributesection2.png)

    f. Az e-mailek és a vezetéknév névteri értékeinek másolásához ismételje meg a fenti lépéseket.

    : Váltson a tabló online alkalmazásra, majd állítsa be a **felhasználói attribútumok & jogcímek** szakaszt a következőképpen:

    * **E-mail: e-mail** vagy **userPrincipalName**

    * Keresztnév: **givenName**

    * **Vezetéknév** : név

    ![Képernyőfelvétel: a Match attribútumok szakasz, ahol megadhatja az értékeket.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tabló online tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a tabló online-ban.

1. A **tabló online** oldalon kattintson a **Beállítások** , majd a **hitelesítés** szakaszra. Görgessen le a **felhasználók kezelése** szakaszhoz. Kattintson a **felhasználók hozzáadása** elemre, majd az **e-mail-címek megadása** lehetőségre.
  
    ![Képernyőfelvétel: a felhasználók kezelése szakasz, ahol a felhasználók hozzáadása lehetőséget választhatja.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Válassza **a felhasználók hozzáadása (SAML) hitelesítés** lehetőséget. Az **e-mail címek megadása** szövegmezőben adja hozzá a Britta. Simon \@ contoso.com
  
    ![Képernyőfelvétel: a felhasználók hozzáadása lap, ahol megadhat egy e-mail-címet.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kattintson a **felhasználók hozzáadása** elemre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a tabló online bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül a tabló online bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások felületén a tabló online csempére kattint, a rendszer átirányítja a tabló online bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A tabló online konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)