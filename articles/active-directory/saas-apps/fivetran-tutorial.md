---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Fivetran | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Fivetran között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 88f6da7e43e269816c7e4ae2fd7bbc3f5e26522b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Fivetran

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Fivetran a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Fivetran-t az Azure AD-vel, a következőket teheti:

* A Fivetran-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Fivetran az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy Fivetran-fiók.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Fivetran támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Fivetran **csak időben támogatja a** felhasználók kiépítési folyamatát

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-fivetran-from-the-gallery"></a>Fivetran hozzáadása a gyűjteményből

A Fivetran Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Fivetran a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Fivetran** kifejezést a keresőmezőbe.
1. Válassza ki a **Fivetran** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Azure AD SSO konfigurálása és tesztelése a Fivetran-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Fivetran a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Fivetran-ben.

Az Azure AD SSO és a Fivetran konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[FIVETRAN SSO konfigurálása](#configure-fivetran-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Fivetran-teszt felhasználót](#create-fivetran-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Fivetran rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Fivetran** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.


1. A Fivetran alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Fivetran alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | -------------- | --------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Fivetran beállítása** szakaszban másolja a **bejelentkezési URL-címet** és az **Azure ad-azonosító** értékeit.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Fivetran.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Fivetran** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nincs szerepkör beállítva ehhez az alkalmazáshoz, akkor a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-fivetran-sso"></a>Fivetran SSO konfigurálása

Ebben a szakaszban az egyszeri bejelentkezést a **Fivetran** oldalon konfigurálhatja.

1. Egy másik böngészőablakban jelentkezzen be a Fivetran-fiókjába a fiók tulajdonosaként.
1. Kattintson a nyílra az ablak bal felső sarkában, majd válassza a **fiók kezelése** lehetőséget a legördülő listából.

   ![Képernyőkép, amely a fiók kezelése menüpontot jeleníti meg.](media/fivetran-tutorial/fivetran-1.png)

1. Nyissa meg a **Beállítások** lap **SAML-konfiguráció** szakaszát.

   ![Képernyőkép, amely megjeleníti az SAML konfigurációs ablaktáblát, és kiemelte a konfigurációs beállításokat.](media/fivetran-tutorial/fivetran-2.png)

   1. Az **SAML-hitelesítés engedélyezéséhez** válassza **a be** lehetőséget.
   1. A **bejelentkezési URL-cím** mezőben illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.
   1. A **kiállítóban** illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
   1. Nyissa meg a letöltött tanúsítványfájl egy szövegszerkesztőben, másolja a tanúsítványt a vágólapra, majd illessze be a **nyilvános tanúsítvány** szövegmezőbe.
   1. Válassza a **konfiguráció mentése** lehetőséget.

### <a name="create-fivetran-test-user"></a>Fivetran-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Fivetran-ben. A Fivetran támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Fivetran-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a Fivetran, amelyhez be szeretné állítani az egyszeri bejelentkezést 

2. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a Fivetran csempére kattint, automatikusan be kell jelentkeznie arra a Fivetran, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A Fivetran konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).