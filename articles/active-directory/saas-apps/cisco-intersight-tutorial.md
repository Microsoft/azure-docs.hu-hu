---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a Cisco Intersight | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Cisco Intersight között.
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
ms.openlocfilehash: 6efec61ec5866547ab6b432cb9c566642f3191ff
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-intersight"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a Cisco Intersighttal

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Cisco Intersightot az Azure Active Directory (Azure AD) használatával. Ha integrálja a Cisco Intersightot az Azure AD-val, a következő funkciókat használhatja:

* Az Azure AD vezérlése, aki hozzáféréssel rendelkezik a Cisco Intersighthoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be a Cisco Intersightba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Cisco Intersight egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A Cisco Intersight támogatja **az SP által** kezdeményezett SSO-t.

> [!NOTE]
> Az alkalmazás azonosítója rögzített sztringérték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-cisco-intersight-from-the-gallery"></a>Cisco Intersight hozzáadása a katalógusból

A Cisco Intersight Azure AD-be való integrálásának konfigurálához hozzá kell adni a Cisco Intersightot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a **Cisco Intersight kifejezéseket** a keresőmezőbe.
1. Válassza a **Cisco Intersight lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-cisco-intersight"></a>Az Azure AD SSO konfigurálása és tesztelése Cisco Intersighthoz

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco Intersighttal egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a Cisco Intersight kapcsolódó felhasználója között.

Ha az Azure AD SSO-t a Cisco Intersighttal konfigurálja és teszteli, hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használják ezt a funkciót.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[A Cisco Intersight SSO konfigurálása](#configure-cisco-intersight-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazásoldalon.
    1. **[Cisco Intersight-tesztfelhasználó](#create-cisco-intersight-test-user)** létrehozása – a B.Simon megfelelője a Cisco Intersightban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **Cisco Intersight** alkalmazásintegrációs oldalán keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. A Set **up single sign-on with SAML** (Egyszeri bejelentkezés beállítása SAML-sel) oldalon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

1. Az **SAML-alapkonfiguráció szakaszban** adja meg a következő mezők értékeit:

    a. A Bejelentkezési **URL-cím** szövegmezőbe írja be az URL-címet: `https://intersight.com`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írja be az URL-címet: `www.intersight.com`

1. A Cisco Intersight-alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőkép erre mutat példát. Az Egyedi felhasználói **azonosító** alapértelmezett értéke **user.userprincipalname,** de a Cisco Intersight azt várja, hogy ezt a felhasználó e-mail-címével kell leképezni. Erre használhatja a **listából a user.mail** attribútumot, vagy használhatja a megfelelő attribútumértéket a szervezet konfigurációja alapján.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Cisco Intersight-alkalmazás arra számít, hogy néhány további attribútumot is vissza kell adni az SAML-válasznak, amelyek alább láthatók. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.
    
    | Name |  Forrásattribútum|
    | -------------- | --------- |
    | First_Name | user.givenname |
    | Last_Name | user.surname |
    | memberOf (tag) | user.groups |

1. Az Egyszeri bejelentkezés beállítása **SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse  meg az Összevonási metaadatok XML-fájlját, majd válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez. 

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

1. A **Cisco Intersight** beállítása szakaszban másolja ki a követelménynek megfelelő URL-címet/URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza a Azure Portal **lehetőséget,** Azure Active Directory **felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja fel a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát a Cisco Intersighthoz való hozzáférés engedélyezésével.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **Cisco Intersight lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza** a Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja azt **a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör lesz kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-cisco-intersight-sso"></a>A Cisco Intersight SSO konfigurálása

Az egyszeri bejelentkezés **Cisco Intersight-oldalon** való konfigurálásához el kell küldenie a letöltött **összevonási** metaadatok XML-fájlját és a megfelelő másolt URL-címeket a Azure Portal Cisco [Intersight támogatási csapatának.](mailto:intersight-feedback@cisco.com) Ezt a beállítást úgy állítják be, hogy az SAML SSO-kapcsolat megfelelően legyen beállítva mindkét oldalon.

### <a name="create-cisco-intersight-test-user"></a>Cisco Intersight-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Cisco Intersightban. A [Cisco Intersight támogatási csapatával](mailto:intersight-feedback@cisco.com) együtt vegye fel a felhasználókat a Cisco Intersight platformra. Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hoznunk és aktiválnunk kell.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a Cisco Intersight bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Cisco Intersight bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Ha a böngészőben a Cisco Intersight csempére Saját alkalmazások, a rendszer átirányítja a Cisco Intersight bejelentkezési URL-címére. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.


## <a name="next-steps"></a>Következő lépések

A Cisco Intersight konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


