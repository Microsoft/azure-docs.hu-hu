---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a insuite szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és insuite között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.openlocfilehash: a75af62043d6aaf5f8397878fe2b6ac61d35ae60
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459963"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-insuite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az insuite szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az insuite-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az insuite-t, a következőket teheti:

* Az insuite szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával való insuite-be automatikusan bejelentkezzenek.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* insuite egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a insuite támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A insuite konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-insuite-from-the-gallery"></a>Insuite hozzáadása a gyűjteményből

A insuite Azure AD-integrációjának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a insuite-t.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **insuite** kifejezést a keresőmezőbe.
1. Válassza a **insuite** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-insuite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az insuite-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a insuite használatával egy **B. Simon**nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és az insuite-beli kapcsolódó felhasználó között.

Az Azure AD SSO és az insuite használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Insuite SSO konfigurálása](#configure-insuite-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy insuite-teszt felhasználót](#create-insuite-test-user)** – hogy a "B. Simon" egy olyan, a felhasználó Azure ad-beli képviseletéhez kapcsolódó "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **insuite** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta egyikének használatával:

    ```http
    https://<CUSTOMER_NAME>.m.diol.jp/cgi-bin/saml_sso.cgi
    https://<CUSTOMER_NAME>.dacl.jp/cgi-bin/saml_sso.cgi
    ```

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `DreamArts_insuite_TENANTNAME`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta egyikének használatával:

    ```http
    https://<CUSTOMER_NAME>.m.diol.jp/cgi-bin/saml_sso.cgi
    https://<CUSTOMER_NAME>.dacl.jp/cgi-bin/saml_sso.cgi
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek megszerzéséhez lépjen kapcsolatba a [insuite ügyfél-támogatási csapatával](mailto:e-support@dreamarts.co.jp) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A saját **csomag beállítása** szakaszban másolja a megfelelő URL-címeket (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az insuite elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **insuite**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-insuite-sso"></a>Insuite SSO konfigurálása

Az egyszeri bejelentkezés **insuite** oldalon való konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portalról a [insuite támogatási csapatba](mailto:e-support@dreamarts.co.jp). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-insuite-test-user"></a>Insuite tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó lett létrehozva az insuite-ban. a insuite támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a insuite-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a insuite csempére kattint, automatikusan be kell jelentkeznie arra a insuite-csomagba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A insuite kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A insuite védelem speciális láthatósággal és vezérlőkkel](/cloud-app-security/proxy-intro-aad)