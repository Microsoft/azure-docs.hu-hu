---
title: 'Oktatóanyag: Azure Active Directory integráció a TalentLMS-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TalentLMS között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 84d50d19a8356418a5cbf1f93784e5b8816d4be5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689351"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Oktatóanyag: Azure Active Directory integráció a TalentLMS

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TalentLMS a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az TalentLMS-t az Azure AD-vel, a következőket teheti:

* A TalentLMS-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a TalentLMS az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció TalentLMS való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* TalentLMS egyszeri bejelentkezésre engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A TalentLMS támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-talentlms-from-the-gallery"></a>TalentLMS hozzáadása a gyűjteményből

A TalentLMS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TalentLMS a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **TalentLMS** kifejezést a keresőmezőbe.
1. Válassza ki a **TalentLMS** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Azure AD SSO konfigurálása és tesztelése a TalentLMS-hez

Konfigurálja és tesztelje az Azure AD SSO-t a TalentLMS a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a TalentLMS-ben.

Az Azure AD SSO és a TalentLMS konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[TALENTLMS SSO konfigurálása](#configure-talentlms-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre TalentLMS-teszt felhasználót](#create-talentlms-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-TalentLMS rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **TalentLMS** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![TalentLMS tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.TalentLMSapp.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a TalentLMS ügyfélszolgálati [csapatához](https://www.talentlms.com/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **TalentLMS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a TalentLMS.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **TalentLMS** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-talentlms-sso"></a>TalentLMS SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a TalentLMS vállalati webhelyre rendszergazdaként.

1. A **fiók & beállításai** szakaszban kattintson a **felhasználók** fülre.

    ![Fiók & beállításai](./media/talentlms-tutorial/IC777296.png "Fiók & beállításai")

1. Kattintson az **egyszeri Sign-On (SSO)** elemre,

1. Az egyszeres Sign-On szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/talentlms-tutorial/saml.png "Egyszeri bejelentkezés")

    a. Az **SSO-integráció típusa** listából válassza az **SAML 2,0** elemet.

    b. Az **Identity Provider (identitásszolgáltató)** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portalból másolt.

    c. Illessze be a Azure Portal **ujjlenyomat** értékét a **tanúsítvány ujjlenyomat** szövegmezőbe.

    d.  A **távoli bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    e. A távoli kijelentkezési **URL-cím** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    f. Töltse ki a következő mezőket:

    * A **TargetedID** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * A **Utónév** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * A **vezetéknév** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Az **e-mail** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Kattintson a **Mentés** gombra.

### <a name="create-talentlms-test-user"></a>TalentLMS-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a TalentLMS, a TalentLMS kell kiépíteni őket. TalentLMS esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **TalentLMS** -bérlőbe.

1. Kattintson a **felhasználók**, majd a **felhasználó hozzáadása** elemre.

1. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felhasználó hozzáadása](./media/talentlms-tutorial/IC777299.png "Felhasználó hozzáadása")  

    a. Az **Utónév** szövegmezőbe írja be a felhasználó nevét (például: `Britta` ).

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét (például: `Simon` ).
 
    c. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet `brittasimon@contoso.com` .

    d. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a TalentLMS által biztosított TalentLMS felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a TalentLMS bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a TalentLMS bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások TalentLMS csempére kattint, a rendszer átirányítja a TalentLMS bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A TalentLMS konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).