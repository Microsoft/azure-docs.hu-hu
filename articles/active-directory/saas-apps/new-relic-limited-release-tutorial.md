---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a New Relic | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és New Relic.
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
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517606"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a New Relic

Ez az oktatóanyag bemutatja, hogyan integrálhatja a New Relic az Azure Active Directory (Azure AD) használatával. Amikor integrálja New Relic Azure AD-val, a következő funkciókat használhatja:

* Szabályozhatja az Azure AD-ban, hogy ki férhet hozzá New Relic.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve New Relic Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Egy New Relic egyszeri bejelentkezésre (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* New Relic támogatja a szolgáltató vagy az identitásszolgáltató által kezdeményezett SSO-t.

## <a name="add-new-relic-from-the-gallery"></a>Alkalmazás New Relic a katalógusból

A virtuális New Relic Azure AD-be való integrálásának konfigurálásához hozzá kell adni New Relic -t **(szervezet szerint)** a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. Válassza ki **a Azure Active Directory** szolgáltatást.
1. Válassza a **Vállalati alkalmazások** Új  >  **alkalmazás lehetőséget.**
1. Az **Azure AD-katalógus tallózása lapon** írja be a **New Relic (szervezet szerint)** gombra a keresőmezőbe.
1. Válassza **New Relic (Szervezet szerint)** lehetőséget az eredmények közül, majd válassza a **Létrehozás lehetőséget.** Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Azure AD SSO konfigurálása és tesztelése New Relic

Konfigurálja és tesztelje az Azure AD SSO-t New Relic egy **B.Simon** nevű tesztfelhasználóval. Ahhoz, hogy az SSO működjön, összekapcsolt kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között a New Relic.

Az Azure AD SSO konfigurálása és tesztelése a New Relic:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használják ezt a funkciót.
   1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének tesztelésére b.Simon használatával.
   1. [Rendelje hozzá az Azure AD-tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy engedélyezze a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. [Konfigurálja New Relic SSO-t,](#configure-new-relic-sso) hogy az egyszeri bejelentkezési beállításokat a New Relic konfigurálja.
   1. [Hozzon New Relic tesztfelhasználót,](#create-a-new-relic-test-user) hogy a B.Simon megfelelője New Relic az Azure AD-felhasználóhoz legyen kapcsolva.
1. [Az SSO tesztelése](#test-sso) annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal alkalmazásintegráció **New Relic lapon** keresse meg a **Kezelés szakaszt.** Ezután válassza **az Egyszeri bejelentkezés lehetőséget.**

1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**

1. A **Set up Single Sign-On with SAML** (Egyetlen alkalmazás beállítása SAML-sel) lapon válassza az SamL-alapkonfiguráció ceruzaikonját a beállítások szerkesztéséhez. 

   ![Képernyőkép az Egyszeres alkalmazás Sign-On SAML-sel, kiemelt ceruza ikonnal.](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg az **Azonosító** és a Válasz **URL-cím értékét.**

   * Ezeket az értékeket a Saját New Relic **alkalmazással lehet** lekérni. Az alkalmazás használata:
      1. [Jelentkezzen be a](https://login.newrelic.com/) New Relic.
      1. A felső menüben válassza az Alkalmazások **lehetőséget.**
      1. A Saját **alkalmazások szakaszban** válassza a Saját **szervezet hitelesítési**  >  **tartománya lehetőséget.**
      1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t csatlakoztatni szeretné (ha több hitelesítési tartománya van). A legtöbb vállalat csak egy Alapértelmezett nevű hitelesítési tartománnyal **van.** Ha csak egy hitelesítési tartomány van, semmit sem kell kiválasztania.
      1. A Hitelesítés **szakaszban** a **Helyességi** feltétel fogyasztói URL-címe tartalmazza a Válasz **URL-címhez használni kívánt értéket.**
      1. A Hitelesítés **szakaszban** az **Entitásazonosító** az azonosítóhoz használni kívánt **értéket tartalmazza.**

1. A Felhasználói **attribútumok & jogcímek** szakaszban  győződjön meg arról, hogy az Egyedi felhasználói azonosító egy olyan mezőre van leképezve, amely a jogcímek alatt használt e-mail-címet New Relic.

   * A **user.userprincipalname** alapértelmezett mező akkor fog működni, ha az értéke megegyezik a New Relic e-mail-címekkel.
   * A  **user.mail mező** jobban működik, ha a **user.userprincipalname** nem a New Relic e-mail-címe.

1. Az **SAML aláíró tanúsítvány szakaszban** másolja ki az Alkalmazás-összevonási metaadatok **URL-címét,** és mentse az értékét későbbi használatra.

1. A **Fiók beállítása szervezet New Relic szakaszban** másolja ki a Bejelentkezési **URL-címet,** és mentse az értékét későbbi használatra.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a New Relic.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza **a** New Relic.
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-new-relic-sso"></a>Az New Relic SSO konfigurálása

Kövesse az alábbi lépéseket az SSO konfigurálás New Relic.

1. [Jelentkezzen be a](https://login.newrelic.com/) New Relic.

1. A felső menüben válassza az Alkalmazások **lehetőséget.**

1. A Saját **alkalmazások szakaszban** válassza a Saját **szervezet hitelesítési**  >  **tartománya lehetőséget.**

1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t csatlakoztatni szeretné (ha több hitelesítési tartománya van). A legtöbb vállalat csak egy Alapértelmezett nevű hitelesítési tartománnyal **van.** Ha csak egy hitelesítési tartomány van, semmit sem kell kiválasztania.

1. A Hitelesítés **szakaszban** válassza a **Konfigurálás lehetőséget.**

   1. Az **SAML-metaadatok** forrása mezőben adja meg az Azure AD-alkalmazás **összevonási** metaadatainak URL-címében korábban mentett értéket.

   1. Az **SSO-cél URL-címe** mezőben adja meg az Azure AD bejelentkezési **URL-cím** mezőjében korábban mentett értéket.

   1. Miután ellenőrizte, hogy a beállítások jól néznek-e ki az Azure AD és New Relic oldalán, válassza a **Mentés lehetőséget.** Ha mindkét oldal nincs megfelelően konfigurálva, a felhasználók nem fognak tudni bejelentkezni a New Relic.

### <a name="create-a-new-relic-test-user"></a>Tesztfelhasználó New Relic létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a New Relic.

1. [Jelentkezzen be a](https://login.newrelic.com/) New Relic.

1. A felső menüben válassza az Alkalmazások **lehetőséget.**

1. Az Alkalmazások **szakaszban válassza** a **Felhasználókezelés lehetőséget.**

1. Válassza a **Felhasználó hozzáadása** elemet.

   1. A **Név alatt** adja meg a **B.Simon nevet.**
   
   1. Az **E-mail** mezőben adja meg az Azure AD SSO által küldött értéket.
   
   1. Válasszon egy **felhasználótípust** és egy **felhasználói** csoportot a felhasználó számára. A tesztfelhasználóknál ésszerű választás  **a** Típus alapszintű felhasználó és a Felhasználó csoport számára.
   
   1. A felhasználó mentéshez válassza a **Felhasználó hozzáadása lehetőséget.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja New Relic bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a New Relic URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal, és automatikusan be kell majd jelentkezve arra a New Relic amelybe beállította az SSO-t. 

A Microsoft Saját alkalmazások is használhatja az alkalmazás tesztelésére bármilyen módban. Ha az Saját alkalmazások-ban a New Relic csempére kattint, és sp módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra az New Relic-be, amelyhez az SSO-t beállította. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A beállítások New Relic kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
