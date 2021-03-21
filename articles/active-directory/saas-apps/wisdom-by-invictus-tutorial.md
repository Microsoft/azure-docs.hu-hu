---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a bölcsességgel Invictus használatával | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directory és a bölcsesség közötti egyszeri bejelentkezést Invictus szerint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: jeedes
ms.openlocfilehash: d1ca90d00dad66bf88f767f46d9cd2839fdd57b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519163"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wisdom-by-invictus"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrációja a bölcsességgel Invictus szerint

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a bölcsességet a Invictus és a Azure Active Directory (Azure AD) használatával. Ha a Invictus az Azure AD-vel integrálja a bölcsességet, a következőket teheti:

* A Invictus által a bölcsességhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Bölcsességbe az Azure AD-fiókjával való Invictus.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Invictus egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.
> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Invictus által támogatott bölcsesség az **SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja
* Miután Invictus a bölcsességet, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wisdom-by-invictus-from-the-gallery"></a>Bölcsesség hozzáadása Invictus a katalógusból

A bölcsességnek az Azure AD-be való Invictus való integrálásának konfigurálásához a Invictus a katalógusból a felügyelt SaaS-alkalmazások listájára kell felvennie a bölcsességet.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Invictus bölcsességet** a keresőmezőbe.
1. Válassza a bölcsesség lehetőséget az **Invictus** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-wisdom-by-invictus"></a>Az Azure AD SSO konfigurálása és tesztelése a bölcsességhez Invictus szerint

Konfigurálja és tesztelje az Azure AD SSO-t a bölcsesség alapján Invictus egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Invictus-ben.

Az Azure AD SSO és a Invictus közötti bölcsesség konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[bölcsesség konfigurálása a Invictus SSO használatával](#configure-wisdom-by-invictus-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre bölcsességet a Invictus test User használatával](#create-wisdom-by-invictus-test-user)** – ha a Invictus-nek a felhasználó Azure ad-képviseletéhez kapcsolódó, a "B. Simon" partnere.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/), a **bölcsesség by Invictus** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://invictuselearning-pool7.com/?option=saml_user_login&idp=Microsoft`

1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy a Invictus a bölcsességhez hozzáférést biztosít.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **bölcsesség a Invictus** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-wisdom-by-invictus-sso"></a>Bölcsesség konfigurálása Invictus SSO-val

Ha Invictus oldalon szeretné beállítani az egyszeri bejelentkezést a **bölcsességen** , el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [bölcsességnek a Invictus támogatási csapatának](mailto:support@invictus.in). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-wisdom-by-invictus-test-user"></a>Bölcsesség létrehozása Invictus-tesztelési felhasználó alapján

Ebben a szakaszban létre fog hozni egy Britta Simon in Invictus nevű felhasználót a bölcsességben. A [Invictus támogatási csapatával](mailto:support@invictus.in) a bölcsességet a Invictus platformon veheti fel a bölcsességbe. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a Invictus csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie a Bölcsességbe olyan Invictus, amelyhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a bölcsességet az Azure AD-vel való Invictus](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A bölcsesség Invictus elleni védelem speciális láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)