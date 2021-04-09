---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Szegmensrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és szegmens között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a szegmenssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a szegmenseket Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a szegmenst, a következőket teheti:

* A szegmenshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való Szegmentálásba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A szegmens egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A szegmens támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A szegmens **csak időben támogatja a** felhasználók üzembe helyezését

* A szegmens konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Szegmens hozzáadása a gyűjteményből

A szegmens Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához tartozó szegmenst.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **szegmens** kifejezést a keresőmezőbe.
1. Válassza az eredmények panel **szegmens** elemét, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a szegmenshez

Konfigurálja és tesztelje az Azure AD SSO-t a szegmens használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a szegmensben lévő kapcsolódó felhasználó között.

Az Azure AD SSO és a szegmens használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Szegmens SSO konfigurálása](#configure-segment-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Szegmens tesztelési felhasználó létrehozása](#create-segment-test-user)** – ha a szegmensben található "B. Simon", a felhasználó Azure ad-képviseletéhez csatolt partneri jogosultsággal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **szegmens** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://app.segment.com`

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét és a bejelentkezési URL-címet kell használnia. Ezen értékek lekérésének lépései az oktatóanyag későbbi részében olvashatók.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **szegmens beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a szegmenshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **szegmens** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-segment-sso"></a>Szegmens SSO konfigurálása

1. Egy új böngészőablakban jelentkezzen be a szegmens vállalati webhelyre rendszergazdaként.

1. Kattintson a **Beállítások ikonra** , és görgessen le a **hitelesítéshez** , majd kattintson a **kapcsolatok** elemre.

    ![Képernyőkép a "beállítások" ikon kiválasztásáról és a "kapcsolatok" lehetőségről a "hitelesítés" menüből.](./media/segment-tutorial/segment1.PNG)

1. Kattintson az **új kapcsolatok hozzáadása** lehetőségre.

    ![Képernyőkép, amely a "kapcsolatok" szakaszt mutatja az "új kapcsolat hozzáadása" gomb kiválasztásával.](./media/segment-tutorial/segment2.PNG)

1. Válassza a **SAML 2,0** lehetőséget a konfiguráláshoz, majd kattintson a **Kapcsolódás kiválasztása** gombra.

    ![A "kapcsolatok kiválasztása" szakaszt ábrázoló képernyőkép, amely az "S A M 2,0" és a "kapcsolatok kiválasztása" gomb kiválasztásával jelenik meg.](./media/segment-tutorial/segment3.PNG)

1. A következő oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép: az "Identity Provider konfigurálása" lap "egyszeres Sign-On U R L" és "célközönség U R L" szövegmezők kiemelve és a "tovább" gomb kiválasztásával.](./media/segment-tutorial/segment4.PNG)

    a. Másolja az **egyszeres Sign-On URL-címet** , és illessze be a **Válasz URL-cím** mezőbe az **alapszintű SAML-konfiguráció** párbeszédpanelen a Azure Portal.

    b. Másolja a * * * * célközönség URL-címe * * * * értéket, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelének **azonosító URL** mezőjébe.

    c. Kattintson a **tovább** gombra.

    ![Szegmens konfigurációja](./media/segment-tutorial/segment5.PNG)

1. Az **SAML 2,0-végpont URL-címe** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL** -értéket.

1. Nyissa meg a letöltött **tanúsítvány (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

1. Kattintson a **Kapcsolatok konfigurálása** elemre.

### <a name="create-segment-test-user"></a>Szegmens tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a szegmensben. A szegmens támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a szegmensben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a szegmens csempére kattint, automatikusan be kell jelentkeznie ahhoz a szegmenshez, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Szegmensek kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A szegmensek elleni védelem speciális láthatósággal és vezérlőkkel](/cloud-app-security/proxy-intro-aad)