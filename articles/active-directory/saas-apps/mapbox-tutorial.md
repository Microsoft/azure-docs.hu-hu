---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Mapbox | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Mapbox között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e74018d4b145f8dec44137ca1a3b0713b1b5cadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728591"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Mapbox

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mapbox a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Mapbox-t az Azure AD-vel, a következőket teheti:

* A Mapbox-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Mapbox az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Mapbox egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Mapbox támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-mapbox-from-the-gallery"></a>Mapbox hozzáadása a gyűjteményből

A Mapbox Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mapbox a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Mapbox** kifejezést a keresőmezőbe.
1. Válassza ki a **Mapbox** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Azure AD SSO konfigurálása és tesztelése a Mapbox-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Mapbox a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Mapbox-ben.

Az Azure AD SSO és a Mapbox konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[MAPBOX SSO konfigurálása](#configure-mapbox-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Mapbox-teszt felhasználót](#create-mapbox-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Mapbox rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Mapbox** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. A Mapbox alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Mapbox alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name   |  Forrás attribútum|
    | -----|--------- |
    | szerepkör | User. assignedroles |
    | | |

    > [!NOTE]
    > A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **Mapbox beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Mapbox.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Mapbox** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a fentiekben ismertetett módon állította be a szerepköröket, kiválaszthatja a **szerepkör kiválasztása** legördülő listából.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-mapbox-sso"></a>Mapbox SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mapbox rendszergazdaként.

1. Kattintson a **Beállítások** fülre.

    ![Mapbox-beállítások lap](./media/mapbox-tutorial/configure1.png)

1. Kattintson a bal oldali navigációs ablaktábla **Biztonság** fülére.

    ![Mapbox Biztonság lap](./media/mapbox-tutorial/configure2.png)

1. Kattintson az **egyszeri bejelentkezés szerkesztése** elemre.

    ![Mapbox egyszeri bejelentkezés szerkesztése](./media/mapbox-tutorial/configure3.png)

1. Görgessen le a **3. lépés: SAML egyszeri bejelentkezés beállítása a Mapbox** , és hajtsa végre a következő lépéseket:

    ![Mapbox-konfiguráció](./media/mapbox-tutorial/configure4.png)

    1. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    1. A **kiállító azonosítója** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    1. Nyissa meg a letöltött **tanúsítvány (RAW)** fájlt a Azure Portal a Jegyzettömbben, és másolja a tanúsítványfájl tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    1. Kattintson az **egyszeri bejelentkezés beállításainak mentése** lehetőségre.

### <a name="create-mapbox-test-user"></a>Mapbox-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Mapbox-ben. Együttműködik a [Mapbox támogatási csapatával](mailto:help@mapbox.com) , hogy hozzáadja a felhasználókat a Mapbox platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Mapbox, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Mapbox csempére kattint, automatikusan be kell jelentkeznie arra a Mapbox, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Mapbox konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).
