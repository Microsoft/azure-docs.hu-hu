---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Firmex VDR | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Firmex VDR között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453384"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Firmex-VDR

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Firmex-VDR a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Firmex VDR, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Firmex VDR.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Firmex VDR az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Firmex VDR egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Firmex VDR támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* A Firmex konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Firmex-VDR hozzáadása a gyűjteményből

A Firmex-VDR Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Firmex-VDR a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Firmex VDR** kifejezést a keresőmezőbe.
1. Válassza ki a **FIRMEX VDR** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Firmex VDR

Konfigurálja és tesztelje az Azure AD SSO-t a Firmex VDR egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Firmex VDR.

Az Azure AD SSO Firmex-VDR való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Firmex-VDR SSO konfigurálása](#configure-firmex-vdr-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Firmex-VDR-teszt felhasználó létrehozása](#create-firmex-vdr-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon FIRMEX-VDR rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Firmex VDR** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://login.firmex.com`

1. Kattintson a **Mentés** gombra.

1. A Firmex VDR alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Firmex VDR alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ------------ | --------- |
    | e-mail | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **set-up FIRMEX VDR** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Firmex VDR.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **FIRMEX VDR** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-firmex-vdr-sso"></a>Firmex VDR SSO konfigurálása

### <a name="before-you-get-started"></a>A kezdés előtt

#### <a name="what-youll-need"></a>Mire lesz szüksége

-   Aktív Firmex-előfizetés
-   Azure AD-beli egyszeri bejelentkezéses szolgáltatás
-   A rendszergazda konfigurálja az SSO-t
-   Ha engedélyezve van az egyszeri bejelentkezés, a vállalat összes felhasználójának be kell jelentkeznie a Firmex az SSO használatával, és nem kell bejelentkezési vagy jelszót használnia.

#### <a name="how-long-will-this-take"></a>Mennyi időt vesz igénybe?

Az SSO implementálása néhány percet vesz igénybe. A Firmex támogatása gyakorlatilag nem áll fenn az egyszeri bejelentkezés engedélyezésével a webhelyén, a vállalat felhasználói pedig SSO használatával hitelesítik magukat. Csak kövesse az alábbi lépéseket.

### <a name="step-1---identify-your-companys-domains"></a>1. lépés – a vállalat tartományának azonosítása

Azonosítsa azokat a tartományokat, amelyekkel a vállalat felhasználói bejelentkeznek.

Például:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>2. lépés – Kapcsolatfelvétel a Firmex-támogatással a tartományokkal

E-mail- [Firmex támogató csapat](mailto:support@firmex.com) vagy 1888 688 4042 x. 11 hívása, hogy beszéljen a Firmex támogatásáról. Adja át a tartomány adatait. A Firmex-támogatás a tartományokat a VDR adja hozzá az **igényelt tartományokhoz**. A rendszergazdának most már konfigurálnia kell az SSO-t.

Figyelmeztetés: amíg a hely rendszergazdája nem konfigurálja az igényelt tartományokat, a vállalat felhasználói nem tudnak majd bejelentkezni a VDR. A nem vállalati felhasználók (azaz a vendég felhasználók) továbbra is bejelentkezhetnek az e-mail-/jelszavuk használatával. A konfiguráció eltarthat néhány percig.

### <a name="step-3---configure-the-claimed-domains"></a>3. lépés – az igényelt tartományok konfigurálása

1. Jelentkezzen be a Firmex webhely-rendszergazdaként.
1. A bal felső sarokban kattintson a céges emblémára.
1. Válassza az  **egyszeri bejelentkezés**  lapot. Ezután válassza az  **SSO-konfiguráció** lehetőséget. Kattintson a konfigurálni kívánt tartományra.

    ![Igényelt tartományok](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Adja meg a rendszergazdának a következő mezőket. A mezőket az identitás-szolgáltatótól kell venni:  

    ![SSO-konfiguráció](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    b. Az **Identity Provider URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. **Nyilvános kulcsú tanúsítvány**  – hitelesítési célból a kibocsátó digitálisan aláírja az SAML-üzenetet. Az üzenet aláírásának ellenőrzéséhez az üzenet fogadója egy ismert nyilvános kulcsot használ, amely a kibocsátóhoz tartozik. Hasonlóképpen, egy üzenet titkosításához a végső fogadóhoz tartozó nyilvános titkosítási kulcsnak ismertnek kell lennie a kibocsátó számára. Mindkét esetben – aláírás és titkosítás – a megbízható nyilvános kulcsokat előre meg kell osztani.  Ez az **x509** az **összevonási metaadatok XML-fájlja**

    d. Az egyszeri bejelentkezés konfigurációjának befejezéséhez kattintson a **Mentés** gombra. A módosítások azonnal érvénybe lépnek.

1. Jelenleg az SSO engedélyezve van a webhelyen.

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Firmex-ben. Együttműködik a [Firmex támogatási csapatával](mailto:support@firmex.com) , hogy hozzáadja a felhasználókat a Firmex platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Firmex VDR csempére kattint, automatikusan be kell jelentkeznie arra a Firmex-VDR, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Firmex VDR az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Firmex és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)