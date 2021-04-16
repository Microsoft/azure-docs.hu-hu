---
title: 'Oktatóanyag: Azure Active Directory Workfront-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Workfront között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478452"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Oktatóanyag: Azure Active Directory Workfront-integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Workfrontot a Azure Active Directory (Azure AD) használatával. Ha integrálja a Workfrontot az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD-beli vezérlés, aki hozzáféréssel rendelkezik a Workfronthoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Workfrontba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD És a Workfront integrációjának konfiguráláshoz a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nincs Azure AD-környezete, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Workfront egyszeri bejelentkezést engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Workfront támogatja **az SP által** kezdeményezett SSO-t.

## <a name="add-workfront-from-the-gallery"></a>Workfront hozzáadása a katalógusból

Ahhoz, hogy konfigurálja a Workfront és az Azure AD integrációját, hozzá kell adni a Workfrontot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Workfront kifejezéseket** a keresőmezőbe.
1. Válassza a **Workfront** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Az Azure AD SSO for Workfront konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Workfronttal egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Workfrontban.

Az Azure AD SSO Workfronttal való konfiguráláshoz és teszteléshez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Workfront SSO konfigurálása](#configure-workfront-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Workfront-tesztfelhasználó létrehozása](#create-workfront-test-user)** – a B.Simon megfelelője a Workfrontban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **a Workfront** alkalmazásintegráció oldalán keresse meg a **Kezelés** szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi mintát használva: `https://<companyname>.attask-ondemand.com`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket az [értékeket a Workfront-ügyfél](https://www.workfront.com/services-and-support) támogatási csapatával kell kapcsolatba lépnie. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. A **Set up Single Sign-On with SAML** (Egyetlen tanúsítvány beállítása SAML-sel) lap **SAML** aláíró tanúsítvány szakaszában kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

6. A **Workfront beállítása szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát a Workfronthoz való hozzáférés engedélyezésével.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Workfront lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-workfront-sso"></a>A Workfront SSO konfigurálása

1. Jelentkezzen be a Munkahelyi webhelyre rendszergazdaként.

2. Ugrás az **Egyszeri bejelentkezés konfigurációja beállításra.**

3. Az Egyszeri **bejelentkezés párbeszédpanelen** hajtsa végre a következő lépéseket
    
    ![Egyszeres Sign-On](./media/workfront-tutorial/single-sign-on.png)
   
    a. A **Típus mezőben** válassza az **SAML 2.0 lehetőséget.**
   
    b. Válassza **a Szolgáltató azonosítója lehetőséget.**
   
    c. Illessze **be a Bejelentkezési URL-címet** a Bejelentkezési **portál URL-címe** szövegmezőbe.
   
    d. **Illessze be a** Kijelentkezés URL-címe szöveget a **Kijelentkezés URL-címe** szövegmezőbe.
   
    e. **Illessze be a Jelszó URL-címének** módosítása szöveget a Jelszó **URL-címének** módosítása szövegmezőbe.
   
    f. Kattintson a **Mentés** gombra.

### <a name="create-workfront-test-user"></a>Workfront-tesztfelhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Workfrontban.

**Britta Simon nevű felhasználó Workfrontban való létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Munkahelyi webhelyre rendszergazdaként.
 
2. A felső menüben kattintson a **Személyek elemre.**
 
3. Kattintson **a New Person (Új személy) elemre.** 

4. Az Új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Workfront-tesztfelhasználó létrehozása](./media/workfront-tutorial/add-person.png)
   
    a. Az First **Name (Név) szövegmezőbe** írja be a "Britta" szöveget.
   
    b. A **Vezetéknév szövegmezőbe** írja be a "Simon" nevet.
   
    c. Az **E-mail-cím** szövegmezőbe írja be Britta Simon e-mail-címét a Azure Active Directory.
   
    d. Kattintson **a Személy hozzáadása elemre.**

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Workfront bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Workfront bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a munkahelyi Saját alkalmazások a Workfront csempére kattint, a rendszer átirányítja a Workfront bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A Workfront konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
