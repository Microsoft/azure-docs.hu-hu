---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja az SDS-sel és & vegyi | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és az SDS & Vegyi információkezeléshez.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja az SDS-sel és & vegyi & információk kezelésével

Ez az oktatóanyag bemutatja, hogyan integrálhatja az SDS & Vegyi Azure Active Directory (Azure AD) használatával. Az SDS és & Az Azure AD-val való integrálása után a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik az SDS & Vegyi információkezeléshez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az SDS-be & a Chemical Information Managementbe az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Az SDS & Vegyiinformáció-kezelés egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* Az SDS & Chemical Information Management támogatja **az SP által** kezdeményezett SSO-t.

* Az SDS & Chemical Information Management támogatja az időben való felhasználóátépítést. 


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>SDS-& vegyiinformáció-kezelés hozzáadása a katalógusból

Az SDS & Chemical Information Management az Azure AD-be való integrálásának konfigurálához hozzá kell adni az SDS & Chemical Information Managementet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszba** írja be az **SDS & Chemical Information Management (Kémiai** információkezelés) kifejezéseket a keresőmezőbe.
1. Válassza **az SDS & Vegyiinformáció-kezelés lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>Az Azure AD SSO konfigurálása és tesztelése SDS-hez & Vegyi információkezeléshez

Konfigurálja és tesztelje az Azure AD SSO-t az SDS & Chemical Information Management használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SDS-& Vegyiinformáció-kezelésben.

Az Azure AD SSO SSO SDS-& és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[Az SDS & Vegyiinformáció-kezelési SSO](#configure-sds--chemical-information-management-sso)** konfigurálása – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
    1. **[Hozzon létre SDS &](#create-sds--chemical-information-management-test-user)** Vegyiinformáció-kezelési tesztfelhasználót, hogy a B.Simon megfelelője legyen az SDS & Chemical Information Managementben, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **SDS & Vegyiinformáció-kezelés** alkalmazásintegrációs oldalán keresse  meg a Kezelés szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. Az Azonosító **mezőbe** írjon be egy URL-címet a következő mintával: `https://cs.cloudsds.com/saml/<ID>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, válasz URL-lel és Sign-On URL-címével. Ezeket [az értékeket & az SDS-& a Kémiai információkezelési ügyfél](mailto:info@cloudsds.com) támogatási csapatával. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

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

Ebben a szakaszban engedélyezni fogja a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az SDS & Chemical Information Management szolgáltatáshoz.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **SDS & Vegyiinformáció-kezelés lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-sds--chemical-information-management-sso"></a>Az SDS & Vegyiinformáció-kezelési SSO konfigurálása

Ha egyszeri bejelentkezést konfigurál az **SDS &** Vegyiadat-kezelési oldalán, el kell küldenie az alkalmazás összevonási metaadatainak **URL-címét** az [SDS & Chemical Information Management támogatási csapatának.](mailto:info@cloudsds.com) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-sds--chemical-information-management-test-user"></a>SDS-& vegyiinformáció-kezelési tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre az SDS & Information Managementben. Az SDS & Chemical Information Management támogatja az alapértelmezés szerint engedélyezett, időponthoz időben elérhető felhasználóátépítést. Ebben a szakaszban nem található műveletelem. Ha egy felhasználó még nem létezik az SDS-& a Chemical Information Managementben, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre** a Azure Portal. Ez átirányítja az SDS-& a Kémiai információkezelés bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az SDS & a Chemical Information Management bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor az SDS & Vegyi információkezelés csempére kattint a Saját alkalmazások, a rendszer átirányítja az SDS & Chemical Information Management bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SDS & Kémiai információkezelést, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


