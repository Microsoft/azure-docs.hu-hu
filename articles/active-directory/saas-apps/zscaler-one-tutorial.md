---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler One-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Zscaler között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 3f825e247aff5c8fc53eb8610f33bd8e0b7fce3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735642"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Oktatóanyag: Azure Active Directory integráció az Zscaler One-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler egyet Azure Active Directory (Azure AD) használatával.
A Zscaler One és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá egy Zscaler.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Zscaler egy (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Zscaler egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler az **SP** által kezdeményezett egyszeri bejelentkezést támogatja

* A Zscaler egy **időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-zscaler-one-from-the-gallery"></a>Zscaler hozzáadása a gyűjteményből

A Zscaler egy Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a Zscaler egyet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler** kifejezést a keresőmezőbe.
1. Válassza ki a **Zscaler egyet** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-one"></a>Azure AD SSO konfigurálása és tesztelése az Zscaler-hez

Konfigurálja és tesztelje az Azure AD SSO-t az Zscaler-mel egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és egy, a Zscaler tartozó felhasználó között.

Ha az Azure AD SSO-t az Zscaler-mel szeretné konfigurálni és tesztelni, hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
2. A **[Zscaler konfigurálása egy egyszeri bejelentkezéshez](#configure-zscaler-one-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Zscaler egy felhasználóval](#create-zscaler-one-test-user)** , hogy rendelkezzen egy, a felhasználó Azure ad-Britta kapcsolódó, a Zscaler.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Zscaler egy** alkalmazás-integráció oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a Zscaler egy alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az értéket a tényleges Sign-On URL-címmel frissíti. Az érték beszerzéséhez vegye fel a kapcsolatot a [Zscaler egy ügyfél-támogatási csapatával](https://www.zscaler.com/company/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az Zscaler egy alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

6. A fentiek mellett a Zscaler egy alkalmazás néhány további attribútumot vár az SAML-válaszban. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Name | Forrás attribútum |
    | ---------| ------------ |
    | memberOf | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.
    
    f. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > [Ide kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben.

7. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. Az **Zscaler beállítása** szakaszban másolja ki a megfelelő URL-címeket (ek) a követelmény szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít egy Zscaler.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **Zscaler egyet**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-zscaler-one-sso"></a>Zscaler konfigurálása egyszeri bejelentkezéshez

1. Ha az Zscaler-n belül szeretné automatizálni a konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Setup Zscaler (telepítés** ) elemre, majd a Zscaler egy alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat az Zscaler való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Egyszeri bejelentkezés beállítása](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler, nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler egy vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Lépjen az **adminisztráció > hitelesítés > hitelesítési beállítások** lapra, és hajtsa végre a következő lépéseket:
   
    ![A képernyőképen a Zscaler egy hely jelenik meg, amely a leírt lépéseket ismerteti.](./media/zscaler-one-tutorial/ic800206.png "Felügyelet")

    a. A hitelesítés típusa területen válassza az **SAML** elemet.

    b. Kattintson az **SAML konfigurálása** elemre.

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket:, majd kattintson a Mentés gombra.  
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-one-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** szövegmezőbe írja be a **NameID** nevet.

    c. Kattintson a **feltöltés** gombra, és töltse fel a **nyilvános SSL-tanúsítványban** Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenített név attribútuma** szövegmezőbe írja be a **DisplayName** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a DisplayName attribútumokhoz.

    f. A **Csoportnév-attribútum** szövegmezőbe írja be a **memberof** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a memberOf attribútumaihoz.

    : A **részleg neve attribútumban** adja meg a **részleget** , ha engedélyezni szeretné az SAML automatikus kiépítés részleg attribútumait.

    h. Kattintson a **Mentés** gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználói hitelesítés konfigurálása párbeszédpanel a kiválasztott aktiválással.](./media/zscaler-one-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás** gombra.

## <a name="configuring-proxy-settings&quot;></a>Proxybeállítások konfigurálása
### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét.   

    ![Internetbeállítások](./media/zscaler-one-tutorial/ic769492.png &quot;Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   

    ![Kapcsolatok](./media/zscaler-one-tutorial/ic769493.png "Kapcsolatok")

4. A LAN- **Beállítások** párbeszédpanel megnyitásához kattintson a **LAN-beállítások** elemre.

5. A proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-one-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.

    b. A címek szövegmezőbe írja be az **átjáró értéket. Zscaler One.net**.

    c. A port szövegmezőbe írja be a következőt: **80**.

    d. Válassza **a proxykiszolgáló kihagyása helyi címeknél** lehetőséget.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

7. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="create-zscaler-one-test-user"></a>Zscaler létrehozása egy tesztelési felhasználóval

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscaler One-ban. A Zscaler egy olyan igény szerinti felhasználói üzembe helyezést támogat, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Zscaler-ben, akkor a hitelesítés után létrejön egy újat.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Zscaler egy támogatási csoporthoz](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Zscaler egy bejelentkezési URL-címre, amelyen kezdeményezheti a bejelentkezési folyamatot. 

* Nyissa meg közvetlenül a Zscaler egy bejelentkezési URL-címet, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Zscaler egy csempére kattint, a rendszer átirányítja a Zscaler egy bejelentkezési URL-címre. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Zscaler konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
