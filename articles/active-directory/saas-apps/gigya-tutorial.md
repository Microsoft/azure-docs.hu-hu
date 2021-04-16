---
title: 'Oktatóanyag: Azure Active Directory Integráció a Gigya-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Gigya között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516077"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Oktatóanyag: Azure Active Directory Integráció a Gigyával

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Gigyát a Azure Active Directory (Azure AD) használatával. Ha integrálja a Gigyát az Azure AD-val, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáfér a Gigyhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Gigyába az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Gigya egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Gigya támogatja **az sp** által kezdeményezett SSO-t.

## <a name="add-gigya-from-the-gallery"></a>Gigya hozzáadása a katalógusból

A Gigya Azure AD-be való integrálásának konfigurálásához fel kell vennie a Gigyát a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Gigya nevet** a keresőmezőbe.
1. Válassza a **Gigya lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Az Azure AD SSO konfigurálása és tesztelése a Gigyhoz

Konfigurálja és tesztelje az Azure AD SSO-t a Gigyával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Gigya kapcsolódó felhasználója között.

Ha az Azure AD SSO-t a Gigyával konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Gigya SSO konfigurálása](#configure-gigya-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Gigya-tesztfelhasználó létrehozása](#create-gigya-test-user)** – a gigya-i B.Simon megfelelője, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal a **Gigya-alkalmazásintegráció** oldalon  keresse meg a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `http://<companyname>.gigya.com`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Az értékekért vegye fel a kapcsolatot a [Gigya-ügyfél](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) támogatási csapatával. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-sel) lap **SAML** aláíró tanúsítvány szakaszában kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

6. A **Gigya beállítása szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Gigyának.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Gigya lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az &quot;Alapértelmezett hozzáférés&quot; szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name=&quot;configure-gigya-sso&quot;></a>A Gigya SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Gigya vállalati webhelyére rendszergazdaként.

2. Lépjen a **Beállítások \> SAML-bejelentkezés lapra,** majd kattintson a **Hozzáadás gombra.**
   
    ![SAML-bejelentkezés](./media/gigya-tutorial/login.png &quot;SAML-bejelentkezés")

3. Az **SAML-bejelentkezés szakaszban** hajtsa végre a következő lépéseket:
   
    ![SAML-konfiguráció](./media/gigya-tutorial/configuration.png "SAML-konfiguráció")
   
    a. A Név **szövegmezőbe** írja be a konfiguráció nevét.
   
    b. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet a Azure Portal. 
   
    c. A **Single Sign-On Service URL** (Egykiszolgálós szolgáltatás URL-címe) szövegmezőbe illessze be a bejelentkezési **URL-cím** értékét, amelyet a Azure Portal.
   
    d. A  **Névazonosító formátuma szövegmezőbe** illessze be a névazonosító formátumának értékét, amelyet a Azure Portal.
   
    e. Nyissa meg a base-64 kódolású tanúsítványt az Azure Portal-ból letöltött jegyzettömbben, másolja a tartalmát a vágólapra, majd illessze be az **X.509-tanúsítvány** szövegmezőbe.
   
    f. Kattintson **a Beállítások mentése gombra.**

## <a name="create-gigya-test-user"></a>Gigya-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkeznek a Gigyába, ki kell őket kiépítenünk a Gigyában. A Gigya esetében a kiépítés manuális feladat.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Felhasználói fiókok végrehajtásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **Gigya vállalati** webhelyére rendszergazdaként.

2. Válassza a **Felhasználók \> rendszergazdai kezelése,** majd a **Felhasználók meghívása gombra.**
   
    ![Felhasználók kezelése](./media/gigya-tutorial/users.png "Felhasználók kezelése")

3. A Felhasználók meghívása párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználók meghívása](./media/gigya-tutorial/invite-user.png "Felhasználók meghívása")
   
    a. Az **E-mail** szövegmezőbe írja be a kiépítni kívánt érvényes Azure Active Directory e-mail-aliast.
    
    b. Kattintson **a Felhasználó meghívása elemre.**
      
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást, amely megerősíti a fiókot, mielőtt az aktívvá válik.
    > 

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Gigya bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Gigya bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a gigya csempére kattint a Saját alkalmazások, a rendszer átirányítja a Gigya bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A Gigya konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
