---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Zscaler Three-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72554985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Zscaler-vel – három

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler-ket a Azure Active Directory (Azure AD) használatával. A Zscaler három Azure AD-val való integrálásával a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá a három Zscaler.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy Zscaler a hármat az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Zscaler három egyszeri bejelentkezéses (SSO) előfizetést engedélyez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Zscaler három támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Zscaler három támogatja a felhasználók üzembe helyezésének **időpontját**

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-zscaler-three-from-the-gallery"></a>Három Zscaler hozzáadása a katalógusból

A Zscaler három Azure AD-integrációjának konfigurálásához hozzá kell adnia a Zscaler-hármat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler három** kifejezést a keresőmezőbe.
1. Válassza az **Zscaler három** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése három Zscaler

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler három használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, a Zscaler háromban.

Az Azure AD SSO és a Zscaler három közötti konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Zscaler három egyszeri bejelentkezés konfigurálása](#configure-zscaler-three-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre három Zscaler](#create-zscaler-three-test-user)** , hogy a "B. Simon" párja legyen a három Zscaler, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Zscaler három** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://login.zscalerthree.net/sfc_sso`

1. A Zscaler három alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

6. A fentiek mellett a Zscaler három alkalmazás is vár néhány további attribútumot az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.
    
    | Név | Forrás attribútum |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    > [!NOTE]
    > [Ide kattintva](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Zscaler három beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Zscaler háromhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Zscaler három**lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a **Britta Simon** elemet a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. A **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>A Zscaler konfigurálása három egyszeri bejelentkezéshez

1. A konfiguráció automatizálásához a Zscaler három rendszeren belül telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, a **három beállítás Zscaler** gombra kattintva a Zscaler három alkalmazást fogja irányítani. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Beállítás](common/setup-sso.png)

3. Ha a Zscaler három kézzel szeretné beállítani, nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler három vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Lépjen az **adminisztráció > hitelesítés > hitelesítési beállítások** lapra, és hajtsa végre a következő lépéseket:
   
    ![Felügyeleti](./media/zscaler-three-tutorial/ic800206.png "Felügyelet")

    a. A hitelesítés típusa területen válassza az **SAML**elemet.

    b. Kattintson az **SAML konfigurálása**elemre.

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket:, majd kattintson a Mentés gombra.  
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-three-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** szövegmezőbe írja be a **NameID**nevet.

    c. Kattintson a **feltöltés**gombra, és töltse fel a **nyilvános SSL-tanúsítványban**Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenített név attribútuma** szövegmezőbe írja be a **DisplayName** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a DisplayName attribútumokhoz.

    f. A **Csoportnév-attribútum** szövegmezőbe írja be a **memberof** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a memberOf attribútumaihoz.

    g. A **részleg neve attribútumban** adja meg a **részleget** , ha engedélyezni szeretné az SAML automatikus kiépítés részleg attribútumait.

    h. Kattintson a **Save** (Mentés) gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felügyelet](./media/zscaler-three-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás**gombra.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét.   
    
     ![Internetbeállítások](./media/zscaler-three-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-three-tutorial/ic769493.png "Connections (Kapcsolatok)")

4. A LAN- **Beállítások** párbeszédpanel megnyitásához kattintson a **LAN-beállítások** elemre.

5. A proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-three-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.

    b. A címek szövegmezőbe írja be az **átjáró értéket. Zscaler Three.net**.

    c. A port szövegmezőbe írja be a következőt: **80**.

    d. Válassza **a proxykiszolgáló kihagyása helyi címeknél**lehetőséget.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="create-zscaler-three-test-user"></a>Zscaler létrehozása három tesztelési felhasználóval

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Zscaler Three-ban. A Zscaler három támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Zscaler-ben, akkor a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a Zscaler háromhoz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Zscaler három támogatási csapatához](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Zscaler három csempére kattint, automatikusan be kell jelentkeznie arra a Zscaler, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Zscaler hármat az Azure AD-vel](https://aad.portal.azure.com/)

