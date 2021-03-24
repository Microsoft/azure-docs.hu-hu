---
title: 'Oktatóanyag: Azure Active Directory integráció a SolarWinds Service Desk szolgáltatással (korábban Samanage) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a SolarWinds Service Desk (korábban Samanage) között.
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
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956043"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Oktatóanyag: Azure Active Directory integráció a SolarWinds Service Desk szolgáltatással (korábban Samanage)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SolarWinds a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SolarWinds-t az Azure AD-vel, a következőket teheti:

* A SolarWinds-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SolarWinds az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SolarWinds egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A SolarWinds támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-solarwinds-from-the-gallery"></a>SolarWinds hozzáadása a gyűjteményből

A SolarWinds Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SolarWinds a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SolarWinds** kifejezést a keresőmezőbe.
1. Válassza ki a **SolarWinds** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Azure AD SSO konfigurálása és tesztelése a SolarWinds-hez

Konfigurálja és tesztelje az Azure AD SSO-t a SolarWinds a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SolarWinds-ben.

Az Azure AD SSO és a SolarWinds konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SOLARWINDS SSO konfigurálása](#configure-solarwinds-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre SolarWinds-teszt felhasználót](#create-solarwinds-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-SolarWinds rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **SolarWinds** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval, amelyet az oktatóanyag későbbi részében ismertet. További részletekért forduljon a [Samanage ügyfél-támogatási csapatához](https://www.samanage.com/support). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **SolarWinds beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a SolarWinds.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SolarWinds** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>SolarWinds SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SolarWinds vállalati webhelyre rendszergazdaként.

2. Kattintson az **irányítópult** elemre, és válassza a **beállítás** lehetőséget a bal oldali navigációs panelen.
   
    ![Irányítópult](./media/samanage-tutorial/tutorial-samanage-1.png "Irányítópult")

3. Kattintson az **egyszeri bejelentkezés** elemre.
   
    ![Egyszeri bejelentkezés](./media/samanage-tutorial/tutorial-samanage-2.png "Egyszeri bejelentkezés")

4. Navigáljon a **Bejelentkezés az SAML használatával** szakaszban, hajtsa végre a következő lépéseket:
   
    ![Bejelentkezés az SAML használatával](./media/samanage-tutorial/tutorial-samanage-3.png "Bejelentkezés az SAML használatával")
 
    a. Kattintson **az egyszeri Sign-On engedélyezése SAML** használatával lehetőségre.  
 
    b. Az **identitás-szolgáltató URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.    
 
    c. Erősítse meg, hogy a **bejelentkezési URL-cím** megegyezik a Azure Portal **ALAPszintű SAML-konfigurációs** szakaszának **bejelentkezési URL-címével** .
 
    d. A **kijelentkezési URL** szövegmezőbe írja be a Azure Portalból másolt **KIJELENTKEZÉSI URL-cím** értékét.
 
    e. Az **SAML-kiállító** szövegmezőbe írja be az azonosító URI-jét az identitás-szolgáltatóba.
 
    f. Nyissa meg Azure Portal a Jegyzettömbből letöltött Base-64 kódolású tanúsítványt, másolja a vágólapra a tartalmát, majd illessze be az **Identity Provider x. 509-tanúsítvány beillesztése** szövegmezőbe.
 
    : Kattintson **a felhasználók létrehozása lehetőségre, ha nem léteznek a SolarWinds-ben**.
 
    h. Kattintson a **Frissítés** parancsra.

### <a name="create-solarwinds-test-user"></a>SolarWinds-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a SolarWinds, a SolarWinds kell kiépíteni őket.  
SolarWinds esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a SolarWinds vállalati webhelyre rendszergazdaként.

2. Kattintson az **irányítópult** elemre, és válassza a **beállítás** elemet a bal oldali navigációs sávon.
   
    ![Beállítás](./media/samanage-tutorial/tutorial-samanage-1.png "Beállítás")

3. Kattintson a **felhasználók** lapra
   
    ![Felhasználók](./media/samanage-tutorial/tutorial-samanage-6.png "Felhasználók")

4. Kattintson az **új felhasználó** elemre.
   
    ![Új felhasználó](./media/samanage-tutorial/tutorial-samanage-7.png "Új felhasználó")

5. Írja be a létrehozni kívánt Azure Active Directory fiók **nevét** és **e-mail-címét** , majd kattintson a **felhasználó létrehozása** lehetőségre.
   
    ![Felhasználó létrehozása](./media/samanage-tutorial/tutorial-samanage-8.png "Felhasználó létrehozása")
   
   >[!NOTE]
   >A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna. A SolarWinds által biztosított egyéb SolarWinds-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a SolarWinds bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a SolarWinds bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások SolarWinds csempére kattint, a rendszer átirányítja a SolarWinds bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A SolarWinds konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).