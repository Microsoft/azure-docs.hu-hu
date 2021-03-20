---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pulse Secure Virtual Traffic Managerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pulse Secure virtuális Traffic Manager között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92505725"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pulse Secure virtuális Traffic Manager

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pulse Secure virtuális Traffic Managert Azure Active Directory (Azure AD) használatával. Ha a Pulse Secure virtuális Traffic Manager az Azure AD-vel integrálja, a következőket teheti:

* Vezérlő az Azure AD-ben, aki hozzáfér a Pulse Secure virtuális Traffic Managerhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Pulse Secure Virtual Traffic Manager Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Pulse Secure virtuális Traffic Manager egyszeri bejelentkezéses (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Pulse Secure Virtual Traffic Manager támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Pulse Secure virtuális Traffic Manager hozzáadása a katalógusból

A Pulse Secure virtuális Traffic Manager Azure AD-be való integrálásának konfigurálásához Pulse Secure virtuális Traffic Managert kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Pulse Secure Virtual Traffic Manager** kifejezést a keresőmezőbe.
1. Válassza a **Pulse Secure virtuális Traffic Manager** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Azure AD SSO konfigurálása és tesztelése a Pulse Secure virtuális Traffic Manager

Konfigurálja és tesztelje az Azure AD SSO-t a Pulse Secure Virtual Traffic Manager egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Pulse Secure virtuális Traffic Managerban.

Az Azure AD SSO és a Pulse Secure Virtual Traffic Manager használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Pulse Secure Virtual Traffic Manager SSO konfigurálása](#configure-pulse-secure-virtual-traffic-manager-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Pulse Secure virtual Traffic Manager test User](#create-pulse-secure-virtual-traffic-manager-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli, Pulse secure virtuális Traffic Manager.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Pulse Secure Virtual Traffic Manager** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<published virtual server FQDN>/saml/consume`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<published virtual server FQDN>/saml/metadata`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez vegye fel a kapcsolatot a [Pulse Secure Virtual Traffic Manager ügyfél-támogatási csapatával](mailto:support@pulsesecure.net) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Pulse Secure Virtual Traffic Manager beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Pulse Secure virtuális Traffic Manager hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Pulse Secure Virtual Traffic Manager** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Pulse Secure Virtual Traffic Manager SSO konfigurálása

Ez a szakasz ismerteti az Azure AD SAML-hitelesítés engedélyezéséhez szükséges konfigurációt a Pulse Virtual Traffic Manageron. Az összes konfigurációs módosítás a Pulse Virtual Traffic Manager a rendszergazdai webes felhasználói felület használatával történik. 

#### <a name="create-a-saml-trusted-identity-provider"></a>SAML megbízható identitás-szolgáltató létrehozása

a. Nyissa meg a **Pulse Virtual Traffic Manager készülék rendszergazdai felhasználói felületét > katalógus > SAML > megbízható azonosítók katalógusa** lapon, és kattintson a **Szerkesztés** gombra.

![SAML-katalógusok lapja](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Adja meg az új SAML megbízható identitás-szolgáltató adatait, másolja az adatokat az Azure AD Enterprise alkalmazásból az egyszeri bejelentkezési beállítások lapon, majd kattintson az **új megbízható identitás-szolgáltató létrehozása** lehetőségre.

![Új megbízható identitás-szolgáltató létrehozása](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* A **név** szövegmezőbe írja be a megbízható identitás szolgáltatójának nevét. 

* A **Entity_id** szövegmezőbe írja be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portalból másolt.  

* Az **URL** szövegmezőbe írja be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt. 

* Nyissa meg a letöltött **tanúsítványt** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat a **tanúsítvány** szövegmezőbe.

c. Ellenőrizze, hogy az új SAML-identitás szolgáltatójának létrehozása sikeres volt-e. 

![Megbízható identitás szolgáltatójának ellenőrzése](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>A virtuális kiszolgáló konfigurálása az Azure AD-hitelesítés használatára

a. Nyissa meg a **Pulse virtual Traffic Manager készülék rendszergazdai felhasználói felületét > Services > Virtual** Servers lapot, és kattintson a korábban létrehozott virtuális kiszolgáló melletti **Szerkesztés** elemre.

![Virtuális kiszolgálók szerkesztése](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. A **hitelesítés** szakaszban kattintson a **Szerkesztés** elemre. 

![Hitelesítés szakasz](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Konfigurálja a következő hitelesítési beállításokat a virtuális kiszolgálóhoz: 

1. Hitelesítés

    ![a virtuális kiszolgáló hitelesítési beállításai](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. Az **Auth! típus mezőben** válassza az **SAML** -szolgáltató elemet. 

    b. Az **Auth! verbose** értéknél állítsa az "igen" értéket az esetleges hitelesítési problémák elhárításához, ellenkező esetben hagyja meg az alapértelmezett értéket "nem" értékre. 

2. Hitelesítési munkamenet kezelése –

    ![Hitelesítési munkamenetek kezelése](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Az **Auth! Session! cookie_name** esetében az alapértelmezett értéket hagyja "VS_SamlSP_Auth" értékre. 

    b. Az **Auth! Session! Timeout** beállításnál hagyja meg az alapértelmezett értéket "7200" értékre. 

    c. Az **Auth! Session! log_external_state** beállításnál állítsa az "igen" értéket a hitelesítési problémák elhárításához, ellenkező esetben hagyja meg az alapértelmezett értéket "nem" értékre. 

    d. Az **Auth! Session! cookie_attributesban** váltson a "HTTPOnly" elemre. 

3. SAML-szolgáltató –

    ![SAML-szolgáltató](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. Az **Auth! SAML! sp_entity_id** szövegmezőben az Azure ad egyszeri bejelentkezési konfigurációs azonosítójának (Entity ID) használt URL-címére kell beállítani. Hasonlóan `https://pulseweb.labb.info/saml/metadata` . 

    b. Az **Auth! SAML! sp_acs_urlban** az Azure ad egyszeri bejelentkezés konfigurációjának újrajátszása URL-címéhez használt URL-cím (a felhasználói szolgáltatás URL-címe) megegyezik. Hasonlóan `https://pulseweb.labb.info/saml/consume` . 

    c. Az **Auth! SAML! identitásszolgáltató** válassza ki az előző lépésben létrehozott **megbízható identitás-szolgáltatót** . 

    d. Az Auth! SAML! time_tolerance az alapértelmezett értéket "5" másodpercre hagyja. 

    e. Az Auth! SAML! nameid_format területen válassza a **meghatározatlan** lehetőséget.

    f. A módosítások alkalmazásához kattintson a lap alján található **frissítés** elemre.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Pulse Secure virtuális Traffic Manager tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Pulse Secure virtuális Traffic Manager. A [Pulse Secure virtual Traffic Manager támogatási csapatával](mailto:support@pulsesecure.net) a felhasználókat a Pulse secure virtuális Traffic Manager platformon veheti fel. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Pulse Secure Virtual Traffic Manager bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

2. Lépjen a Pulse Secure Virtual Traffic Manager bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

3. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Pulse Secure Virtual Traffic Manager csempére kattint, a rendszer átirányítja a Pulse Secure virtuális Traffic Manager bejelentkezési URL-címére. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A Pulse Secure virtuális Traffic Manager konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).