---
title: 'Oktatóanyag: Azure Active Directory integráció a CPQSync-mel a Cincom használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a CPQSync között a Cincom használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 4bd98acec06caf9c807844df2c957e61e0f0b2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455120"
---
# <a name="tutorial-integrate-cpqsync-by-cincom-with-azure-active-directory"></a>Oktatóanyag: a CPQSync integrálása a Cincom és a Azure Active Directory használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CPQSync a Cincom segítségével a Azure Active Directory (Azure AD) használatával. Ha a Cincom és az Azure AD segítségével integrálja a CPQSync-et, a következőket teheti:

* Vezérlés az Azure AD-ben, aki a Cincom által CPQSync fér hozzá.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a CPQSync az Azure AD-fiókjával való Cincom.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* CPQSync az Cincom egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cincom által támogatott CPQSync az **SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja

## <a name="adding-cpqsync-by-cincom-from-the-gallery"></a>CPQSync hozzáadása a Cincom-ből a katalógusból

Ahhoz, hogy az Azure AD-be Cincom az CPQSync-integrációt, hozzá kell adnia a CPQSync a Cincom a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CPQSync by Cincom** kifejezést a keresőmezőbe.
1. Válassza ki a **CPQSync az Cincom** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cpqsync-by-cincom"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Cincom-CPQSync

Konfigurálja és tesztelje az Azure AD SSO-t a CPQSync-mel a **B. Simon** nevű Cincom használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CPQSync által a Cincom-ben.

Az Azure AD SSO konfigurálásához és a Cincom-mel történő CPQSync való teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[CPQSync konfigurálása a CINCOM SSO használatával](#configure-cpqsync-by-cincom-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre CPQSync a Cincom test User használatával](#create-cpqsync-by-cincom-test-user)** – ha a CPQSync által használt "B. Simon" jogosultsággal rendelkezik a felhasználó Azure ad-képviseletéhez kapcsolódó Cincom.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **CPQSync a Cincom** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://cincom.oktapreview.com/sso/saml2/<CUSTOMURL>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://cincom.okta.com/sso/saml2/<CUSTOMDOMAIN>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://cincom.okta.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot a [CPQSync az Cincom ügyfélszolgálati csapatával](https://cpqsupport.cincomcpq.com) az értékek lekéréséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **CPQSync által Cincom beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a CPQSync számára a Cincom.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **CPQSync a Cincom alapján** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cpqsync-by-cincom-sso"></a>CPQSync konfigurálása Cincom SSO-val

Ha az Cincom oldalon egyszeri bejelentkezést szeretne beállítani a **CPQSync** , el kell küldenie a letöltött **tanúsítványt (RAW)** és a megfelelő másolt url-címeket a Azure Portalról a [Cincom támogatási csapatának CPQSync](https://cpqsupport.cincomcpq.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-cpqsync-by-cincom-test-user"></a>CPQSync létrehozása a Cincom test User használatával

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a CPQSync által a Cincom. Együttműködik a [CPQSync által a Cincom támogatási csapatával](https://cpqsupport.cincomcpq.com) , hogy hozzáadja a felhasználókat a CPQSync a Cincom platform használatával. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a CPQSync by Cincom csempe elemre kattint, automatikusan be kell jelentkeznie a CPQSync olyan Cincom, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)