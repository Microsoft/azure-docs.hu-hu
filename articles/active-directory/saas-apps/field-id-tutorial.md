---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a mező azonosítójával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a mező azonosítója között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a mező azonosítójával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) mező-azonosítót. Ha az Azure AD-val integrálja a mezőnevet, a következőket teheti:

* A mező-azonosítóhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Field iD-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A mező-azonosító egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A mező azonosítója támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* A mezőérték konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezete bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Mező azonosítójának hozzáadása a gyűjteményből

A mezőérték Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy mező-azonosítót a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Az új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **mező azonosítóját** .
1. Válassza ki a **mező azonosítóját** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a mező-azonosítóhoz

Konfigurálja és tesztelje az Azure AD SSO-t a mező-azonosítóval egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy csatolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a mező-azonosítóban.

Az Azure AD SSO és a mező azonosítójának konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja az egyszeri](#configure-field-id-sso) bejelentkezés beállításait az alkalmazás oldalán az egyszeri bejelentkezési beállítások konfigurálásához.
    1. [Hozzon létre egy mező-azonosító tesztelési felhasználót](#create-a-field-id-test-user) , hogy a "B. Simon" jogosultsággal rendelkezzen a mező azonosítójában, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **mező azonosítója** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt. Ezután válassza **az egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel egy Sign-On SAML-oldallal való beállításáról, a ceruza ikon kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

   a. Az **azonosító** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<tenantname>.fieldid.com/fieldid`

   b. A **Válasz URL-cím** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez lépjen kapcsolatba a [mező azonosítója támogatási csapatával](mailto:support@ecompliance.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás ikont az **alkalmazás-összevonási metaadatok URL-címének** másolásához. Mentse a számítógépére.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról, a másolás ikon kiemelve](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **név** mezőbe írja be a következőt: `B.Simon` .  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension (például) nevet `B.Simon@contoso.com` .
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a mező-azonosítóhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **mező azonosítója** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Képernyőfelvétel – felhasználó hozzáadása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a **felhasználók** listából, majd a képernyő alján válassza a **kiválasztás** lehetőséget.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-field-id-sso"></a>A mező azonosítójának konfigurálása SSO

Ha be szeretné állítani az egyszeri bejelentkezést a mező-azonosító oldalon, küldje el az **alkalmazás-összevonási metaadatok URL-címét** a [mező-azonosító támogatási csapatának](mailto:support@ecompliance.com). Gondoskodnak arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-a-field-id-test-user"></a>Mező-azonosító tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a mező-azonosítóban. Működjön együtt a Field [ID támogatási csapatával](mailto:support@ecompliance.com) , és vegye fel a felhasználókat a Field ID platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a mező azonosítója csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a mező-azonosítóra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Field iD kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A mező-azonosító védetté tétele speciális láthatósággal és vezérlőkkel](/cloud-app-security/proxy-intro-aad)