---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a peremhálózati 81-mel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a peremhálózati 81 között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363849"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a peremhálózati 81

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a peremhálózati 81-et Azure Active Directory (Azure AD-val). Ha az Azure AD-vel integrálja a peremhálózati 81-et, a következőket teheti:

* Szabályozza az Azure AD-t, hogy ki férhet hozzá a 81-es peremhálózaton.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek a 81-as körzetbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kerület 81 egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A peremhálózati 81 támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A peremhálózati 81 **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-perimeter-81-from-the-gallery"></a>A 81 kerület hozzáadása a gyűjteményből

Az 81-as peremhálózati környezet Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a szegélyhálózati 81 a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt a keresőmezőbe: **peremhálózati 81** .
1. Válassza ki a **kerület 81** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Azure AD SSO konfigurálása és tesztelése a peremhálózati 81-hez

Konfigurálja és tesztelje az Azure AD SSO-t a peremhálózati 81-mel egy **B. Simon** nevű tesztelési felhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a 81-as peremhálózati kapcsolaton.

Az Azure AD SSO és a peremhálózati 81 közötti konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[szegélyhálózati 81 SSO konfigurálása](#configure-perimeter-81-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre](#create-perimeter-81-test-user)** egy, a 81-81 es tesztelési célú felhasználót –, hogy a "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **peremhálózati 81** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő mintát használó értéket: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot a [peremhálózati 81 ügyfél-támogatási csapatával](mailto:support@perimeter81.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **szegélyhálózati 81 beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a szegélyhálózati 81-hez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **peremhálózat 81** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-perimeter-81-sso"></a>A szegélyhálózati 81 SSO konfigurálása

1. Az 81-es peremhálózati konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **szegélyhálózati 81 beállítása** elemre, majd a peremhálózati 81-alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a 81-as körzetbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha a szegélyhálózati 81-et manuálisan szeretné beállítani, egy másik böngészőablakban jelentkezzen be a szegélyhálózati 81 vállalati webhelyre rendszergazdaként.

4. Lépjen a **Beállítások** pontra, és kattintson az **identitás-szolgáltatók** elemre.

    ![Peremhálózati 81-beállítások](./media/perimeter-81-tutorial/settings.png)

5. Kattintson a **szolgáltató hozzáadása** gombra.

    ![Peremhálózati 81 – szolgáltató hozzáadása](./media/perimeter-81-tutorial/add-provider.png)

6. Válassza ki az **SAML 2,0 Identity Providers** elemet, majd kattintson a **Continue (folytatás** ) gombra.

    ![Peremhálózati 81 – identitás-szolgáltató hozzáadása](./media/perimeter-81-tutorial/add-identity-provider.png)

7. Az **SAML 2,0 Identity Providers** szakaszban hajtsa végre a következő lépéseket:

    ![Peremhálózati 81 – SAML beállítása](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. A **tartomány aliasnevei** szövegmezőbe írja be a tartomány aliasának értékét.

    c. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat a **X509 aláíró tanúsítvány** szövegmezőbe.

    > [!NOTE]
    > Azt is megteheti, hogy a **PEM/tanúsítvány feltöltése** lehetőségre kattint a Azure Portalról letöltött **tanúsítvány (Base64)** feltöltéséhez.
    
    d. Kattintson a **Kész** gombra.

### <a name="create-perimeter-81-test-user"></a>Peremhálózati 81 tesztelési felhasználó létrehozása

Ebben a szakaszban a Britta Simon nevű felhasználó jön létre a 81-as kerületben. A peremhálózati 81 támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a peremhálózati 81-ben, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja az URL-címre a 81-es bejelentkezésre, ahol a bejelentkezési folyamatot kezdeményezheti.  

* Lépjen közvetlenül a kerület 81 bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a peremhálózati 81-re, amelyhez be szeretné állítani az egyszeri bejelentkezést.

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások felületén a 81-es csempére kattint, ha az SP módban van konfigurálva, akkor a bejelentkezési folyamat elindításához és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a peremhálózati 81-be, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A szegélyhálózati 81 konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
