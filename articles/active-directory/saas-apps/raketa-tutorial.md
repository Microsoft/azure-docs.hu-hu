---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Raketa | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Raketa között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511154"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Raketa

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Raketa a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Raketa-t az Azure AD-vel, a következőket teheti:

* A Raketa-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Raketa az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Raketa egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Raketa támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.
* A Raketa konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Raketa hozzáadása a gyűjteményből

A Raketa Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Raketa a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Keresse meg a **vállalati alkalmazások** [2] elemet, majd válassza a **minden alkalmazás** [3] lehetőséget.

1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** [4] elemet. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. A **Hozzáadás a** katalógusból [5] szakaszban írja be a **Raketa** kifejezést a keresőmezőbe: [6].

1. Válassza a **Raketa** elemet az eredmények panel [7] területen, majd kattintson a **Hozzáadás** gombra [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Raketa

Konfigurálja és tesztelje az Azure AD SSO-t a Raketa a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Raketa-ben.

Az Azure AD SSO és a Raketa konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[RAKETA SSO konfigurálása](#configure-raketa-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Raketa-teszt felhasználót](#create-raketa-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Raketa rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Raketa** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** [9] lehetőséget.

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon [9] Válassza az **SAML** [10] lehetőséget.

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztése/toll ikonjára [11] a beállítások szerkesztéséhez.

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    1. Az **azonosítóban (Entity ID)** [12] és a **bejelentkezési URL-cím** [14] szövegmezőben írja be a következő URL-címet: `https://raketa.travel/` .

    1. A **Válasz URL-címe** szövegmezőben [13] írjon be egy URL-címet a következő minta használatával: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` .  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a Raketa ügyfélszolgálati [csapatához](mailto:help@raketa.travel) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd válassza a **Letöltés** [15] lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

1. A **Raketa beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    1. Bejelentkezési URL [16] – az engedélyezési weblap URL-címe, amely a felhasználók hitelesítési rendszerre való átirányítására szolgál.

    1. Azure AD-azonosító [17] – Azure AD-azonosító.

    1. Kijelentkezési URL [18] – a weblap URL-címe, amely a felhasználók a kijelentkezés után való átirányításához használatos.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** [1], majd a **felhasználók** [19] lehetőséget, majd válassza **a minden felhasználó** [20] elemet.

1. Válassza a képernyő tetején található [21] **új felhasználót** .

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:

   1. A Felhasználónév mezőben adja meg a (z) [22] **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.

   1. A név mezőbe írja be a (z) [23] **nevet** `B.Simon` .

   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet [25], majd írja le a [24] **jelszó** mezőben megjelenő értéket.

   1. Kattintson a **Létrehozás** [26] elemre. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Raketa.

1. A Azure Portal válassza a **vállalati alkalmazások** [2] elemet, majd válassza a **minden alkalmazás** [3] lehetőséget.

1. Az alkalmazások listában válassza a **Raketa** [27] elemet.  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** [28] lehetőséget. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Válassza a **felhasználó hozzáadása** [29] lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** [30] lehetőséget.

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** [31] elemet a felhasználók listából, majd kattintson a **Select** [32] gombra a képernyő alján.

1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Raketa SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **Raketa** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [Raketa támogatási csapatához](mailto:help@raketa.travel). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-raketa-test-user"></a>Raketa-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Raketa-ben. Együttműködik a [Raketa támogatási csapatával](mailto:help@raketa.travel) , hogy hozzáadja a felhasználókat a Raketa platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Raketa csempére kattint, automatikusan be kell jelentkeznie arra a Raketa, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Raketa kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Raketa és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)