---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler Two-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler között.
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
ms.openlocfilehash: 8c3d76f24cce860538dc3bd5dcf29b59708bdb5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726244"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler Two-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler kettőt Azure Active Directory (Azure AD) használatával. Ha a Zscaler két Azure AD-val integrálja, a következőket teheti:

* Vezérlőelem az Azure AD-ben, amely két Zscaler fér hozzá.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy Zscaler a két Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zscaler két egyszeri bejelentkezést engedélyező előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler két verziója támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Zscaler két verziója támogatja **a** felhasználók üzembe helyezését

## <a name="adding-zscaler-two-from-the-gallery"></a>Két Zscaler hozzáadása a katalógusból

A Zscaler két Azure AD-integrációjának konfigurálásához hozzá kell adnia a Zscaler kettőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler Two** kifejezést a keresőmezőbe.
1. Válassza az **Zscaler kettőt** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Az Azure AD SSO konfigurálása és tesztelése két Zscaler

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler két használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Zscaler-ben.

Az Azure AD SSO két Zscaler való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Zscaler két egyszeri bejelentkezés konfigurálása](#configure-zscaler-two-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre két Zscaler](#create-zscaler-two-test-user)** -t, hogy a B. Simon a Zscaler két tagja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Zscaler három** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a ZScaler két alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az értéket a tényleges Sign-On URL-címmel frissíti. Az érték beszerzéséhez vegye fel a kapcsolatot a [Zscaler két ügyfél-támogatási csapatával](https://www.zscaler.com/company/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A Zscaler két alkalmazása egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

6. A fentiek mellett a Zscaler két alkalmazás is vár néhány további attribútumot, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Name | Forrás attribútum |
    | ---------| ------------ |
    | memberOf | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a felhasználói jogcímek új jogcím hozzáadására szolgáló lehetőséggel jelennek meg.](common/new-save-attribute.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése párbeszédpanel, amelyen megadhatja a leírt értékeket.](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.
    
    f. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > [Ide kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben.

7. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler két beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Zscaler.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Zscaler két** lehetőséget.
2. Az alkalmazások listában válassza a **Zscaler két** lehetőséget.
3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.
4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.
5. A **felhasználók és csoportok** párbeszédpanelen válassza ki a **Britta Simon** elemet a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![Képernyőfelvétel: a felhasználók és csoportok párbeszédpanel, ahol kijelölhet egy felhasználót.](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. A **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![Képernyőfelvétel: a szerepkör kiválasztása párbeszédpanel, ahol kiválaszthat egy felhasználói szerepkört.](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

    ![Képernyőfelvétel: a hozzárendelés hozzáadása párbeszédpanel, ahol kiválaszthatja a hozzárendelés lehetőséget.](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

## <a name="configure-zscaler-two-sso"></a>Zscaler két SSO konfigurálása

1. Ha a konfigurációt a Zscaler belül szeretné automatizálni, a **bővítmény telepítése** lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítás Zscaler két** lehetőségre a Zscaler két alkalmazásra. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Egyszeri bejelentkezés beállítása](common/setup-sso.png)

3. Ha két kézzel szeretné beállítani a Zscaler, nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler két vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Lépjen az **adminisztráció > hitelesítés > hitelesítési beállítások** lapra, és hajtsa végre a következő lépéseket:
   
    ![A képernyőképen a Zscaler egy hely jelenik meg, amely a leírt lépéseket ismerteti.](./media/zscaler-two-tutorial/ic800206.png "Felügyelet")

    a. A hitelesítés típusa területen válassza az **SAML** elemet.

    b. Kattintson az **SAML konfigurálása** elemre.

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket:, majd kattintson a Mentés gombra.  
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-two-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** szövegmezőbe írja be a **NameID** nevet.

    c. Kattintson a **feltöltés** gombra, és töltse fel a **nyilvános SSL-tanúsítványban** Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenített név attribútuma** szövegmezőbe írja be a **DisplayName** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a DisplayName attribútumokhoz.

    f. A **Csoportnév-attribútum** szövegmezőbe írja be a **memberof** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a memberOf attribútumaihoz.

    : A **részleg neve attribútumban** adja meg a **részleget** , ha engedélyezni szeretné az SAML automatikus kiépítés részleg attribútumait.

    h. Kattintson a **Mentés** gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználói hitelesítés konfigurálása párbeszédpanel a kiválasztott aktiválással.](./media/zscaler-two-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás** gombra.

## <a name="configuring-proxy-settings&quot;></a>Proxybeállítások konfigurálása
### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét.   
    
     ![Internetbeállítások](./media/zscaler-two-tutorial/ic769492.png &quot;Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-two-tutorial/ic769493.png "Kapcsolatok")

4. A LAN- **Beállítások** párbeszédpanel megnyitásához kattintson a **LAN-beállítások** elemre.

5. A proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-two-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.

    b. A címek szövegmezőbe írja be az **átjáró értéket. Zscaler Two.net**.

    c. A port szövegmezőbe írja be a következőt: **80**.

    d. Válassza **a proxykiszolgáló kihagyása helyi címeknél** lehetőséget.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.


### <a name="create-zscaler-two-test-user"></a>Zscaler létrehozása két tesztelési felhasználóval

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscaler Two-ban. A Zscaler két verziója támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Zscaler-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Zscaler két támogatási csapatához](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a Zscaler két bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen a Zscaler két bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások Zscaler két csempére kattint, a rendszer átirányítja a Zscaler két bejelentkezési URL-címre. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Zscaler két beállítása után kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
