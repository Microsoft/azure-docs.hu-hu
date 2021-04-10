---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a GitHub Enterprise Cloud-Enterprise-fiókkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a GitHub Enterprise Cloud-Enterprise-fiók között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a GitHub Enterprise Cloud-Enterprise-fiókkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a GitHub Enterprise Cloud-Enterprise fiókot Azure Active Directory (Azure AD-val). Ha a GitHub Enterprise Cloud-Enterprise fiókot az Azure AD-vel integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér egy GitHub vállalati fiókhoz és a vállalati fiókban lévő szervezetekhez.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy [GitHub Enterprise-fiók](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Egy GitHub-felhasználói fiók, amely egy vállalati fiók tulajdonosa. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A GitHub Enterprise Cloud-Enterprise fiók támogatja az **SP** -t és a **IDENTITÁSSZOLGÁLTATÓ** kezdeményezett SSO-t.
* A GitHub Enterprise Cloud-Enterprise-fiókja **csak időben támogatja a** felhasználók üzembe helyezését.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>GitHub Enterprise Cloud-Enterprise-fiók hozzáadása a katalógusból

A GitHub Enterprise Cloud-Enterprise-fiók Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a GitHub Enterprise Cloud-Enterprise fiókot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **GitHub Enterprise Cloud-Enterprise-fiók** kifejezést a keresőmezőbe.
1. Válassza ki a **GitHub Enterprise Cloud-Enterprise fiókot** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Azure AD SSO konfigurálása és tesztelése a GitHub Enterprise Cloud-Enterprise-fiókhoz

Konfigurálja és tesztelje az Azure AD SSO-t a GitHub Enterprise Cloud-Enterprise-fiókkal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a GitHub Enterprise Cloud-Enterprise fiókban.

Az Azure AD SSO és a GitHub Enterprise Cloud-Enterprise fiókjának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendeljen Azure ad-felhasználót és a felhasználói fiók tesztelése a GitHub-alkalmazáshoz](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** – a felhasználói fiók engedélyezéséhez és a felhasználó `B.Simon` Azure ad-alapú egyszeri bejelentkezés használatára való teszteléséhez.
1. Az **[SAML engedélyezése és tesztelése a vállalati fiók és annak szervezetei számára](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
    1. **[Egyszeri bejelentkezés tesztelése egy másik vállalati fiók tulajdonosával vagy szervezeti fiókkal](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **GitHub Enterprise Cloud-Enterprise Account** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Cserélje le a helyére `<ENTERPRISE-SLUG>` a GitHub Enterprise-fiókjának tényleges nevét.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateBase64.png)

1. A **GitHub Enterprise Cloud-Enterprise fiók beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy teszt felhasználót hoz létre a Azure Portal nevű felhasználó `B.Simon` .

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Azure AD-felhasználó és a felhasználói fiók tesztelése a GitHub-alkalmazáshoz

Ebben a szakaszban engedélyezheti `B.Simon` és felhasználói fiókja az Azure egyszeri bejelentkezést a GitHub Enterprise Cloud-Enterprise-fiókhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **GitHub Enterprise Cloud-Enterprise fiók** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** és a felhasználói fiók lehetőséget a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Az SAML engedélyezése és tesztelése a vállalati fiók és annak szervezetei számára

Ha az egyszeri bejelentkezést a **GitHub Enterprise Cloud-Enterprise fiók** oldalán szeretné konfigurálni, kövesse a [GitHub dokumentációjában](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)felsorolt lépéseket. 
1. Jelentkezzen be a GitHub.com olyan felhasználói fiókkal, amely [vállalati fiók tulajdonosa](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Másolja az értéket az `Login URL` alkalmazás mezőjéből a Azure Portalból, és illessze be a `Sign on URL` GitHub Enterprise-fiók SAML-beállításainak mezőjébe. 
1. Másolja az értéket az `Azure AD Identifier` alkalmazás mezőjéből a Azure Portalból, és illessze be a `Issuer` GitHub Enterprise-fiók SAML-beállításainak mezőjébe. 
1. Másolja a fenti lépésekben letöltött **tanúsítvány-(Base64-)** fájl tartalmát Azure Portal és illessze be őket a GitHub Enterprise-fiók SAML-beállításainak megfelelő mezőjébe. 
1. Kattintson a elemre `Test SAML configuration` , és erősítse meg, hogy a GitHub Enterprise-fiókból sikeresen tud hitelesíteni az Azure ad-t.
1. Ha a teszt sikeres volt, mentse a beállításokat. 
1. Miután először a GitHub Enterprise-fiókból hitelesíti az SAML-t, a rendszer létrehoz egy _csatolt külső identitást_ a GitHub Enterprise-fiókban, amely a bejelentkezett GitHub-felhasználói fiókot társítja az Azure ad felhasználói fiókkal.  
 
Miután engedélyezte az SAML SSO-t a GitHub Enterprise-fiókjához, az SAML SSO alapértelmezés szerint engedélyezve van a vállalati fiók tulajdonában lévő összes szervezet számára. Minden tagnak hitelesítenie kell magát az SAML SSO használatával, ha hozzáférést szeretne adni azokhoz a szervezetekhez, ahol azok tagjai, és a vállalati tulajdonosoknak az SAML SSO használatával kell hitelesíteniük a vállalati fiókokhoz való hozzáférés során.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Egyszeri bejelentkezés tesztelése egy másik vállalati fiók tulajdonosa vagy szervezeti tagja fiókkal

Miután beállította az SAML-integrációt a GitHub Enterprise-fiókhoz (amely a vállalati fiók GitHub-szervezeteire is vonatkozik), az Azure AD-ben az alkalmazáshoz hozzárendelt egyéb vállalati fiókok tulajdonosainak el kell tudniuk érni a GitHub Enterprise-fiók URL-címét ( `https://github.com/enterprises/<enterprise account>` ), hitelesíteniük kell az SAML-n keresztül, és el kell érni a szabályzatokat és a beállításokat a GitHub Enterprise-fiók 

A vállalati fiókban dolgozó szervezet tulajdonosának meg kell tudnia [hívni a felhasználót, hogy csatlakozzon a GitHub-szervezethez](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Jelentkezzen be a GitHub.com egy szervezet tulajdonosi fiókjával, és kövesse a cikk lépéseit, hogy meghívja `B.Simon` a szervezetet. Létre kell hozni egy GitHub-felhasználói fiókot, `B.Simon` Ha még nem létezik ilyen. 

A GitHub-szervezet hozzáférésének tesztelése a vállalati fiókhoz a `B.Simon` felhasználói fiókkal:
1. Meghívja a `B.Simon` vállalati fiókkal rendelkező szervezetet szervezete tulajdonosának. 
1. Jelentkezzen be a GitHub.com-be az `B.Simon` Azure ad felhasználói fiókhoz csatolni kívánt felhasználói fiókkal.
1. Jelentkezzen be az Azure AD-be a `B.Simon` felhasználói fiók használatával.
1. Nyissa meg a GitHub-szervezetet. A felhasználónak meg kell adnia a hitelesítést az SAML-n keresztül. A sikeres SAML-hitelesítés után `B.Simon` képesnek kell lennie a szervezeti erőforrásokhoz való hozzáférésre. 

## <a name="next-steps"></a>Következő lépések

Ha a GitHub Enterprise Cloud-Enterprise fiókot konfigurálta, akkor kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
