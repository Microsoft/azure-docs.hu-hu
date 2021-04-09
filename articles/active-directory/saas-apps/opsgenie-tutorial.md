---
title: 'Oktatóanyag: Azure Active Directory integráció a OpsGenie-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és OpsGenie között.
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
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728524"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OpsGenie

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OpsGenie a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az OpsGenie-t az Azure AD-vel, a következőket teheti:

* A OpsGenie-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a OpsGenie az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* OpsGenie egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A OpsGenie támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie hozzáadása a gyűjteményből

A OpsGenie Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a OpsGenie a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **OpsGenie** kifejezést a keresőmezőbe.
1. Válassza ki a **OpsGenie** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Azure AD SSO konfigurálása és tesztelése a OpsGenie-hez

Konfigurálja és tesztelje az Azure AD SSO-t a OpsGenie a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a OpsGenie-ben.

Az Azure AD SSO és a OpsGenie konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[OPSGENIE SSO konfigurálása](#configure-opsgenie-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre OpsGenie-teszt felhasználót](#create-opsgenie-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-OpsGenie rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **OpsGenie** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. A **OpsGenie beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a OpsGenie.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **OpsGenie** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-opsgenie-sso"></a>OpsGenie SSO konfigurálása

1. A OpsGenie belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **OpsGenie beállítása** elemre, majd a OpsGenie alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a OpsGenie való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a OpsGenie, egy másik böngészőablakban jelentkezzen be a OpsGenie vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások** elemre, majd kattintson az **egyszeri bejelentkezés** fülre.
   
    ![OpsGenie egyetlen Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Az egyszeri bejelentkezés engedélyezéséhez válassza az **engedélyezve** lehetőséget.
   
    ![Képernyőfelvétel: az "engedélyezve" jelölőnégyzet be van jelölve.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. A **szolgáltató** szakaszban kattintson a **Azure Active Directory** fülre.
   
    ![Képernyőfelvétel: a "Azure Active Directory" lapon kiválasztva a "szolgáltató" szakasz látható.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. A Azure Active Directory párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: az "egyszeri bejelentkezés" szakasz "egyszeri bejelentkezés engedélyezése", "S A" M 2,0 Endpoint "és" metadata U R L ".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Másolja az **alkalmazás-azonosító URI** -értékét, és illessze be **azonosító (Entity ID)** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    a. Másolja a **Válasz URL-címét** , és illessze be a **Válasz URL** -szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    a. Az **SAML 2,0 végpont** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portal másolt.
    
    b. A **metaadatok URL-címe:** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.
    
    c. Az egyszeri bejelentkezés engedélyezéséhez kapcsolja be az **egyszeri bejelentkezés engedélyezése** kapcsolót.

    d. Kattintson az **SSO-beállítások alkalmazása** lehetőségre.

### <a name="create-opsgenie-test-user"></a>OpsGenie-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy B. Simon nevű felhasználó létrehozása a OpsGenie-ben. 

1. A webböngésző ablakban jelentkezzen be a OpsGenie-bérlőbe rendszergazdaként.

2. A bal oldali panelen a **felhasználók** elemre kattintva navigáljon a felhasználók listájához.
   
    ![OpsGenie-beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

4. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![A "felhasználó hozzáadása" párbeszédpanelt a "levelezés" és a "teljes név" szövegmezők kiemelve, valamint a "Mentés" gomb kiválasztásával jeleníti meg.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Az **e-mail** szövegmezőbe írja be az Azure Active Directoryban található B. Simon e-mail-címét.
   
    b. A **teljes név** szövegmezőbe írja be a következőt: **B. Simon**.
   
    c. Kattintson a **Mentés** gombra. 

> [!NOTE]
> B. Simon egy e-mailt kap, amely útmutatást nyújt a profil beállításához.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a OpsGenie, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások OpsGenie csempére kattint, automatikusan be kell jelentkeznie arra a OpsGenie, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

* A OpsGenie konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).