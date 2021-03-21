---
title: 'Oktatóanyag: Azure Active Directory a Citrix ShareFile-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Citrix ShareFile között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 03f2ec7aef1faadcb72d6c7a5a058c7d06596539
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729676"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Oktatóanyag: Azure Active Directory integráció a Citrix ShareFile

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Citrix ShareFile Azure Active Directory (Azure AD) használatával.
A Citrix ShareFile és az Azure AD integrálásával a következő előnyöket nyújtja:

* A Citrix ShareFile-hez hozzáférő Azure AD-beli vezérlést is beállíthat.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Citrix ShareFile (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Citrix ShareFile való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.
* Citrix ShareFile egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Citrix ShareFile támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Citrix ShareFile hozzáadása a katalógusból

A Citrix ShareFile az Azure AD-be való integrálásának konfigurálásához a katalógusból Citrix ShareFile kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Citrix ShareFile** kifejezést a keresőmezőbe.
1. Válassza ki a **Citrix ShareFile** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Azure AD SSO konfigurálása és tesztelése a Citrix ShareFile-hez

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Citrix ShareFile konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Citrix ShareFile kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Citrix ShareFile való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. A **[Citrix SHAREFILE SSO konfigurálása](#configure-citrix-sharefile-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Citrix ShareFile test User](#create-citrix-sharefile-test-user)** -t, hogy a Britta Simon a Citrix ShareFile, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Citrix ShareFile** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.sharefile.com/saml/login`

    b. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez lépjen kapcsolatba a [Citrix ShareFile ügyfél-támogatási csapatával](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Citrix ShareFile beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Citrix ShareFile elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Citrix ShareFile** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-citrix-sharefile-sso"></a>A Citrix ShareFile SSO konfigurálása

1. A **Citrix ShareFile** belül a konfiguráció automatizálásához a **bővítmény telepítése** lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Citrix ShareFile beállítása** elemre, majd a Citrix ShareFile alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Citrix ShareFile való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha a Citrix ShareFile-t manuálisan szeretné beállítani, egy másik böngészőablakban jelentkezzen be a Citrix ShareFile vállalati webhelyre rendszergazdaként.

1. Az **irányítópulton** kattintson a **Beállítások** elemre, és válassza a **rendszergazdai beállítások** lehetőséget.

    ![Felügyelet](./media/sharefile-tutorial/settings.png)

1. A rendszergazdai beállítások területen lépjen a **biztonsági**  ->  **Bejelentkezés & biztonsági házirend** elemre.
   
    ![Fiókkezelés](./media/sharefile-tutorial/settings-security.png "Fiókkezelés")

1. Az **egyszeri bejelentkezés/SAML 2,0 konfigurációs** párbeszédablak **alapszintű beállítások** területén hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés](./media/sharefile-tutorial/saml-configuration.png "Egyszeri bejelentkezés")
   
    a. Az **SAML engedélyezése** területen válassza az **Igen** lehetőséget.

    b. Másolja a **ShareFile kiállítói/entitás-azonosító** értékét, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelének **azonosító URL** mezőjébe.
    
    c. A **identitásszolgáltató-kiállító/entitás-azonosító** szövegmezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    d. Kattintson az **X. 509 tanúsítvány** mező melletti **módosítás** elemre, majd töltse fel a letöltött tanúsítványt a Azure Portal.
    
    e. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.
    
    f. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    : A **választható beállítások** területen válassza az **SP-ben kezdeményezett hitelesítési környezet** **felhasználónevet és jelszót** , valamint a **pontos** beállítást.

5. Kattintson a **Mentés** gombra.

## <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile-teszt felhasználó létrehozása

1. Jelentkezzen be a **Citrix ShareFile** -bérlőbe.

2. Kattintson **a**  ->  **felhasználók felhasználók kezelése Kezdőlap**  ->  **új felhasználók** létrehozása  ->  **alkalmazottak létrehozásához**.
   
    ![Alkalmazott létrehozása](./media/sharefile-tutorial/create-user.png "Alkalmazott létrehozása")

3. Az **alapszintű információ** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Alapvető adatok](./media/sharefile-tutorial/user-form.png "Alapvető adatok")
   
    a. Az **Utónév** szövegmezőbe írja be a felhasználó **Britta** **nevet** .
   
    b.  A **vezetéknév** szövegmezőbe írja be a felhasználó **vezetéknevét** **Simon** néven.
   
    c. Az **E-mail cím** szövegmezőbe írja be a Britta Simon e-mail-címét **brittasimon \@ contoso.com** néven.

4. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.
  
    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa egy e-mailt kap, és egy hivatkozást követve megerősíti a fiókját, mielőtt az aktívvá válna. Az Azure AD felhasználói fiókjainak kiépítéséhez a Citrix ShareFile által biztosított bármely más Citrix ShareFile felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhatja.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a Citrix ShareFile bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.

* Lépjen közvetlenül a Citrix ShareFile bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Citrix ShareFile csempére kattint, a rendszer átirányítja a Citrix ShareFile bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Citrix ShareFile konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).