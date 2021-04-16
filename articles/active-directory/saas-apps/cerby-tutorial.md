---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Cerby-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a Cerby között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 53813bd692f1e621c89e234748f2f9a2d343684b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520930"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cerby"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Cerbyvel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Cerbyt a Azure Active Directory (Azure AD) használatával. Ha integrálja a Cerbyt az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a Cerbyhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Cerbybe az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Cerby egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A Cerby támogatja **az SP által** kezdeményezett SSO-t.

* A Cerby támogatja **az időben való felhasználóátépítést.**

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-cerby-from-the-gallery"></a>Cerby hozzáadása a katalógusból

A Cerby Azure AD-be való integrálásának konfigurálához hozzá kell adni a Cerbyt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Cerby kifejezéseket** a keresőmezőbe.
1. Válassza **a Cerby lehetőséget** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-cerby"></a>Az Azure AD SSO konfigurálása és tesztelése a Cerbyhez

Konfigurálja és tesztelje az Azure AD SSO-t a Cerbyvel egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Kapcsolódó felhasználó között a Cerbyben.

Az Azure AD SSO Cerbyvel való konfiguráláshoz és teszteléshez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Cerby SSO konfigurálása](#configure-cerby-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy](#create-cerby-test-user)** Cerby-tesztfelhasználót, hogy a Cerbyben a B.Simon megfelelője legyen, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal a Cerby-alkalmazásintegráció oldalon keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.** 
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `urn:amazon:cognito:sp:<ID>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<CustomerName>-cerbyauth.auth.us-east-2.amazoncognito.com/saml2/idpresponse`

    c. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet az alábbi minták egyikével:

    | Bejelentkezési URL-cím |
    |--------|
    | `https://app.cerby.com` |
    | `https://<CustomerName>.cerby.com` |
    |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címével. Ezekért az értékekért forduljon a [Cerby-ügyfél](mailto:help@cerby.com) támogatási csapatához. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse meg a  **Tanúsítvány (Base64)** et, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A Tanúsítvány letöltése hivatkozás](common/certificatebase64.png)

1. A **Tanúsítvány beállítása szakaszban** másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

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

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát a Cerbyhez való hozzáférés engedélyezésével.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Cerby lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-cerby-sso"></a>A Cerby SSO konfigurálása

Az egyszeri bejelentkezés **Cerby-oldalon** való konfigurálásához el kell küldenie a letöltött tanúsítványt **(Base64)** és a megfelelő másolt URL-címeket a Azure Portal [a Cerby támogatási csapatának.](mailto:help@cerby.com) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-cerby-test-user"></a>Cerby-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a Cerbyben. A Cerby támogatja az alapértelmezés szerint engedélyezett, időben elérhető felhasználókiépítést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a Cerbyben, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre** a Azure Portal. Ez átirányítja a Cerby bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Cerby bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a tanúsítványtárolóban a Cerby csempére Saját alkalmazások, a rendszer átirányítja a Cerby bejelentkezési URL-címére. További információ a [Saját alkalmazások: Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

A Cerby konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


