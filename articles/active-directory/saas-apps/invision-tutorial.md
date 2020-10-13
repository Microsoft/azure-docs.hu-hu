---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a InVision | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és InVision között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.openlocfilehash: 2e8f542f8a734f5bd6592ebc743b30676dd61f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850547"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a InVision

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a InVision a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az InVision-t az Azure AD-vel, a következőket teheti:

* A InVision-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a InVision az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* InVision egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A InVision támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A InVision konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>InVision hozzáadása a gyűjteményből

A InVision Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a InVision a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **InVision** kifejezést a keresőmezőbe.
1. Válassza ki a **InVision** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a InVision

Konfigurálja és tesztelje az Azure AD SSO-t a InVision a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a InVision-ben.

Az Azure AD SSO és a InVision konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[INVISION SSO konfigurálása](#configure-invision-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre InVision-teszt felhasználót](#create-invision-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-InVision rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **InVision** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.invisionapp.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a InVision ügyfélszolgálati [csapatához](mailto:support@invisionapp.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **InVision beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a InVision.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **InVision**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-invision-sso"></a>InVision SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a InVision webhelyre.

1. Kattintson a **csapat** elemre, és válassza a **Beállítások**lehetőséget.

    ![Képernyőfelvétel: a csapat lap a kiválasztott beállításokkal.](./media/invision-tutorial/config1.png)

1. Görgessen le az **egyszeri bejelentkezéshez** , majd kattintson a **módosítás**gombra.

    ![A képernyőképen az egyszeri bejelentkezés módosítása gomb látható.](./media/invision-tutorial/config3.png)

1. Az **egyszeri bejelentkezés** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyszeri bejelentkezési oldal, amelyen megadhatja az ebben a lépésben szereplő értékeket.](./media/invision-tutorial/config4.png)

    a. Módosítsa az **egyszeri bejelentkezést a < fióknév minden tagja számára, >** a **be**értékre.

    b. A **név** szövegmezőbe írja be a nevet (például `azureadsso` ).

    c. Adja meg a bejelentkezési URL-címet a **bejelentkezési URL** szövegmezőben.

    d. A kijelentkezési **URL** szövegmezőbe illessze be a **kijelentkezési** URL-címet, amelyet a Azure Portal másolt.

    e. Az **SAML-tanúsítvány** szövegmezőben nyissa meg a letöltött **tanúsítványt (Base64)** a Jegyzettömbben, másolja a tartalmat, és illessze be az SAML-tanúsítvány szövegmezőbe.

    f. A **név azonosító formátuma** szövegmezőben használja a `Unspecified` **nevet azonosító formátumhoz**.

    : Válassza az **SHA-256** elemet a **kivonatoló algoritmus**legördülő menüjéből.

    h. Adja meg a megfelelő nevet az **egyszeri bejelentkezés gombjának címkéjéhez**.

    i. Az igény szerinti **kiépítés engedélyezése** .

    j. Kattintson a **Frissítés** parancsra.

### <a name="create-invision-test-user"></a>InVision-tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a InVision webhelyre.

1. Kattintson a **csapatra** , és válassza a **személyek**lehetőséget.

    ![Képernyőfelvétel: a csapat lap, amelyen a személyek vannak kiválasztva.](./media/invision-tutorial/config2.png)

1. Új felhasználó hozzáadásához kattintson a **+ ikonra** .

    ![Képernyőfelvétel: a + ikont jeleníti meg a felhasználó hozzáadásához.](./media/invision-tutorial/user1.png)

1. Adja meg a felhasználó e-mail-címét, és kattintson a **tovább**gombra.

    ![A képernyőképen a meghívása párbeszédpanel jelenik meg, ahol megadhatja a címeket.](./media/invision-tutorial/user2.png)

1. Az e-mail-cím ellenőrzése után kattintson a **meghívás**elemre.

    ![A képernyőképen megjelenik a meghívás párbeszédpanel, ahol a meghívás lehetőségre kattintva folytathatja a folytatást.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a InVision csempére kattint, automatikusan be kell jelentkeznie arra a InVision, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A InVision kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A InVision és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)