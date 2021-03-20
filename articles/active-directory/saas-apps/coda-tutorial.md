---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Coda szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Coda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: c78744538991e337b226e49a053807ef8174d507
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455731"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Coda-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Codat Azure Active Directory (Azure AD-val). A Coda és az Azure AD integrálásával a következőket teheti:

* A Codahoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Codaba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Coda egyszeri bejelentkezés (SSO) engedélyezett előfizetése (Enterprise) GDrive-integrációval letiltva. Ha jelenleg engedélyezve van, lépjen kapcsolatba a [CODA támogatási csoportjával](mailto:support@coda.io) , és tiltsa le a GDrive-integrációt a szervezet számára.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Coda támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A Coda a felhasználók üzembe helyezésének **időpontját is** támogatja

* A Coda konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>CODA hozzáadása a gyűjteményből

A Coda Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Coda-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CODA** kifejezést a keresőmezőbe.
1. Válassza ki a **CODA** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Coda számára

Konfigurálja és tesztelje az Azure AD SSO-t a Coda használatával a **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a Coda-beli kapcsolódó felhasználó között.

Az Azure AD SSO és a Coda konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[A Coda SSO konfigurációjának megkezdése](#begin-configuration-of-coda-sso)** – az egyszeri bejelentkezés konfigurációjának megkezdése a Coda-ban.
1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
   * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
   * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[CODA SSO konfigurálása](#configure-coda-sso)** – a Coda egyszeri bejelentkezési beállításainak konfigurálásának befejezéséhez.
   * **[Hozzon létre CODA-teszt felhasználót](#create-coda-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli, a Coda-beli ügyféllel.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="begin-configuration-of-coda-sso"></a>A Coda SSO konfigurációjának megkezdése

A kezdéshez kövesse az alábbi lépéseket a Coda-ben.

1. A Coda-ban nyissa meg a **szervezeti beállítások** panelt.

   ![Szervezeti beállítások megnyitása](media/coda-tutorial/org-settings.png)

1. Győződjön meg arról, hogy a szervezet GDrive-integrációja ki van kapcsolva. Ha jelenleg engedélyezve van, lépjen kapcsolatba a [CODA támogatási csapatával](mailto:support@coda.io) , és segítsen a GDrive kikapcsolásában.

   ![GDrive letiltva](media/coda-tutorial/gdrive-off.png)

1. A **hitelesítés egyszeri bejelentkezéssel (SAML)** területen válassza az **SAML konfigurálása** lehetőséget.

   ![SAML-beállítások](media/coda-tutorial/saml-settings-link.png)

1. Jegyezze fel az **entitás-azonosító** és az **SAML-válasz URL-címének** értékeit, amelyeket a következő lépésekben kell megadnia.

   ![Az Azure-ban használandó entitás-azonosító és SAML-válasz URL-címe](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **CODA** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

   a. Az **azonosító** szövegmezőbe írja be a fenti "Entity id" értéket. A következő mintát kell követnie: `https://coda.io/samlId/<CUSTOMID>`

   b. A **Válasz URL-címe** szövegmezőbe írja be a fenti "SAML-válasz URL-címe" kifejezést. A következő mintát kell követnie: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Az értékek eltérnek a fentitől; az értékeket a Coda "SAML konfigurálása" konzolján találja. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **CODA beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Coda elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **CODA** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

   ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-coda-sso"></a>CODA SSO konfigurálása

A telepítés befejezéséhez Azure Active Directory értékeket kell megadnia a Coda **configure SAML** -paneljén.

1. A Coda-ban nyissa meg a **szervezeti beállítások** panelt.
1. A **hitelesítés egyszeri bejelentkezéssel (SAML)** területen válassza az **SAML konfigurálása** lehetőséget.
1. **SAML-szolgáltató** beállítása **Azure Active Directoryra**.
1. Az **Identity Provider bejelentkezési URL-címében** illessze be a **bejelentkezési URL-címet** az Azure-konzolból.
1. Az **Identity Provider-kiállítóban** illessze be az **Azure ad-azonosítót** az Azure-konzolból.
1. Az **Identitáskezelő nyilvános tanúsítványa** lapon jelölje be a **tanúsítvány feltöltése** lehetőséget, majd válassza ki a korábban letöltött tanúsítványfájl-fájlt.
1. Kattintson a **Mentés** gombra.

Ezzel befejezte az SAML SSO-kapcsolatok beállításához szükséges munkát.

### <a name="create-coda-test-user"></a>CODA-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Codaban. A Coda támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Coda-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Coda csempére kattint, automatikusan be kell jelentkeznie a Coda-be, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Coda kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Coda védetté tétele a speciális láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)