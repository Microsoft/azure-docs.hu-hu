---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Analytics-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Analytics-felhő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652640"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Oktatóanyag: SAP Analytics-felhő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Analytics-felhőt Azure Active Directory (Azure AD) használatával. Ha integrálja az SAP Analytics-felhőt az Azure AD-vel, a következőket teheti:

* Az SAP Analytics-felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek az SAP Analytics-felhőbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP Analytics felhőalapú egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Analytics-felhő támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>SAP Analytics-felhő hozzáadása a katalógusból

Az SAP Analytics Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Analytics-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **SAP Analytics Cloud** kifejezést.
1. Válassza az **SAP Analytics Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Azure AD SSO konfigurálása és tesztelése az SAP Analytics-felhőhöz

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Analytics-felhővel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Analytics-felhőben.

Az Azure AD SSO SAP Analytics-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SAP Analytics FELHŐALAPÚ egyszeri bejelentkezés konfigurálása](#configure-sap-analytics-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre SAP Analytics Cloud test User](#create-sap-analytics-cloud-test-user)** -t, hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, az SAP Analytics-felhőben található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **SAP Analytics Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Az **azonosító (Entity ID)** szövegmezőben írja be az URL-címet a következő minták egyikének használatával:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Ezekben az URL-címekben szereplő értékek csak a bemutatóra vonatkoznak. Frissítse az értékeket a tényleges bejelentkezési URL-címmel és az azonosító URL-címével. A bejelentkezési URL-cím beszerzéséhez lépjen kapcsolatba az [SAP Analytics Cloud Client támogatási csapatával](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Az azonosító URL-cím lekéréséhez töltse le az SAP Analytics felhőalapú metaadatait a felügyeleti konzolról. Ezt az oktatóanyag későbbi részében ismertetjük.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **SAP Analytics-felhő beállítása** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SAP Analytics-felhőhöz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **SAP Analytics-felhő** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics felhőalapú egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az SAP Analytics felhőalapú vállalati webhelyére.

2. Válassza a **menü**  >    >  **rendszerfelügyelet** menüpontot.
    
    ![Válassza a menü, majd a rendszer, majd az adminisztráció lehetőséget.](./media/sapboc-tutorial/configure-1.png)

3. A **Biztonság** lapon válassza a **Szerkesztés** (toll) ikont.
    
    ![A biztonság lapon kattintson a Szerkesztés ikonra.](./media/sapboc-tutorial/configure-2.png)  

4. A **hitelesítési módszer** beállításnál válassza az **SAML egyszeri Sign-On (SSO)** lehetőséget.

    ![SAML-Sign-On kiválasztása a hitelesítési módszerhez](./media/sapboc-tutorial/configure-3.png)  

5. A szolgáltatói metaadatok letöltéséhez (1. lépés) válassza a **Letöltés** lehetőséget. A metaadat-fájlban keresse meg és másolja a **entityID** értéket. A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen illessze be az értéket az **azonosító** mezőbe.

    ![Másolja és illessze be a entityID értéket](./media/sapboc-tutorial/configure-4.png)  

6. Ha fel szeretné tölteni a szolgáltatói metaadatokat (2. lépés) a Azure Portal letöltött fájlban, az **Identity Provider metaadatainak feltöltése** területen válassza a **feltöltés** lehetőséget.  

    ![Az identitás-szolgáltató metaadatainak feltöltése területen válassza a feltöltés lehetőséget.](./media/sapboc-tutorial/configure-5.png)

7. A **felhasználói attribútum** listáról válassza ki a megvalósításhoz használni kívánt felhasználói attribútumot (3. lépés). Ez a felhasználói attribútum az identitás-szolgáltatóhoz van hozzárendelve. Ha egyéni attribútumot szeretne megadni a felhasználó oldalán, használja az **Egyéni SAML-leképezési** lehetőséget. Vagy az **e-mail-címet** vagy a **felhasználói azonosítót** is választhatja felhasználói attribútumként. A példánkban kiválasztottuk az **e-mailt** , mert a felhasználói azonosító jogcímet a **userPrincipalName** attribútummal együtt a Azure Portal a **felhasználói attribútumok & jogcímek** című szakaszban leképezték. Ez egy egyedi felhasználói e-mailt biztosít, amely minden sikeres SAML-válasz esetében az SAP Analytics Cloud alkalmazásnak lesz elküldve.

    ![Felhasználói attribútum kiválasztása](./media/sapboc-tutorial/configure-6.png)

8. A fiók identitás-szolgáltatóval való ellenőrzéséhez (4. lépés) a **bejelentkezési hitelesítő adatok (e-mail)** mezőben adja meg a felhasználó e-mail-címét. Ezután válassza a **Fiók ellenőrzése** lehetőséget. A rendszer hozzáadja a bejelentkezési hitelesítő adatokat a felhasználói fiókhoz.

    ![Adja meg az e-mailt, és válassza a fiók ellenőrzése lehetőséget](./media/sapboc-tutorial/configure-7.png)

9. Válassza a **Mentés** ikont.

    ![Mentés ikon](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics Cloud test-felhasználó létrehozása

Az Azure AD-felhasználókat az SAP Analytics felhőbe való bejelentkezés előtt kell kiépíteni az SAP Analytics-felhőbe. Az SAP Analytics felhőben a kiépítés manuális feladat.

Felhasználói fiók kiépítése:

1. Jelentkezzen be rendszergazdaként az SAP Analytics felhőalapú vállalati webhelyére.

2. Válassza a **menü**  >  **biztonsági**  >  **felhasználók** lehetőséget.

    ![Alkalmazott hozzáadása](./media/sapboc-tutorial/user-1.png)

3. A **felhasználók** lapon új felhasználói adatok hozzáadásához válassza a elemet **+** . 

    ![Felhasználók hozzáadása lap](./media/sapboc-tutorial/user-4.png)

    Ezután hajtsa végre a következő lépéseket:

    1. A **felhasználói azonosító** mezőben adja meg a felhasználó felhasználói azonosítóját, például: **B**.

    1. A **keresztnév** mezőbe írja be a felhasználó utónevét, például: **B**.

    1. A **vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    1. A **megjelenítendő név** mezőbe írja be a felhasználó teljes nevét, például: **B. Simon**.

    1. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét, például: `b.simon@contoso.com` .

    1. A **szerepkörök kiválasztása** lapon válassza ki a megfelelő szerepkört a felhasználó számára, majd kattintson az **OK gombra**.

        ![Válasszon szerepkört](./media/sapboc-tutorial/user-3.png)

    1. Válassza a **Mentés** ikont.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja az SAP Analytics Felhőbeli bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az SAP Analytics felhőalapú bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások SAP Analytics-felhő csempére kattint, a rendszer átirányítja az SAP Analytics felhőalapú bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Az SAP Analytics-felhő konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).