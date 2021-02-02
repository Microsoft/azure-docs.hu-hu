---
title: 'Oktatóanyag: Azure Active Directory a Dropbox Businessrel való integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Dropbox Business között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: cc7e75094f23c5382797541911d0aeb8be50f432
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430913"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Oktatóanyag: a Dropbox Business integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Dropbox businesst a Azure Active Directory (Azure AD) szolgáltatással. Ha a Dropbox businesst az Azure AD-vel integrálja, a következőket teheti:

* Az Azure AD-ben elérhető, a Dropbox üzleti tevékenységhez hozzáférő vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Dropboxba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Dropbox üzleti egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

* Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Dropbox Business támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Dropbox Business támogatja [a felhasználók automatikus üzembe](dropboxforbusiness-tutorial.md) helyezését és megszüntetését

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-dropbox-business-from-the-gallery"></a>Dropbox Business hozzáadása a katalógusból

A Dropbox Business Azure AD-integrációjának konfigurálásához hozzá kell adnia a Dropbox businesst a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Dropbox Business** kifejezést a keresőmezőbe.
1. Válassza a **Dropbox Business** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Azure AD SSO konfigurálása és tesztelése a Dropbox Businesshez

Azure AD SSO konfigurálása és tesztelése a Dropbox Business használatával a **Britta Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a Dropbox Business-beli kapcsolódó felhasználó között.

Az Azure AD SSO a Dropbox Businessrel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.    
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. A **[Dropbox Business SSO konfigurálása](#configure-dropbox-business-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Dropbox Business test User](#create-dropbox-business-test-user)** -to have a Britta Simon in Dropbox Business, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Dropbox Business** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.dropbox.com/sso/<id>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a következő értéket: `Dropbox`

    > [!NOTE]
    > Az előző bejelentkezési URL-cím értéke nem valós érték. A tényleges bejelentkezési URL-címmel frissítenie kell az értéket, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Dropbox Business beállítása** szakaszban másolja a megfelelő URL-címet (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Dropbox üzleti hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Dropbox Business** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-dropbox-business-sso"></a>A Dropbox Business SSO konfigurálása

1. A Dropbox Businessen belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítás Dropbox Business** lehetőségre a Dropbox üzleti alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a Dropbox üzletágba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Dropbox businesst, nyisson meg egy új böngészőablakot, és lépjen a Dropbox üzleti bérlőre, és jelentkezzen be a Dropbox üzleti bérlőbe. és hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely a "Dropbox Business Sign in" lapot mutatja.](./media/dropboxforbusiness-tutorial/account.png "Egyszeri bejelentkezés konfigurálása")

4. Kattintson a **felhasználó ikonra** , és válassza a **Beállítások** lapot.

    ![Képernyőkép, amely a "felhasználó ikon" műveletet és a "beállítások" beállítást mutatja.](./media/dropboxforbusiness-tutorial/configure1.png "Egyszeri bejelentkezés konfigurálása")

5. A bal oldali navigációs ablaktáblán kattintson a **felügyeleti konzol** elemre.

    ![A "felügyeleti konzol" elemet megjelenítő képernyőkép.](./media/dropboxforbusiness-tutorial/configure2.png "Egyszeri bejelentkezés konfigurálása")

6. A **felügyeleti konzolon** kattintson a bal oldali navigációs ablaktábla **Beállítások** elemére.

    ![Képernyőkép, amely a "beállítások" elemet jeleníti meg.](./media/dropboxforbusiness-tutorial/configure3.png "Egyszeri bejelentkezés konfigurálása")

7. Válassza az **egyszeri bejelentkezés** lehetőséget a **hitelesítés** szakaszban.

    ![A "hitelesítés" szakaszt az "egyszeri bejelentkezés" beállítással ábrázoló képernyőkép.](./media/dropboxforbusiness-tutorial/configure4.png "Egyszeri bejelentkezés konfigurálása")

8. Az **egyszeri bejelentkezés** szakaszban hajtsa végre a következő lépéseket:  

    ![Képernyőkép, amely az "egyszeri bejelentkezés" konfigurációs beállításait jeleníti meg.](./media/dropboxforbusiness-tutorial/configure5.png "Egyszeri bejelentkezés konfigurálása")

    a. Válassza a **kötelező** lehetőséget a legördülő listából az **egyszeri bejelentkezéshez**.

    b. Kattintson a **bejelentkezési URL-cím hozzáadása** elemre, majd az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt, majd válassza a **kész** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/sso.png "Egyszeri bejelentkezés konfigurálása")

    c. Kattintson a **tanúsítvány feltöltése** elemre, majd keresse meg a **Base64-kódolású tanúsítványfájl** , amelyet a Azure Portal letöltött.

    d. Kattintson a **Másolás hivatkozásra** , és illessze be a másolt értéket a **Dropbox üzleti tartomány és URL-** **cím** szövegmezőbe Azure Portal.

    e. Kattintson a **Mentés** gombra.

### <a name="create-dropbox-business-test-user"></a>Dropbox Business test-felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Dropbox Businessben. A Dropbox Business az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Dropbox-üzletágban, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Dropbox üzleti ügyfél-támogatási csapatával](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Dropbox üzleti bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Dropbox üzleti bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Dropbox Business csempére kattint, a rendszer átirányítja a Dropbox üzleti bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A Dropbox Business konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).