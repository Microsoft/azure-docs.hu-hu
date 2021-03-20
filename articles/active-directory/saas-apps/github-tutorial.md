---
title: 'Oktatóanyag: Azure Active Directory a GitHub vállalati felhőalapú szervezettel való integrációt | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és egy GitHub Enterprise Cloud Organization között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: c87f66e48aa7b39a7f3fc5f9b8572dd6925abbc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732155"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a GitHub vállalati felhőalapú szervezettel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a GitHub Enterprise Cloud **organizationt** Azure Active Directory (Azure ad) használatával. Amikor a GitHub Enterprise Cloud Organizationt az Azure AD-vel integrálja, a következőket teheti:

* A GitHub Enterprise Cloud Organization szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* A GitHub Enterprise Cloud Organization elérésének kezelése egyetlen központi helyen – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció egy GitHub Enterprise Cloud Organization-szervezettel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* GitHub [vállalati felhőben](https://help.github.com/articles/github-s-products/#github-enterprise)létrehozott GitHub-szervezet, amelyhez a [GitHub Enterprise számlázási csomag](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) szükséges

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A GitHub támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A GitHub támogatja a felhasználók automatikus kiépítését [  (a szervezeti meghívókat)](github-provisioning-tutorial.md)


## <a name="adding-github-from-the-gallery"></a>A GitHub hozzáadása a gyűjteményből

A GitHub Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a GitHubot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **GitHub** kifejezést a keresőmezőbe.
1. Válassza a **GitHub Enterprise Cloud-Organization** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-github"></a>Azure AD SSO konfigurálása és tesztelése a GitHubhoz

Konfigurálja és tesztelje az Azure AD SSO-t a GitHubon egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a GitHubon lévő kapcsolódó felhasználó között.

Az Azure AD SSO és a GitHub használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[GITHUB SSO konfigurálása](#configure-github-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy GitHub-teszt felhasználót](#create-github-test-user)** – hogy a githubon lévő B. Simon-nek egy, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerét társítsa.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **GitHub** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

   a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/orgs/<Organization ID>/sso`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/orgs/<Organization ID>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem valódi értékek. Ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosító és válasz URL-cím használatával kell frissítenie. Itt javasoljuk, hogy a karakterlánc egyedi értékét használja az azonosítóban. Az értékek beolvasásához lépjen a GitHub-adminisztrátor szakaszra.

5. A GitHub-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol az **egyedi felhasználói azonosító (Name ID)** a **User. userPrincipalName** leképezéssel van leképezve. A GitHub-alkalmazás a **User. mail** használatával rendeli hozzá az **egyedi felhasználói azonosítót (Name ID)** , ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútumok leképezését.

    ![A "szerkesztés" ikonnal jelölt "felhasználói attribútumok" szakaszt megjelenítő képernyőkép.](common/edit-attribute.png)

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **GitHub beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a GitHubhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **GitHub** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.

    ![felhasználói szerepkör](./media/github-tutorial/user-role.png)

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-github-sso"></a>GitHub SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezeti webhelyre rendszergazdaként.

2. Navigáljon a **beállításokhoz** , és kattintson a **Biztonság** elemre.

    ![Képernyőfelvétel: a GitHub "szervezeti beállítások" menüjének kijelölése a "biztonság" lehetőség kiválasztásával.](./media/github-tutorial/security.png)

3. Jelölje be az **SAML-hitelesítés engedélyezése** jelölőnégyzetet az egyszeri bejelentkezés konfigurációs mezőinek megjelenítéséhez, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "S A M l egyszeri bejelentkezés" szakaszt jeleníti meg az "1. M L hitelesítés engedélyezése" és az U R L szövegmezők kiemelésével.](./media/github-tutorial/saml-sso.png)

    a. Másolja az **egyszeri bejelentkezési URL-címet** , és illessze be ezt az értéket a **bejelentkezési URL** szövegmezőbe a Azure Portal **alapszintű SAML-konfigurációjában** .
    
    b. Másolja a **felhasználói szolgáltatás URL-címének** értékét, és illessze be ezt az értéket a Azure Portal **ALAPszintű SAML-KONFIGURÁCIÓJÁNAK** **Válasz URL-** szövege mezőjébe.

4. Konfigurálja a következő mezőket:

    ![A "bejelentkezési URL", a "kiállító" és a "nyilvános tanúsítvány" szövegmezőket megjelenítő képernyőkép.](./media/github-tutorial/configure.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    b. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Nyissa meg a letöltött tanúsítványt Azure Portal a Jegyzettömbben, illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

    d. Kattintson az **Edit (Szerkesztés** ) ikonra **az aláírási módszer** és a **kivonatoló módszer** **RSA-SHA1** és **SHA1** – **RSA-sha256** és **sha256** szerinti szerkesztéséhez az alább látható módon.
    
    e. Frissítse az **állítási fogyasztói szolgáltatás URL-címét (válasz URL-címe)** az alapértelmezett URL-címről, hogy a GitHub URL-címe megfeleljen az Azure-alkalmazás REGISZTRÁCIÓjának URL-címének.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kattintson a **SAML-konfiguráció tesztelése** elemre annak ellenőrzéséhez, hogy nincs-e érvényesítési hiba vagy hiba az egyszeri bejelentkezés során.

    ![Beállítások](./media/github-tutorial/test.png)

6. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az egyszeri bejelentkezés a GitHubon egy adott szervezetbe történik a GitHubon, és nem helyettesíti a GitHub hitelesítését. Ezért ha a felhasználó github.com-munkamenete lejárt, előfordulhat, hogy az egyszeri bejelentkezés során a GitHub AZONOSÍTÓját/jelszavát kell hitelesítenie.

### <a name="create-github-test-user"></a>GitHub-teszt felhasználó létrehozása

A szakasz célja, hogy létrehozzon egy Britta Simon nevű felhasználót a GitHubban. A GitHub támogatja a felhasználók automatikus üzembe helyezését, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](github-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a GitHub vállalati webhelyére rendszergazdaként.

2. Kattintson a **személyek** elemre.

    ![Képernyőfelvétel: a GitHub-webhely, ahol a felhasználók ki vannak választva.](./media/github-tutorial/people.png "People")

3. Kattintson a **tag meghívása** elemre.

    ![Felhasználók meghívása](./media/github-tutorial/invite-member.png "Felhasználók meghívása")

4. A **tag meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Az **e-mail** szövegmezőbe írja be a Britta Simon-fiók e-mail-címét.

    ![Személyek meghívása](./media/github-tutorial/email-box.png "Személyek meghívása")

    b. Kattintson a **Meghívás küldése** gombra.

    ![A "tag" kijelölt és a "Meghívás küldése" gombra kattintva megjelenő képernyőkép a "tag meghívása" párbeszédpanelt jeleníti meg.](./media/github-tutorial/send-invitation.png "Személyek meghívása")

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a GitHub bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a GitHub bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások GitHub csempére kattint, a rendszer átirányítja a GitHub bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A GitHub konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)