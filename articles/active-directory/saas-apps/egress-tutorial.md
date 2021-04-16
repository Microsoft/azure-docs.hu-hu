---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a bejövő forgalom között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 392697978041cdab50f45e22a73acbadb9122546
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egress"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a bejövő forgalommal

Ez az oktatóanyag bemutatja, hogyan integrálhatja a bejövő forgalom Azure Active Directory (Azure AD) használatával. A bejövő forgalom és az Azure AD integrálásakor a következő funkciókat használhatja:

* Vezérlés az Azure AD-ban, aki hozzáféréssel rendelkezik a forgalomhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a bejövő forgalomba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A bejövő forgalom támogatja **az SP-t és az idP által** kezdeményezett SSO-t.
* A ki- és kiépítés támogatja **az "Időben"** felhasználóátépítést.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="add-egress-from-the-gallery"></a>Bejövő forgalom hozzáadása a katalógusból

A bejövő forgalom Azure AD-be való integrálásának konfigurálásához fel kell vennie a gyűjteményből a bejövő forgalom szolgáltatásokat a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a következőt a keresőmezőbe: **Egress** .
1. Válassza **a Forgalom lehetőséget** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-egress"></a>Az Azure AD SSO konfigurálása és tesztelése a bejövő forgalomhoz

Konfigurálja és tesztelje az Azure AD SSO-t a bejövő forgalommal egy **B.Simon nevű tesztfelhasználó használatával.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó, bejövő forgalomban található felhasználó között.

Az Azure AD SSO AD-t a következő lépésekkel konfigurálhatja és tesztelhatja a bejövő forgalommal:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A bejövő SSO konfigurálása](#configure-egress-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Hozzon létre egy](#create-egress-test-user)** bejövő forgalomteszt-felhasználót , hogy a B.Simon megfelelője legyen a ki- és beúszióban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal integrációs  oldalán keresse meg a  Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció** szakaszban a felhasználónak semmilyen lépést nem kell végrehajtania, mivel az alkalmazás már előre integrálva van az Azure-ral.

1. Kattintson **a További URL-címek beállítása lehetőségre,** és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://switch.egress.com/ui/`

1. Kattintson a **Mentés** gombra.

1. Az Egyszeri bejelentkezés beállítása **SAML-lel** lap **SAML** aláíró tanúsítvány szakaszában kattintson a Másolás gombra az Alkalmazás-összevonási metaadatok **URL-címének** másolása és mentése a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza Azure Portal lehetőséget, **Azure Active Directory** **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát, ha hozzáférést ad a forgalomhoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Bejövő forgalom lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-egress-sso"></a>A bejövő forgalom SSO-nak konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a vállalati webhelyre.

1. Hajtsa végre a következő lépéseket a következő oldalon.

    ![A forgalom konfigurálása](./media/egress-tutorial/configure-1.PNG)

    a. A bal oldali menüben kattintson az **SSO-konfiguráció elemre.**

    b. Válassza **az Egyszeri bejelentkezés használata választógombot** az Egyszeri bejelentkezés használatára.

    c. Adjon meg egy érvényes leírást a **Szolgáltató leírása szövegmezőben.**

    d. A Metadata **URL (Metaadatok URL-címe)** szövegmezőbe illessze be a vágólapra másolt **Alkalmazás-összevonási** metaadatok URL-címe értéket.

    e. Kattintson a **Metaadatok betöltése elemre.**

    f. Kattintson a **Mentés gombra** az SSO-konfiguráció frissítéséhez.

### <a name="create-egress-test-user"></a>A bejövő forgalom tesztelésére használt felhasználó létrehozása

1. Jelentkezzen be a **vállalati Egressziós** céges webhelyre.

1. A bal **oldali menüben** kattintson a Felhasználók meghívása elemre, majd kattintson az **Egyetlen felhasználó meghívása elemre** a felhasználó hozzáadásához.

    ![Képernyőkép a "Felhasználók meghívása" oldalról, az "Egyetlen felhasználó meghívása" gomb kijelölve.](./media/egress-tutorial/create-user-1.PNG)

1. Töltse ki a kötelező mezőket, és kattintson a Meghívás **elemre.**

    ![A ki- és leágazást tesztelő felhasználó létrehozása](./media/egress-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Bejövő forgalom bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a Bejövő forgalom bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie arra a forgalomra, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha a Saját alkalmazások-ban a Bejövő forgalom csempére kattint, és SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra a forgalomra, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A bejövő forgalom konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
