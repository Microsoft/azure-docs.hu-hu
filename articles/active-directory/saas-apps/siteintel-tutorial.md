---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SiteIntel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SiteIntel között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92509998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SiteIntel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SiteIntel a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SiteIntel-t az Azure AD-vel, a következőket teheti:

* A SiteIntel-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a SiteIntel az Azure AD-fiókjával.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SiteIntel egyszeri bejelentkezés (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SiteIntel támogatja az SP által kezdeményezett és a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.
* A SiteIntel konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>SiteIntel hozzáadása a gyűjteményből

A SiteIntel Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SiteIntel a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A Hozzáadás a katalógusból mezőbe írja be a **következőt** : **SiteIntel**.
1. Az eredmények listájában válassza a **SiteIntel** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a SiteIntel

Konfigurálja és tesztelje az Azure AD SSO-t a SiteIntel egy *B. Simon* nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SiteIntel-ben.

Az Azure AD SSO és a SiteIntel konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.  

    a. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon felhasználóval.  

    b. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy engedélyezze a B. Simon felhasználó számára az Azure ad egyszeri bejelentkezés használatát.

1. **[Konfigurálja a SITEINTEL SSO](#configure-siteintel-sso)** -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.

    * **[Hozzon létre egy SiteIntel-tesztelési felhasználót](#create-a-siteintel-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon felhasználóhoz tartozó SiteIntel rendelkezik.

1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, tegye a következőket:

1. A [Azure Portal](https://portal.azure.com/) **SiteIntel** alkalmazás-integráció lapján lépjen a **kezelés** szakaszra, majd válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **alapszintű SAML-konfiguráció** mellett válassza a **Szerkesztés** (toll ikon) lehetőséget.

   ![Képernyőkép: "egyszeri bejelentkezés beállítása SAML használatával" panel](common/edit-urls.png)

1. Az alkalmazás identitásszolgáltató módban történő konfigurálásához az **alapszintű SAML-konfiguráció** szakaszban tegye a következőket:

    a. Az **azonosító** mezőbe írja be az URL-címet a következő formátumban: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. A **Válasz URL-címe** mezőbe írja be az URL-címet a következő formátumban: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. A **továbbítási állapot** mezőbe írja be az URL-címet a következő formátumban: `https://<CLIENT>.siteintel.com`

1. Az alkalmazás SP-kezdeményezésű módban való konfigurálásához válassza a **további URL-címek beállítása** lehetőséget, majd tegye a következőket:

   * A **bejelentkezési URL-cím** mezőbe írja be az URL-címet a következő formátumban: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse őket a tényleges azonosítóval, a válasz URL-címével, a bejelentkezési URL-címmel és a továbbítási állapottal. Az értékek beszerzéséhez forduljon a SiteIntel ügyfélszolgálati [csapatához](mailto:support@intalytics.com). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** gombot, és másolja az URL-címet az **alkalmazás-összevonási metaadatok URL-címe** mezőbe.

    ![Az "alkalmazás-összevonási metaadatok URL-címe" másolási gomb képernyőképe](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** elemet a panel tetején.
1. A **felhasználó** tulajdonságainál tegye a következőket:

   a. A név mezőbe írja be a **B. Simon** **nevet** .  

   b. **A Felhasználónév mezőbe írja** be a felhasználónevet a következő formátumban: `username@companydomain.extension` (például `B.Simon@contoso.com` ).

   c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

   d. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti a B. Simon felhasználó számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a SiteIntel.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az **alkalmazások** listában válassza a **SiteIntel** lehetőséget.
1. Az alkalmazás – Áttekintés lap **kezelés** területén válassza a **felhasználók és csoportok** lehetőséget.

   ![Képernyőkép a "felhasználók és csoportok" hivatkozásról](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználó hozzáadása" gomb képernyőképe](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **B. Simon** lehetőséget, majd a képernyő alsó részén kattintson a **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

## <a name="configure-siteintel-sso"></a>SiteIntel SSO konfigurálása

Ha az egyszeri bejelentkezést az SiteIntel oldalon szeretné konfigurálni, küldje el az **alkalmazás-összevonási metaadatok URL-címéből** másolt URL-címet a [SiteIntel-támogatási csapatnak](mailto:support@intalytics.com). Ezek az értékek úgy vannak beállítva, hogy az SAML SSO-kapcsolatot mindkét oldalon megfelelően hozza létre.

### <a name="create-a-siteintel-test-user"></a>SiteIntel-teszt felhasználó létrehozása

Ebben a szakaszban egy *Britta Simon* nevű felhasználót hoz létre a SiteIntel-ben. Együttműködik a [SiteIntel támogatási csapatával](mailto:support@intalytics.com) , hogy hozzáadja a felhasználókat a SiteIntel platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja a **SiteIntel** csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a SiteIntel, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálását ismertető oktatóanyagok listája ](./tutorial-list.md)
- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
- [A SiteIntel kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)
- [A SiteIntel és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)