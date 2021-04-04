---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Nintex Promapp | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Nintex Promapp között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: fc31195e7f544bdce7fe2f135a39cb9992875d0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505884"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Nintex-Promapp

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Nintex-Promapp a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Nintex Promapp, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Nintex Promapp.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Nintex Promapp az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Nintex Promapp egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Nintex Promapp támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Nintex Promapp **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-nintex-promapp-from-the-gallery"></a>Nintex-Promapp hozzáadása a gyűjteményből

A Nintex-Promapp Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Nintex-Promapp a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Nintex Promapp** kifejezést a keresőmezőbe.
1. Válassza ki a **Nintex Promapp** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Nintex Promapp

Konfigurálja és tesztelje az Azure AD SSO-t a Nintex Promapp egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Nintex Promapp.

Az Azure AD SSO Nintex-Promapp való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Nintex-PROMAPP SSO konfigurálása](#configure-nintex-promapp-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Nintex-Promapp-teszt felhasználó létrehozása](#create-nintex-promapp-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon Nintex-Promapp rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Nintex Promapp** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    1. Az **azonosító** mezőbe írjon be egy URL-címet ebben a mintában:

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Az Azure AD-integráció a Nintex Promapp jelenleg csak a szolgáltatás által kezdeményezett hitelesítéshez van konfigurálva. (Azaz a Nintex Promapp URL-címe kezdeményezi a hitelesítési folyamatot.) A **Válasz URL-címe** mező azonban kötelező mező.

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet ebben a mintában:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** -cím mezőben adjon meg egy URL-címet ebben a mintában: `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét és a bejelentkezési URL-címet kell használnia. Az értékek lekéréséhez lépjen kapcsolatba a [Nintex Promapp támogatási csapatával](https://www.promapp.com/about-us/contact-us/) . A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Nintex Promapp beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Nintex Promapp.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Nintex Promapp** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-nintex-promapp-sso"></a>Nintex Promapp SSO konfigurálása

1. Jelentkezzen be a Nintex Promapp vállalati webhelyre rendszergazdaként.

2. Az ablak tetején található menüben válassza a **rendszergazda** elemet:

    ![Rendszergazda kiválasztása][12]

3. Válassza a **Konfigurálás** lehetőséget:

    ![Konfigurálás kiválasztása][13]

4. A **Biztonság** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Biztonság párbeszédpanel][14]

    1. Illessze be a Azure Portalból másolt **bejelentkezési URL-címet** az **SSO-login URL-cím** mezőbe.

    1. Az **egyszeri bejelentkezés – egyszeri bejelentkezési mód** listán válassza a nem **kötelező** lehetőséget. Kattintson a **Mentés** gombra.

       > [!NOTE]
       > A választható mód csak tesztelésre szolgál. Miután elégedett a konfigurációval, válassza a **kötelező** lehetőséget az egyszeri bejelentkezéses **bejelentkezési mód** listában, hogy az összes felhasználóra kikényszerítse az Azure ad-hitelesítést.

    1. Nyissa meg az előző szakaszban letöltött tanúsítványt a Jegyzettömbben. Másolja a tanúsítvány tartalmát az első sor nélkül (**-----BEGIN certificate-----**) vagy az utolsó sort (**-----END Certificate-----**). Illessze be a tanúsítvány tartalmát az **SSO-x. 509 tanúsítvány** mezőbe, majd kattintson a **Mentés** gombra.

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Nintex Promapp. A Nintex Promapp támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Nintex Promapp, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Nintex Promapp csempére kattint, automatikusan be kell jelentkeznie arra a Nintex-Promapp, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Nintex Promapp az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png