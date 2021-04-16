---
title: 'Oktatóanyag: Azure Active Directory Integráció az Egnyte-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az Egnyte között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása az Egnyte-el

Ez az oktatóanyag bemutatja, hogyan integrálhatja az Egnyte-et a Azure Active Directory (Azure AD) használatával. Ha az Egnyte-et integrálja az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik az Egnyte-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az Egnyte-ba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Az egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* Az Egnyte támogatja **az sp által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-egnyte-from-the-gallery"></a>Az Egnyte hozzáadása a katalógusból

Az Egnyte Azure AD-be való integrálásának konfigurálához hozzá kell adni az Egnyte-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe az **Egnyte** parancsot.
1. Válassza **az Egnyte lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Az Azure AD SSO konfigurálása és tesztelése Az Egnyte-hez

Konfigurálja és tesztelje az Azure AD SSO-t Form.com egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Form.com.

Ha az Azure AD SSO-t a Form.com konfigurálni és tesztelni, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja az Egnyte SSO-t](#configure-egnyte-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Egnyte-tesztfelhasználó létrehozása](#create-egnyte-test-user)** – ha az Egnyte-ben a B.Simon megfelelője a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal az **Egnyte** alkalmazásintegráció oldalán  keresse meg a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<companyname>.egnyte.com`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse az értéket a tényleges urlSign-On a válasz URL-címével. Az értékért forduljon az [Egnyte-ügyfél](https://www.egnyte.com/corp/contact_egnyte.html) támogatási csapatához. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

4. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-sel) lap **SAML** aláíró tanúsítvány szakaszában kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

5. A **Set up Egnyte (Egnyte** beállítása) szakaszban másolja ki a megfelelő URL-cím(eke)t a követelménynek megfelelően.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát. Ehhez hozzáférést biztosít az Egnyte-hoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **Egnyte lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az &quot;Alapértelmezett hozzáférés&quot; szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name=&quot;configure-egnyte-sso&quot;></a>Az Egnyte SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az Egnyte vállalati webhelyre.

2. Kattintson a **Beállítások** elemre.
   
    ![1. beállítások](./media/egnyte-tutorial/settings-tab.png &quot;Beállítások")

3. A menüben kattintson a **Beállítások elemre.**

    ![1. menü](./media/egnyte-tutorial/menu-tab.png "Menü")

4. Kattintson a **Konfiguráció fülre,** majd a Biztonság **elemre.**

    ![Biztonság](./media/egnyte-tutorial/configuration.png "Biztonság")

5. Az Egyetlen **Sign-On hitelesítés szakaszban** hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezéses hitelesítés](./media/egnyte-tutorial/authentication.png "Egyszeri bejelentkezéses hitelesítés")   
    
    a. Egyszeri **bejelentkezéses hitelesítésként válassza** az **SAML 2.0 lehetőséget.**
   
    b. **Identitásszolgáltatóként válassza az** **AzureAD lehetőséget.**
   
    c. **Illessze be** a bejelentkezési URL Azure Portal az Identitásszolgáltató bejelentkezési **URL-címe szövegmezőbe.**
   
    d. Illessze be az **Azure AD-azonosítót,** amelyet a Azure Portal identitásszolgáltató entitásazonosítója **szövegmezőbe** másolt.
      
    e. Nyissa meg a base-64 kódolású tanúsítványt a Azure Portal-ból letöltött jegyzettömbben, másolja a tartalmát  a vágólapra, majd illessze be az Identitásszolgáltató tanúsítványa szövegmezőbe.
   
    f. Alapértelmezett **felhasználóleképezésként válassza** az E-mail-cím **lehetőséget.**
   
    : A **Use domain-specific issuer value (Tartományspecifikus kiállító értékének használata) beállításban** válassza a **disabled (letiltva) lehetőséget.**
   
    h. Kattintson a **Mentés** gombra.

### <a name="create-egnyte-test-user"></a>Egnyte-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudják jelentkezni az Egnyte-ba, az Egnyte-ban kell kiépítenünk őket. Az Egnyte esetében a kiépítés manuális feladat.

**Felhasználói fiókok végrehajtásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be **az Egnyte vállalati** webhelyére rendszergazdaként.

2. Kattintson a **Beállítások – Felhasználók és & \> elemre.**

3. Kattintson **az Új felhasználó hozzáadása elemre,** majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
    ![Felhasználók](./media/egnyte-tutorial/add-user.png "Felhasználók")

4. Az Új **energiafelhasználó szakaszban** hajtsa végre a következő lépéseket:
    
    ![Új általános felhasználó](./media/egnyte-tutorial/new-user.png "Új általános felhasználó")   

    a. Az **E-mail** szövegmezőbe írja be annak a felhasználónak az e-mail-címét, mint **Brittasimon \@ contoso.com.**

    b. A **Felhasználónév** szövegmezőbe írja be annak a felhasználónak a felhasználónevét, mint **Brittasimon.**

    c. Válassza **az Egyszeri bejelentkezés hitelesítési** **típust.**
   
    d. Kattintson a **Mentés** gombra.
    
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos egy értesítő e-mailt kap.
    >

>[!NOTE]
>Az Azure AD felhasználói fiókok létrehozására az Egnyte által biztosított bármely más Egnyte-felhasználói fiók létrehozására használható eszköz vagy API.
>

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja az Egnyte bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az Egnyte bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a böngészőben az Egnyte csempére Saját alkalmazások, a rendszer átirányítja az Egnyte bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Az Egnyte konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
