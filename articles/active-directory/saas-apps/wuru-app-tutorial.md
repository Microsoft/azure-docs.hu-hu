---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Wúru App | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory wúru alkalmazás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 8b4cb988329f599e098b0e392938ae050b55577d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520911"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wru-app"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Wúru-alkalmazással

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Wúru Appot Azure Active Directory (Azure AD) alkalmazással. Amikor integrálja a Wúru-alkalmazást az Azure AD-val, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáfér a Wúru-alkalmazáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Wúru-alkalmazásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Wúru App single sign-on (SSO) enabled subscription.Wúru App single sign-on (SSO) enabled subscription.Wúru App single sign-on (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A Wúru App támogatja **az SP által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-wru-app-from-the-gallery"></a>Wúru-alkalmazás hozzáadása a katalógusból

A Wúru-alkalmazás Azure AD-be való integrálásának konfigurálásához fel kell vennie a Wúru-alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe a **Wúru App (Wúru-alkalmazás)** parancsot.
1. Válassza **a Wúru App** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-wru-app"></a>Azure AD SSO konfigurálása és tesztelése Wúru-alkalmazáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Wúru App használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Wúru App kapcsolódó felhasználója között.

Az Azure AD SSO Wúru-alkalmazással való konfiguráláshoz és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A W automatikus alkalmazás-egyszeri bejelentkezés (SSO)](#configure-wuru-app-sso)** konfigurálása – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazásoldalon.
    1. **[Hozzon létre](#create-wuru-app-test-user)** egy W adatalkalmazás-tesztfelhasználót, hogy a Wúru-alkalmazásban a B.Simon megfelelője legyen, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **A Wúru App** alkalmazásintegráció lapján keresse meg a **Kezelés** szakaszt, és válassza az egyszeri **bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://app.wuru.site/api/auth/azure`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írja be az értéket: `urn:amazon:cognito:sp:us-east-2_142Y3PTBg`

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse  meg az Összevonási metaadatok XML-fájlját, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez. 

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

1. A **Wúru-alkalmazás beállítása szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Wúru-alkalmazáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Wúru App lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-wuru-app-sso"></a>A W áruházbeli alkalmazás SSO-nak konfigurálása

Ha egyszeri bejelentkezést konfigurál a **Wúru** alkalmazás oldalán, el kell küldenie a letöltött összevonási metaadatok XML-ét és a megfelelő másolt **URL-címeket** a Azure Portal-ből a [Wúru-alkalmazás támogatási csapatának.](mailto:contacto@wuru.site) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-wuru-app-test-user"></a>W adatalkalmazás-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Wúru-alkalmazásban. A [Wúru alkalmazástámogatási csapatával](mailto:contacto@wuru.site) való munka során vegye fel a felhasználókat a Wúru App platformra. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Wúru-alkalmazás bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Wúru alkalmazás bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor a Wúru App csempére kattint a Saját alkalmazások, a rendszer átirányítja a Wúru-alkalmazás bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

A Wúru App konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


