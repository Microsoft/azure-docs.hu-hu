---
title: 'Oktatóanyag: Azure Active Directory Zscaler Two | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Zscaler Two között.
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
ms.openlocfilehash: b4d39492a05ee90c0ba2e081336d7b04579a5c43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519028"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Oktatóanyag: Azure Active Directory Zscaler Two-val való integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Zscaler Two-t a Azure Active Directory (Azure AD) használatával. Ha integrálja a Zscaler Two-t az Azure AD-val, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáfér a Zscaler Two-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Zscaler Two-ba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD És a Zscaler Two integrációjának konfigurálását a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nincs Azure AD-környezete, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Zscaler Két egyszeri bejelentkezéses előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A Zscaler Two támogatja **az SP által** kezdeményezett SSO-t.

* A Zscaler Two támogatja **az időben való felhasználóátépítést.**

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-zscaler-two-from-the-gallery"></a>Zscaler Two hozzáadása a katalógusból

A Zscaler Two Azure AD-be való integrálásának konfigurálásához hozzá kell adni a Zscaler 2-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Zscaler Two (Zscaler 2)** kifejezéseket a keresőmezőbe.
1. Válassza **a Zscaler Two** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Az Azure AD SSO konfigurálása és tesztelése Zscaler 2-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler Two használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Zscaler Two kapcsolódó felhasználója között.

Az Azure AD SSO Zscaler Two-val való konfigurálását és tesztelését a következő lépésekkel végezheti el:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Zscaler két egyszeri bejelentkezés konfigurálása](#configure-zscaler-two-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálását.
    1. **[Zscaler Két tesztfelhasználó létrehozása](#create-zscaler-two-test-user)** – a Zscaler 2-ben a B.Simon megfelelője, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **Zscaler Két** alkalmazásintegráció oldalán keresse  meg a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** szövegmezőbe írja be azt az URL-címet, amelyet a felhasználók a ZScaler Two alkalmazásba való bejelentkezéshez használnak.

    > [!NOTE]
    > Az értéket a tényleges URL-Sign-On frissíti. Az értékért lépjen kapcsolatba a [Zscaler két](https://www.zscaler.com/company/contact) ügyféltámogatási csapatával. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. A Zscaler Two-alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat-attribútumok konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson **a Szerkesztés ikonra** a **Felhasználói attribútumok párbeszédpanel megnyitásához.**

    ![A felhasználói attribútumokat a Szerkesztés ikonnal kijelölve bemutató képernyőkép.](common/edit-attribute.png)

6. A Zscaler Two-alkalmazás a fentiek mellett arra számít, hogy néhány további attribútumot kell visszaadódni AZ SAML-válaszban. A Felhasználói **attribútumok**  párbeszédpanel Felhasználói jogcímek szakaszában hajtsa végre a következő lépéseket az SAML-jogkivonat attribútumának hozzáadásához az alábbi táblázatban látható módon:
    
    | Name | Forrásattribútum |
    | ---------| ------------ |
    | memberOf (tag) | user.assignedroles |

    a. Kattintson **az Új jogcím hozzáadása elemre** a Felhasználói **jogcímek kezelése párbeszédpanel megnyitásához.**

    ![Képernyőkép a Felhasználói jogcímek lehetőségről az Új jogcím hozzáadása lehetőséggel.](common/new-save-attribute.png)

    ![Képernyőkép a Felhasználói jogcímek kezelése párbeszédpanelről, ahol megadhatja a leírt értékeket.](common/new-attribute-details.png)

    b. A Név **szövegmezőbe** írja be az adott sorhoz látható attribútumnevet.

    c. Hagyja **üresen a névteret.**

    d. Válassza a Forrás **attribútumot.**

    e. A **Forrásattribútum listából** írja be az adott sorhoz látható attribútumértéket.
    
    f. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Kattintson ide [a](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) szerepkör Azure AD-beli konfigurálásának konfigurálásért.

7. A **Set up Single Sign-On with SAML** (Egyszeri tanúsítvány beállítása SAML-sel) lapon, az **SAML signing Certificate (SAML** aláíró tanúsítvány) szakaszban kattintson a **Download** (Letöltés) gombra a tanúsítvány **(Base64)** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

8. A **Zscaler 2** beállítása szakaszban másolja ki a megfelelő URL-cím(eke)t a saját követelményeinek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza a Azure Portal **lehetőséget,** Azure Active Directory **felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja fel a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát a Zscaler Two-hoz való hozzáférés engedélyezésével.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Zscaler Two lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-zscaler-two-sso"></a>Zscaler két SSO konfigurálása

1. A Zscaler Two konfigurációjának automatizálása érdekében telepítenie kell Saját alkalmazások **Biztonságos** bejelentkezés böngészőbővítményt a Bővítmény **telepítése gombra kattintva.**

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Zscaler Two** beállítása elemre, amely a Zscaler Two alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler Two-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3–6. lépést.

    ![SSO beállítása](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler Twot, nyisson meg egy új webböngészőablakot, és jelentkezzen be a Zscaler Two vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Az Adminisztráció **és > hitelesítési > lapon hajtsa** végre a következő lépéseket:
   
    ![Képernyőkép a Zscaler One webhelyről a leírt lépésekkel.](./media/zscaler-two-tutorial/administrator.png "Felügyelet")

    a. A Hitelesítés típusa alatt válassza az **SAML lehetőséget.**

    b. Kattintson **az SAML konfigurálása elemre.**

5. Az **SAML szerkesztése ablakban** hajtsa végre a következő lépéseket: és kattintson a Mentés gombra.  
            
    ![Felhasználók és & kezelése](./media/zscaler-two-tutorial/authentication.png "Felhasználók és & kezelése")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a bejelentkezési **URL-címet,** amelyet a Azure Portal.

    b. A Login **Name Attribute (Bejelentkezési név attribútuma) szövegmezőbe** írja be a **NameID nevet.**

    c. Kattintson **az Upload (Feltöltés)** gombra a nyilvános SSL-tanúsítványban Azure Portal azure saml aláíró tanúsítvány **feltöltéséhez.**

    d. Az SAML automatikus **kiépítésének engedélyezése lehetőség átállítása.**

    e. A User **Display Name Attribute** (Felhasználónév attribútuma) szövegmezőbe írja be a **displayName** szöveget, ha engedélyezni szeretné az SAML automatikus kiépítést a displayName attribútumok számára.

    f. A **Group Name Attribute (Csoportnév attribútuma)** szövegmezőbe írja be **a memberOf (TagOf)** szöveget, ha engedélyezni szeretné az SAML automatikus kiépítést a memberOf attribútumok számára.

    : Ha **engedélyezni** szeretné  az SAML automatikus kiépítést a részlegattribútumokhoz, a Részleg neve attribútum adja meg a részleget.

    h. Kattintson a **Mentés** gombra.

6. A Felhasználói **hitelesítés konfigurálása párbeszédpanelen** hajtsa végre a következő lépéseket:

    ![Képernyőkép a Felhasználói hitelesítés konfigurálása párbeszédpanelről, az Aktiválás elem bejelölve.](./media/zscaler-two-tutorial/activation.png)

    a. Vigye az egérmutatót **az Aktiválás** menü fölé a bal alsó sarokban.

    b. Kattintson az **Activate (Aktiválás) gombra.**

## <a name="configuring-proxy-settings&quot;></a>Proxybeállítások konfigurálása

### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>A proxybeállítások konfigurálása a Internet Explorer

1. Indítsa **el Internet Explorer.**

2. Válassza **az Internetbeállítások** lehetőséget az **Eszközök menüben** az **Internetbeállítások párbeszédpanel megnyitásához.**   
    
     ![Internetbeállítások](./media/zscaler-two-tutorial/internet.png &quot;Internetbeállítások")

3. Kattintson a **Kapcsolatok fülre.**   
  
     ![Kapcsolatok](./media/zscaler-two-tutorial/ic769493.png "Kapcsolatok")

4. Kattintson **a LAN-beállítások** elemre a **LAN-beállítások párbeszédpanel megnyitásához.**

5. A Proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-two-tutorial/proxy.png "Proxykiszolgáló")

    a. Válassza **a Use a proxy server for your LAN (Proxykiszolgáló használata a helyi hálózathoz) lehetőséget.**

    b. A Cím szövegmezőbe írja be az átjáró **szöveget. Zscaler Two.net**.

    c. A Port szövegmezőbe írja be a **80 szöveget.**

    d. Helyi **címek esetén válassza a Proxykiszolgáló megkerülése lehetőséget.**

    e. Kattintson **az OK** gombra a Helyi hálózat **(LAN) beállításai párbeszédpanel bezárásához.**

6. Kattintson **az OK** gombra az **Internetbeállítások párbeszédpanel bezárásához.**


### <a name="create-zscaler-two-test-user"></a>Zscaler két tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a Zscaler Two-ban. A Zscaler Two támogatja az alapértelmezés szerint engedélyezett, időben elérhető felhasználókiépítést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a Zscaler Two-ban, a hitelesítés után létrejön egy új.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Zscaler két támogatási csapatával.](https://www.zscaler.com/company/contact)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Zscaler két bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Zscaler két bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a Zscaler Two csempére kattint a Saját alkalmazások, a rendszer átirányítja a Zscaler Két bejelentkezési URL-címre. További információ a Saját alkalmazások: [Bevezetés a](../user-help/my-apps-portal-end-user-access.md)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A Zscaler Two konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-any-app)
