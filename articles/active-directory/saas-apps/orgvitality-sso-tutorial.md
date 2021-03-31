---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OrgVitality SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és OrgVitality SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: b774f50f6f136d9e7c71b5fe6fbffb725a9240a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-orgvitality-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OrgVitality SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OrgVitality SSO-t Azure Active Directory (Azure AD-val). Ha a OrgVitality SSO-t az Azure AD-vel integrálja, a következőket teheti:

* Az Azure AD-ben a OrgVitality SSO-hoz hozzáférő vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy OrgVitality egyszeri bejelentkezést az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* OrgVitality SSO egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A OrgVitality SSO támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t

## <a name="adding-orgvitality-sso-from-the-gallery"></a>OrgVitality SSO hozzáadása a katalógusból

A OrgVitality SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az OrgVitality SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **OrgVitality SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **ORGVITALITY SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-orgvitality-sso"></a>Azure AD SSO konfigurálása és tesztelése a OrgVitality SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a OrgVitality SSO használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a OrgVitality SSO-ban.

Az Azure AD SSO OrgVitality SSO-vel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ORGVITALITY SSO-SSO konfigurálása](#configure-orgvitality-sso-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[ORGVITALITY SSO-teszt felhasználó létrehozása](#create-orgvitality-sso-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli OrgVitality szeretne létrehozni.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **ORGVITALITY SSO** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://rpt.orgvitality.com/<COMPANY_NAME>/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://rpt.orgvitality.com/<COMPANY_NAME>Auth/default.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek megszerzéséhez forduljon a [ORGVITALITY SSO-támogatási csapatához](https://orgvitality.com/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A OrgVitality SSO-alkalmazása egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A OrgVitality SSO-alkalmazás azt várja, hogy a **NameIdentifier** a **User. Alkalmazottkód** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/default-attributes.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **ORGVITALITY SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a OrgVitality SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **OrgVitality egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-orgvitality-sso-sso"></a>OrgVitality SSO SSO konfigurálása

Az egyszeri bejelentkezés **ORGVITALITY egyszeri** bejelentkezéses oldalon való konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-CÍMEKET a Azure Portal [OrgVitality SSO-támogatás csapatának](https://orgvitality.com/contact-us/). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-orgvitality-sso-test-user"></a>OrgVitality SSO-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a OrgVitality SSO-ban. A [ORGVITALITY SSO-támogatással foglalkozó csapatával](https://orgvitality.com/contact-us/) felveheti a felhasználókat a OrgVitality SSO-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

1. Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a OrgVitality SSO-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést

1. Használhatja a Microsoft Access panelt. Ha a hozzáférési panelen a OrgVitality SSO csempére kattint, automatikusan be kell jelentkeznie arra a OrgVitality SSO-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Következő lépések

A OrgVitality SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).