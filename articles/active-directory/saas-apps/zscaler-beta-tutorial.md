---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler Beta szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler Beta között.
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
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735584"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler Betatal

Ez az oktatóanyag azt ismerteti, hogyan integrálható a Zscaler Beta Azure Active Directory (Azure AD) szolgáltatással.
A Zscaler Beta és az Azure AD integrálásával a következőket teheti:

* A Zscaler Beta elérését biztosító Azure AD-beli vezérlés.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezzenek a Zscaler Beta szolgáltatásba az Azure AD-fiókjával. A hozzáférés-vezérlés neve egyszeri bejelentkezés (SSO).
* A fiókokat egy központi helyen kezelheti a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler Beta-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést használó Zscaler Beta-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler Beta támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.
* A Zscaler Beta **csak időben támogatja a** felhasználók üzembe helyezését.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta hozzáadása a katalógusból

A Zscaler Beta Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler Beta alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler Beta** kifejezést a keresőmezőbe.
1. Válassza az **Zscaler Beta** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Azure AD SSO konfigurálása és tesztelése a Zscaler Beta-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler Beta használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Zscaler Beta-ban.

Az Azure AD SSO Zscaler Beta-vel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:


1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Zscaler Beta SSO konfigurálása](#configure-zscaler-beta-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Zscaler beta-teszt felhasználó létrehozása](#create-zscaler-beta-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Zscaler rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Zscaler Beta** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** ceruza ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** mezőbe írja be a felhasználók által a Zscaler Beta Beta-alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-értékkel. Az érték beszerzéséhez forduljon a [Zscaler Beta ügyfél-támogatási csapatához](https://www.zscaler.com/company/contact).

5. A Zscaler Beta alkalmazás megadott formátumban várja az SAML-kijelentéseket. Egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Válassza a **Szerkesztés** lehetőséget a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

6. A Zscaler Beta alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. Az alábbi táblázatban látható módon adja hozzá az SAML-jogkivonat attribútumot a felhasználói **jogcímek** szakaszban, a **felhasználói attribútumok** párbeszédpanelen.
    
    | Name | Forrás attribútum | 
    | ---------------| --------------- |
    | memberOf  | User. assignedroles |

    a. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** mezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névtér** mezőt.

    d. A **forrás** mezőben válassza az **attribútum** lehetőséget.

    e. A **forrás attribútum** listáról adja meg az adott sorhoz megjelenő attribútum értékét.

    f. Válassza az **OK** lehetőséget.

    : Kattintson a **Mentés** gombra.

    > [!NOTE]
    > [Ide kattintva](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben.

7. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** elemet a tanúsítvány letöltéséhez **(Base64)**. Mentse a számítógépére.

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler Beta beállítása** szakaszban másolja a követelményekhez szükséges URL-címeket:

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Zscaler Beta elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Zscaler Beta** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zscaler-beta-sso"></a>Zscaler Beta SSO konfigurálása

1. A Zscaler Beta-on belüli konfiguráció automatizálásához telepítse a **saját alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőség kiválasztásával.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, a **Zscaler Beta beállítása** lehetőség választásával a Zscaler Beta alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler Beta rendszerbe való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3 – 6. lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. A Zscaler Beta manuális beállításához nyisson meg egy új böngészőablakot. Jelentkezzen be rendszergazdaként a Zscaler Beta vállalati webhelyre, és kövesse az alábbi lépéseket.

4. Nyissa meg a **felügyeleti**  >  **hitelesítési**  >  **hitelesítési beállításokat**, és kövesse az alábbi lépéseket.
   
    ![Felügyelet](./media/zscaler-beta-tutorial/ic800206.png "Felügyelet")

    a. A **Hitelesítés típusa** területen válassza az **SAML** lehetőséget.

    b. Válassza az **SAML konfigurálása** lehetőséget.

5. Az **SAML szerkesztése** ablakban kövesse az alábbi lépéseket: 
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-beta-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** mezőbe írja be a **NameID** nevet.

    c. A **nyilvános SSL-tanúsítvány** mezőben válassza a **feltöltés** lehetőséget a Azure Portal letöltött Azure SAML-aláíró tanúsítvány feltöltéséhez.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenítendő neve attribútum** mezőjébe írja be a **DisplayName** értéket, ha engedélyezni szeretné a DISPLAYNAME attribútumokhoz tartozó SAML-attribútumok kiépítését.

    f. A **Csoportnév-attribútum** mezőben adja meg a **memberof** értéket, ha engedélyezni szeretné az SAML-kiépítést a memberOf attribútumaihoz.

    : A részleg **neve attribútum** mezőben adja meg a **részleg** értéket, ha engedélyezni szeretné a részleg attribútumainak SAML-kiépítését.

    h. Kattintson a **Mentés** gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Aktiválás menü és aktiválás gomb](./media/zscaler-beta-tutorial/ic800207.png)

    a. Vigye a kurzort a bal alsó sarokban lévő **aktiválási** menüre.

    b. Válassza az **aktiválás** lehetőséget.

## <a name="configure-proxy-settings&quot;></a>Proxybeállítások konfigurálása
A proxybeállítások az Internet Explorerben való konfigurálásához kövesse az alábbi lépéseket.

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét. 
    
     ![Internetbeállítások párbeszédpanel](./media/zscaler-beta-tutorial/ic769492.png &quot;Internetbeállítások")

3. Válassza a **kapcsolatok** fület. 
  
     ![Connections (Kapcsolatok) lap](./media/zscaler-beta-tutorial/ic769493.png "Kapcsolatok")

4. A **helyi hálózati (LAN) beállítások** párbeszédpanel megnyitásához válassza a **LAN-beállítások** lehetőséget.

5. A **proxykiszolgáló** szakaszban hajtsa végre az alábbi lépéseket: 
   
    ![Proxykiszolgáló szakasz](./media/zscaler-beta-tutorial/ic769494.png "Proxykiszolgáló")

    a. Jelölje be a **proxykiszolgáló használata a helyi hálózat számára** jelölőnégyzetet.

    b. A **címterület** mezőbe írja be az **átjáró értéket. Zscaler Beta.net**.

    c. A **port** mezőbe írja be a **80** értéket.

    d. Jelölje be a **proxykiszolgáló kihagyása helyi címeknél** jelölőnégyzetet.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK gombra** .

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK gombra** .

### <a name="create-zscaler-beta-test-user"></a>Zscaler beta-teszt felhasználó létrehozása

Ebben a szakaszban a Simon Britta felhasználó a Zscaler Beta-ban jön létre. A Zscaler Beta támogatja az **igény szerinti felhasználói üzembe** helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban semmi teendője van. Ha egy felhasználó még nem létezik a Zscaler Beta-ban, a hitelesítés után létrejön egy újat.

>[!Note]
>Ha manuálisan szeretné létrehozni a felhasználót, forduljon a [Zscaler Beta támogatási csapatához](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Zscaler Beta bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Zscaler Beta bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Zscaler Beta csempére kattint, a rendszer átirányítja a Zscaler Beta bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Zscaler Beta konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
