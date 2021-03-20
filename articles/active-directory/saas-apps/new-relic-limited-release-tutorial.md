---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az új ereklye között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504049"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye szolgáltatással

Ez az oktatóanyag azt ismerteti, hogyan integrálható az új ereklye Azure Active Directory (Azure AD) használatával. Az új ereklye Azure AD-val való integrálásával a következőket teheti:

* Az Azure AD-ben való hozzáférés az új ereklye eléréséhez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az új Ereklyebe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Új ereklye-előfizetés, amely engedélyezve van az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az új ereklye támogatja a szolgáltató vagy az identitás-szolgáltató által kezdeményezett egyszeri bejelentkezést.
* Az új ereklye konfigurálása után kényszerítheti a munkamenet-vezérlést, amely védelmet nyújt a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgása ellen. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Új ereklye hozzáadása a katalógusból

Az új ereklye Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az **új ereklye (szervezet szerint)** elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. Válassza ki a **Azure Active Directory** szolgáltatást.
1. Válassza a **vállalati alkalmazások**  >  **új alkalmazás** lehetőséget.
1. Az **Azure ad** -katalógus tallózása lapon írja be a keresőmezőbe az **új ereklye (szervezet szerint)** kifejezést.
1. Válassza ki az **új ereklye (szervezet szerint)** lehetőséget az eredmények közül, majd válassza a **Létrehozás** lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Az Azure AD SSO konfigurálása és tesztelése az új ereklye számára

Konfigurálja és tesztelje az Azure AD SSO-t új ereklye használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az új Ereklyeben.

Az Azure AD SSO konfigurálása és tesztelése új ereklye használatával:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
   1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
   1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja az új EREKLYE SSO](#configure-new-relic-sso) -t az egyszeri bejelentkezési beállítások konfigurálásához az új ereklye oldalon.
   1. [Hozzon létre egy új ereklye tesztelési felhasználót](#create-a-new-relic-test-user) , hogy az Azure ad-felhasználóhoz társított új ereklye alapján a B. Simon-nek legyen egy párja.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **új ereklye a szervezeti** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt. Ezután válassza **az egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeres Sign-On beállítása SAML-vel, a ceruza ikon kiemelve.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg az **azonosító** és a **Válasz URL-cím** értékeit.

   * Ezeket az értékeket az új ereklye **saját szervezet** alkalmazás használatával kérheti le. Az alkalmazás használata:
      1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.
      1. A felső menüben válassza az **alkalmazások** lehetőséget.
      1. A **saját alkalmazások** szakaszban válassza a **saját szervezet**  >  **hitelesítési tartományok** elemet.
      1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t szeretné kapcsolni (ha több hitelesítési tartománnyal rendelkezik). A legtöbb vállalatnak csak egy **alapértelmezett** nevű hitelesítési tartománya van. Ha csak egy hitelesítési tartomány van, akkor semmit nem kell kiválasztania.
      1. A **hitelesítés** szakaszban az **állítás fogyasztói URL-cím** tartalmazza a **Válasz URL-címéhez** használandó értéket.
      1. A **hitelesítés** szakaszban a **saját entitás azonosítója** tartalmazza az **azonosítóhoz** használandó értéket.

1. A **felhasználói attribútumok & jogcímek** szakaszban győződjön meg arról, hogy az **egyedi felhasználói azonosító** egy olyan mezőre van leképezve, amely az új ereklye által használt e-mail-címet tartalmazza.

   * Az alapértelmezett mező **User. userPrincipalName** fog működni, ha az értékei megegyeznek az új ereklye e-mail-címeivel.
   * Ha a **User. userPrincipalName** nem az új ereklye e-mail-címe, akkor a mező **felhasználója** is jobban dolgozhat.

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **alkalmazás-összevonási metaadatok URL-címét** , és mentse az értékét későbbi használatra.

1. Az **új ereklye beállítása szervezet szerint** szakaszban másolja a **bejelentkezési URL-címet** , és mentse az értékét későbbi használatra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebből a témakörből megtudhatja, hogyan hozhat létre tesztelési felhasználót a B. Simon nevű Azure Portalban.

1. A Azure Portal válassza a **Azure Active Directory** lehetőséget.
1. Válassza a **felhasználók**  >  **új felhasználó** lehetőséget.
1. Az **új felhasználó** lapon:
   1. A Felhasználónév mezőben adja meg a **nevet** `username@companydomain.extension` . Például: `b.simon@contoso.com`. Ennek egyeznie kell az új ereklye oldalon használt e-mail-címmel.
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd mentse a megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

A következőképpen engedélyezheti a B. Simon számára az Azure AD egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az új ereklye számára a szervezeti alkalmazásban.

1. A Azure Portal válassza a **Azure Active Directory** lehetőséget.
1. Válassza a **vállalati alkalmazások**  >  **új ereklye szervezet szerint** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![Képernyőkép a kezelésről szakasz, amely kiemelte a felhasználókat és a csoportokat.](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** területen válassza a **felhasználók és csoportok** (vagy a **felhasználók**) lehetőséget a csomag szintjétől függően.

   ![Képernyőfelvétel a felhasználó hozzáadása lehetőségről.](common/add-assign-user.png)

1. A **felhasználók és csoportok** (vagy **felhasználók**) területen válassza ki a **felhasználók** listából a **B. Simon** elemet, majd a képernyő alján válassza a **kiválasztás** lehetőséget.
1. A **hozzárendelés hozzáadása** lapon válassza a **hozzárendelés** lehetőséget.

## <a name="configure-new-relic-sso"></a>Új ereklye SSO konfigurálása

A következő lépésekkel konfigurálhatja az egyszeri bejelentkezést az új ereklye szolgáltatásban.

1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.

1. A felső menüben válassza az **alkalmazások** lehetőséget.

1. A **saját alkalmazások** szakaszban válassza a **saját szervezet**  >  **hitelesítési tartományok** elemet.

1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t szeretné kapcsolni (ha több hitelesítési tartománnyal rendelkezik). A legtöbb vállalatnak csak egy **alapértelmezett** nevű hitelesítési tartománya van. Ha csak egy hitelesítési tartomány van, akkor semmit nem kell kiválasztania.

1. A **hitelesítés** szakaszban válassza a **Konfigurálás** lehetőséget.

   1. Az **SAML-metaadatok forrásaként** adja meg az Azure ad- **alkalmazás-összevonási metaadatok URL-címe** mezőben korábban mentett értéket.

   1. Az **SSO cél URL-címe** mezőben adja meg az Azure ad **bejelentkezési URL-címéből** korábban mentett értéket.

   1. Miután meggyőződött arról, hogy a beállítások az Azure AD-ben és az új ereklye oldalon is jól megjelentek, válassza a **Mentés** lehetőséget. Ha a két oldal nem megfelelően van konfigurálva, a felhasználók nem fognak tudni bejelentkezni az új ereklye szolgáltatásba.

### <a name="create-a-new-relic-test-user"></a>Új ereklye teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az új Ereklyeben.

1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.

1. A felső menüben válassza az **alkalmazások** lehetőséget.

1. Az **alkalmazások** szakaszban válassza a **felhasználói kezelés** lehetőséget.

1. Válassza a **Felhasználó hozzáadása** elemet.

   1. A **név** mezőbe írja be a következőt: **B. Simon**.
   
   1. Az **e-mailek** mezőben adja meg az Azure ad SSO által küldendő értéket.
   
   1. Válasszon egy felhasználói **típust** és egy felhasználói **csoportot** a felhasználó számára. Tesztelési felhasználó esetén az **alapszintű felhasználó** a csoport típusa és **felhasználója** számára ésszerű választás.
   
   1. A felhasználó mentéséhez válassza a **felhasználó hozzáadása** elemet.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel segítségével tesztelheti.

Ha a hozzáférési panelen kiválasztja az **új ereklye a szervezet alapján** lehetőséget, automatikusan be kell jelentkeznie az új ereklyebe. További információ a hozzáférési panelről: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Új ereklye kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)
