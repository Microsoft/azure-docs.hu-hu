---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az ADP Globalview (elavult) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ADP Globalview (elavult) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589310"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az ADP Globalview (elavult)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az ADP Globalview (elavult) a Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja az ADP Globalview (elavult), a következőket teheti:

* Az Azure AD azon vezérlője, aki hozzáfér az ADP Globalview (elavult).
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az ADP Globalview (elavult) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ADP Globalview (elavult) egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az ADP Globalview (elavult) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>ADP-Globalview (elavult) hozzáadása a gyűjteményből

Az ADP-Globalview (elavult) Azure AD-be való integrálásának konfigurálásához a katalógusból ADP-Globalview (elavult) kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **ADP Globalview (elavult)** kifejezést.
1. Válassza az **ADP Globalview (elavult)** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>Azure AD SSO konfigurálása és tesztelése az ADP Globalview (elavult)

Konfigurálja és tesztelje az Azure AD SSO-t az ADP Globalview (elavult) egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az ADP Globalview (elavult).

Az Azure AD SSO és az ADP Globalview (elavult) konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[ADP Globalview (elavult) egyszeri bejelentkezés konfigurálása](#configure-adp-globalview-deprecated-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre ADP Globalview (elavult) tesztelési felhasználót](#create-adp-globalview-deprecated-test-user)** – a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli (elavult) Globalview.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **ADP Globalview (elavult)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    
    Az **azonosító** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Azonosító |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > Ez az érték nem valós. Frissítse az értéket a tényleges azonosítóval. Az érték beszerzéséhez forduljon az [ügyfél-támogatási csapathoz az ADP Globalview (elavult)](https://www.adp.com/contact-us/overview.aspx) ügyfélszolgálatnál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **set up ADP Globalview (elavult)** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az ADP-Globalview (elavult) való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **ADP Globalview (elavult)** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-adp-globalview-deprecated-sso"></a>Az ADP Globalview (elavult) SSO konfigurálása

Az egyszeri bejelentkezés **ADP-Globalview (elavult)** való konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket Azure Portalról [ADP Globalview (elavult) támogatási csoportba](https://www.adp.com/contact-us/overview.aspx). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-adp-globalview-deprecated-test-user"></a>ADP-Globalview létrehozása (elavult) tesztelési felhasználó

Ebben a szakaszban létrehoz egy B. Simon nevű felhasználót az ADP Globalview (elavult). Az [ADP-Globalview (elavult) támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) az ADP Globalview (elavult) platformon veheti fel a felhasználókat. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra az ADP-Globalview (elavult), amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Ha a saját alkalmazások ADP Globalview (elavult) csempére kattint, automatikusan be kell jelentkeznie az ADP Globalview (elavult), amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.


## <a name="next-steps"></a>Következő lépések

Az ADP-Globalview (elavult) konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).