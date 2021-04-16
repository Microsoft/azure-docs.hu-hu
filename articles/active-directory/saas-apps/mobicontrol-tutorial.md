---
title: 'Oktatóanyag: Azure Active Directory MobiControl-| Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a MobiControl között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 87cbbd0caf735afaaadfa47bebf13feeaefcc832
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520071"
---
# <a name="tutorial-azure-active-directory-integration-with-mobicontrol"></a>Oktatóanyag: Azure Active Directory MobiControl-integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja a MobiControlt a Azure Active Directory (Azure AD) használatával. Ha integrálja a MobiControlt az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáfér a MobiControlhoz.
* Engedélyezze a felhasználók számára, hogy automatikusan bejelentkezve legyen a MobiControlba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* MobiControl egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A MobiControl támogatja **az SP által** kezdeményezett SSO-t.

## <a name="add-mobicontrol-from-the-gallery"></a>MobiControl hozzáadása a katalógusból

Ahhoz, hogy a MobiControl integrálható az Azure AD-be, hozzá kell adni a MobiControlt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe a **MobiControl** parancsot.
1. Válassza **a MobiControl lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-mobicontrol"></a>Az Azure AD SSO konfigurálása és tesztelése a MobiControlhoz

Konfigurálja és tesztelje az Azure AD SSO-t a MobiControl használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a MobiControl kapcsolódó felhasználója között.

Ha az Azure AD SSO-t a MobiControl segítségével konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Konfigurálja a MobiControl SSO-t](#configure-mobicontrol-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálához.
    1. **[Hozzon létre egy MobiControl-tesztfelhasználót,](#create-mobicontrol-test-user)** hogy a MobiControlban a B.Simon megfelelője legyen, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **MobiControl** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<SUBDOMAIN>.corp.soti.net/mobicontrol`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<SUBDOMAIN>.mobicontrolcloud.com/mobicontrol`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket [az értékeket a MobiControl-ügyfél](https://www.soti.net/about/contact-us/) támogatási csapatától kapják meg. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

5. Az **Alkalmazás-összevonási** metaadatok URL-címének másolása és Sign-On az SAML-tanúsítványsal lap **SAML** aláíró tanúsítvány szakaszában kattintson a Másolás gombra az Alkalmazás-összevonási metaadatok **URL-címének** másolása és mentése a számítógépre.

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

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát úgy, hogy hozzáférést biztosít a MobiControlhoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **MobiControl lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-mobicontrol-sso"></a>A MobiControl SSO konfigurálása

Ha az egyszeri bejelentkezést a **MobiControl** oldalán konfigurálja, el kell küldenie az alkalmazás összevonási metaadatainak **URL-címét** a [MobiControl támogatási csapatának.](https://www.soti.net/about/contact-us/) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-mobicontrol-test-user"></a>MobiControl tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a MobiControlban. A [MobiControl támogatási csapatával](https://www.soti.net/about/contact-us/) együtt vegye fel a felhasználókat a MobiControl platformra. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a MobiControl bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a MobiControl bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a rendszer a mobiControl csempére kattint a Saját alkalmazások, a rendszer átirányítja a MobiControl bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A MobiControl konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)