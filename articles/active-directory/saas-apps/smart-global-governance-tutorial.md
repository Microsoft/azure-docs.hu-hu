---
title: 'Oktatóanyag: Azure AD SSO-integráció intelligens globális irányítással'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az intelligens globális irányítás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997069"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az intelligens globális irányítással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) intelligens globális irányítását. Az intelligens globális irányítás Azure AD-vel való integrálásával a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá az intelligens globális irányításhoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az intelligens globális irányításba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Intelligens globális irányítási előfizetés egyszeri bejelentkezéssel (SSO) engedélyezve.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

Az intelligens globális irányítás támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

Az intelligens globális irányítás konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Intelligens globális irányítás hozzáadása a katalógusból

Az intelligens globális irányítás Azure AD-be való integrálásának konfigurálásához intelligens globális irányítást kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg az **intelligens globális irányítás** kifejezést a keresőmezőbe.
1. Válassza az **intelligens globális irányítás** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Az Azure AD SSO konfigurálása és tesztelése az intelligens globális irányításhoz

Az Azure AD SSO konfigurálását és tesztelését intelligens globális irányítással végezheti el egy B. Simon nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó között az intelligens globális irányításban.

Az Azure AD SSO intelligens globális irányítással való konfigurálásához és teszteléséhez hajtsa végre a következő magas szintű lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t annak engedélyezéséhez, hogy a felhasználók használják a szolgáltatást.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** , hogy lehetővé váljon a felhasználó számára az Azure ad egyszeri bejelentkezés használata.
1. **[Konfigurálja az intelligens globális irányítási egyszeri bejelentkezést](#configure-smart-global-governance-sso)** az alkalmazás oldalán.
    1. **[Hozzon létre egy intelligens globális irányítási tesztet használó felhasználót](#create-a-smart-global-governance-test-user)** a felhasználó Azure ad-beli képviseletének megfelelő felhasználóként.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/)az **intelligens globális irányítási** alkalmazás-integráció lapon, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Az alapszintű SAML-konfiguráció ceruza gombja](common/edit-urls.png)

1. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni az alkalmazást identitásszolgáltató módban, hajtsa végre az alábbi lépéseket.

    a. Az **azonosító** mezőben adja meg az alábbi URL-címek egyikét:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. A **Válasz URL-címe** mezőbe írja be az alábbi URL-címek egyikét:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és végezze el a következő lépést.

   - A **bejelentkezési URL-cím** mezőbe írja be az alábbi URL-címek egyikét:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a tanúsítvány **(RAW)** **letöltési** hivatkozását a tanúsítvány letöltéséhez, és mentse azt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **intelligens globális irányítás beállítása** szakaszban másolja a megfelelő URL-címet vagy URL-címeket a követelmények alapján:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget. Válassza a **felhasználók** lehetőséget, majd válassza **a minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet \<username> @ \<companydomain> . \<extension> Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése** lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="grant-access-to-the-test-user"></a>Hozzáférés biztosítása a tesztelési felhasználónak

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezést használja azáltal, hogy hozzáférést biztosít a felhasználónak az intelligens globális irányításhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Válassza az alkalmazások lista **intelligens globális irányítás** elemét.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok** lehetőséget:

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget:

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-smart-global-governance-sso"></a>Intelligens globális irányítású egyszeri bejelentkezés konfigurálása

Az intelligens globális irányítási oldalon való egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött nyers tanúsítványt és a Azure Portalról az [intelligens globális irányítási támogatási csapatba](mailto:support.tech@smartglobal.com)másolt megfelelő URL-címeket. Az SAML SSO-kapcsolatokat úgy konfigurálja, hogy mindkét oldalon megfelelő legyen.

### <a name="create-a-smart-global-governance-test-user"></a>Intelligens globális irányítási teszt felhasználó létrehozása

Működjön együtt az [intelligens globális irányítási támogatási csapattal](mailto:support.tech@smartglobal.com) , és adjon hozzá egy B. Simon nevű felhasználót az intelligens globális irányításban. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja az intelligens globális irányítás csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az intelligens globális irányítási példányba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelbe](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Útmutatók az SaaS-alkalmazások Azure Active Directory-nal való integrálásához ](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)

- [Intelligens globális irányítás kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [Intelligens globális irányítás biztosítása fejlett láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)