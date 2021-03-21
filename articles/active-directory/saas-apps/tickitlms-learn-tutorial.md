---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a TickitLMS megismerésével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a TickitLMS között.
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
ms.openlocfilehash: b37a5332e7c8f21d4309102b6b111953c1765ea6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tickitlms-learn"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a TickitLMS megismerésével

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TickitLMS-t a Azure Active Directory (Azure AD) használatával. Ha integrálja az TickitLMS-t az Azure AD-val, a következőket teheti:

* A TickitLMS megismeréséhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek, hogy TickitLMS az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A TickitLMS az egyszeri bejelentkezés (SSO) engedélyezett előfizetését ismeri.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* TickitLMS **– az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés támogatása

## <a name="adding-tickitlms-learn-from-the-gallery"></a>TickitLMS-tanulás hozzáadása a katalógusból

A TickitLMS az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az TickitLMS-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **TickitLMS Learn** kifejezést a keresőmezőbe.
1. Válassza az **TickitLMS megismerése** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-tickitlms-learn"></a>Az Azure AD SSO konfigurálása és tesztelése a TickitLMS megismeréséhez

Az Azure AD SSO konfigurálása és tesztelése a TickitLMS segítségével a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a TickitLMS learningben.

Az Azure AD SSO konfigurálásához és teszteléséhez a TickitLMS megismeréséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[TickitLMS konfigurálása – egyszeri](#configure-tickitlms-learn-sso)** bejelentkezés – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazás oldalán.
    1. **[TickitLMS létrehozása – további információ a felhasználóról](#create-tickitlms-learn-test-user)** – ha a TickitLMS B. Simon partnere, ismerkedjen meg a felhasználó Azure ad-képviseletével.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **TickitLMS learning** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https:/learn.tickitlms.com/sso/login`

1. Kattintson a **Mentés** gombra.

1. A TickitLMS learning alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a TickitLMS learning alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name | Forrás attribútum|
    | ---- | --------------- |
    | samlaccount | User. samlaccount |
    | Alkalmazottkód | User. Alkalmazottkód |
    | szerepkör | felhasználó. szerepkör |
    | Részleg | felhasználó. részleg |
    | ReportsTo | User. ReportsTo |

    > [!NOTE]
    > A TickitLMS az alkalmazáshoz hozzárendelt felhasználók szerepköreit várja. Állítsa be ezeket a szerepköröket az Azure AD-ben, hogy a felhasználók hozzá lehessen rendelni a megfelelő szerepköröket. A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)
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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a TickitLMS megismeréséhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **TickitLMS megismerés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-tickitlms-learn-sso"></a>A TickitLMS konfigurálása – egyszeri bejelentkezés

Ha az egyszeri bejelentkezést szeretné konfigurálni a **TickitLMS, olvassa** el az **alkalmazás-összevonási metaadatok URL-címét** , hogy TickitLMS a [támogatási csapatot](mailto:support@tickitlms.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-tickitlms-learn-test-user"></a>TickitLMS létrehozása – felhasználói tesztek

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a TickitLMS Learn-ben. Működjön együtt a [TickitLMS-támogatással foglalkozó csapattal](mailto:support@tickitlms.com) , hogy hozzáadja a felhasználókat a TickitLMS Learn platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ekkor a rendszer átirányítja a TickitLMS megtanulni az URL-címen, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg közvetlenül a TickitLMS a bejelentkezési URL-címet, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a TickitLMS, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások TickitLMS Learn csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat kezdeményezéséhez, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a TickitLMS, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A TickitLMS konfigurálásával megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
