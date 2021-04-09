---
title: 'Oktatóanyag: Azure Active Directory integráció a 123FormBuilder SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és 123FormBuilder SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319179"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a 123FormBuilder SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a 123FormBuilder SSO-t Azure Active Directory (Azure AD-val). Ha a 123FormBuilder SSO-t az Azure AD-vel integrálja, a következőket teheti:

* Az Azure AD-ben a 123FormBuilder SSO-hoz hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a 123FormBuilder SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* 123FormBuilder SSO egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a 123FormBuilder SSO támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* a 123FormBuilder SSO **csak időben támogatja a** felhasználók üzembe helyezését.
* A 123FormBuilder SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>123FormBuilder SSO hozzáadása a katalógusból

A 123FormBuilder SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az 123FormBuilder SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **123FormBuilder SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **123FORMBUILDER SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a 123FormBuilder SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a 123FormBuilder SSO használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a 123FormBuilder SSO-ban.

Az Azure AD SSO 123FormBuilder SSO-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[123FORMBUILDER SSO konfigurálása](#configure-123formbuilder-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[123FORMBUILDER SSO-teszt felhasználó létrehozása](#create-123formbuilder-sso-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli 123FormBuilder szeretne létrehozni.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **123FormBuilder SSO** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket frissítenie kell a tényleges URL-címekből és azonosítóból, amely az oktatóanyag későbbi részében is szerepel.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **123FORMBUILDER SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a 123FormBuilder SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **123FormBuilder egyszeri bejelentkezés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-123formbuilder-sso"></a>123FormBuilder SSO konfigurálása

1. Az egyszeri bejelentkezés **123FormBuilder egyszeri bejelentkezéses** oldalon való konfigurálásához lépjen a [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) következő lépésekre, és végezze el az alábbi lépéseket:

    ![Az SSO SAML-Identity szolgáltató konfigurációs képernyőjét bemutató képernyőkép.](./media/123formbuilder-tutorial/submit.png) 

    a. Az **e-mail** szövegmezőbe írja be a felhasználó e-mail-címét, például: `B.Simon@Contoso.com` .

    b. Kattintson a **feltöltés** elemre, és keresse meg a letöltött metaadatok XML-fájlját, amelyet a Azure Portal letöltött.

    c. Kattintson az **űrlap elküldése** elemre.

2. Az **Microsoft Azure ad – egyszeri bejelentkezés – az Alkalmazásbeállítások konfigurálása** a következő lépéseket hajtja végre:

    ![Egyetlen Sign-On konfigurálása](./media/123formbuilder-tutorial/url3.png)

    a. Ha az alkalmazást **identitásszolgáltató kezdeményezett módban** szeretné konfigurálni, másolja ki a példány **azonosító** értékét, és illessze be az **azonosító** szövegmezőbe az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.

    b. Ha az alkalmazást **identitásszolgáltató kezdeményezett módban** szeretné konfigurálni, másolja ki a példány **Válasz URL-címét** , és illessze be a **Válasz URL-címe** SZÖVEGMEZŐbe az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.

    c. Ha az alkalmazást SP-ben **kezdeményezett módban** szeretné konfigurálni, másolja a **bejelentkezési URL-címet** a példányhoz, és illessze be a **bejelentkezési URL-cím** SZÖVEGMEZŐbe az **alapszintű SAML-konfiguráció** szakaszának Azure Portal.

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a 123FormBuilder SSO-ban. a 123FormBuilder SSO támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a 123FormBuilder SSO-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a 123FormBuilder SSO csempére kattint, automatikusan be kell jelentkeznie a 123FormBuilder SSO-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a 123FormBuilder SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)