---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a 8x8 | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és 8x8 között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a 8x8

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a 8x8 a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az 8x8-t az Azure AD-vel, a következőket teheti:

* A 8x8-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a 8x8 az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy 8x8-előfizetés.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a 8x8 támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-8x8-from-the-gallery"></a>8x8 hozzáadása a gyűjteményből

A 8x8 Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a 8x8 a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **8x8** kifejezést a keresőmezőbe.
1. Válassza ki a **8x8** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Azure AD SSO konfigurálása és tesztelése a 8x8-hez

Konfigurálja és tesztelje az Azure AD SSO-t a 8x8 a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a 8x8-ben.

Az Azure AD SSO és a 8x8 konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[8X8 SSO konfigurálása](#configure-8x8-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre 8x8-teszt felhasználót](#create-8x8-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-8x8 rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **8x8** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `https://sso.8x8.com/saml2`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet: `https://sso.8x8.com/saml2`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre. A tanúsítványt később a **8X8 SSO konfigurálása** szakaszban található oktatóanyagban fogja használni.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **8X8 beállítása** szakaszban másolja az URL-címeket, és ezeket az URL-értékeket az oktatóanyag későbbi részében fogja használni.

    ![Konfigurációs URL-címek másolása](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a 8x8.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **8x8** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-8x8-sso"></a>8x8 SSO konfigurálása

Az oktatóanyag következő része attól függ, hogy milyen típusú előfizetéssel rendelkezik a 8x8.

* A 8x8-kiadások és az X sorozatú, az Configuration Manager for adminisztrációt használó ügyfelek esetében tekintse meg a [8X8 felügyeleti konzol konfigurálása](#configure-8x8-admin-console)című témakört.
* A fiókkezelés felügyeletét használó Virtual Office-ügyfelek esetében tekintse meg a [8X8 Account Manager konfigurálását](#configure-8x8-account-manager)ismertető témakört.

### <a name="configure-8x8-admin-console"></a>A 8x8 felügyeleti konzol konfigurálása

1. A 8x8 belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **8X8 beállítása** elemre, majd a 8x8 alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a 8x8 való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a 8x8, jelentkezzen be a 8x8 [felügyeleti konzolra](https://admin.8x8.com/) rendszergazdaként.

1. A kezdőlapon kattintson az **Identitáskezelés** elemre.

    ![Képernyőkép, amely kiemeli az Identitáskezelés csempéjét.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Jelölje be **az egyszeri bejelentkezés (SSO)** jelölőnégyzetet, majd válassza a **Microsoft Azure ad** lehetőséget.

    ![Képernyőfelvétel: az egyszeri bejelentkezés (SSO) és a Microsoft Azure AD lehetőségek.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Másolja ki a három URL-címet és az aláíró tanúsítványt az Azure AD-ben az **egyszeri Sign-On beállítása az SAML** -oldalról a 8X8 felügyeleti konzoljának **Microsoft Azure ad SAML-beállítások** szakaszában.

    ![8x8 felügyeleti konzol](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Másolja a **bejelentkezési URL-címet** a **identitásszolgáltató bejelentkezési URL-címére**.

    b. Másolja az **Azure ad-azonosítót** a **identitásszolgáltató kiállító URL-CÍMÉRE/urn-re**.

    c. A **kijelentkezési URL-cím** másolása **identitásszolgáltató KIJELENTKEZÉSI URL-címre**

    d. Töltse le a **tanúsítványt (Base64)** , és töltse fel a **tanúsítványba**.

    e. Kattintson a **Mentés** gombra.

### <a name="configure-8x8-account-manager"></a>A 8x8 Account Manager konfigurálása

1. Jelentkezzen be a 8x8 Virtual Office-bérlőre rendszergazdaként.

1. Válassza ki a **Virtual Office-fiókhoz tartozó Mgr** alkalmazást az alkalmazás panelen.

    ![Képernyőkép, amely kiemeli a virtuális Office-fiók kezelője csempét.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Válassza a kezelendő **üzleti** fiók elemet, majd kattintson **a bejelentkezés** gombra.

    ![Képernyőkép, amely kiemeli az üzleti lehetőséget és a bejelentkezés gombot.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Kattintson a **fiókok** fülre a menü listában.

    ![Képernyőkép, amely kiemeli a fiókok lapot a menü listában.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Kattintson az **egyszeri bejelentkezés** elemre a fiókok listájában.

    ![Képernyőkép, amely kiemeli az egyszeri bejelentkezés lehetőségét.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Válassza az **egyszeri bejelentkezés** lehetőséget a hitelesítési módszerek területen, majd kattintson az **SAML** elemre.

    ![Képernyőkép, amely kiemeli az SAML-t az egyszeri bejelentkezés alatt.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Az **SAML egyszeri bejelentkezés** szakaszban hajtsa végre a következő lépéseket:

    ![Konfigurálás az alkalmazás oldalán](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    b. A kijelentkezési **URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.

    c. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    d. Kattintson a **Tallózás** gombra a Azure Portalból letöltött tanúsítvány feltöltéséhez.

    e. Kattintson a **Mentés** gombra.

### <a name="create-8x8-test-user"></a>8x8-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a 8x8-ben. Együttműködik a [8x8 támogatási csapatával](https://www.8x8.com/about-us/contact-us) , hogy hozzáadja a felhasználókat a 8x8 platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a 8x8 bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a 8x8 bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a 8x8, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a 8x8 csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie arra a 8x8, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A 8x8 konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).