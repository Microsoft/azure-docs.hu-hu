---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Emődi-vel – stratégiai felügyelet | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Emődi között a stratégiai felügyelethez.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.openlocfilehash: d443f1f85caf53a40d5cd80b4f9438427d18a4fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Emődi-vel – stratégiai felügyelet

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Emődi a stratégiai felügyelettel Azure Active Directory (Azure AD) használatával. Ha integrálja a Emődi-t az Azure AD-vel való stratégiai gondossággal, a következőket teheti:

* Az Azure AD-ben való hozzáférés Emődi navigáljon a stratégiai felügyelethez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Emődi az Azure AD-fiókjával való stratégiai felügyeletet.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Emődi a stratégiai ellátás egyszeri bejelentkezés (SSO) engedélyezett előfizetését navigálja.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Emődi – stratégiai felügyelet – az **SP** által KEZDEMÉNYEZett SSO-t támogatja

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>Emődi hozzáadása stratégiai Care a katalógusból

A Emődi integrációjának konfigurálásához navigáljon az Azure AD-be stratégiai gondossággal, a Emődi navigáljon stratégiai gondossággal a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Emődi navigáljon a stratégiai** területen a keresőmezőbe.
1. Válassza a **Emődi stratégiai felügyelet** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Azure AD-alapú egyszeri bejelentkezés konfigurálása és tesztelése a Emődi-hez – stratégiai felügyelet

Konfigurálja és tesztelje az Azure AD SSO-t a Emődi, és navigáljon a stratégiai felügyelethez egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Emődi-ben, és navigáljon a stratégiai felügyeletbe.

Az Azure AD SSO konfigurálásához és teszteléséhez a Emődi navigáljon a stratégiai felügyelettel, végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Emődi konfigurálása a stratégiai felügyelettel kapcsolatos egyszeri bejelentkezéshez](#configure-eab-navigate-strategic-care-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    1. **[Emődi létrehozása a stratégiai Care-teszt felhasználóhoz](#create-eab-navigate-strategic-care-test-user)** – ha a Emődi B. Simon partnere, navigáljon a felhasználó Azure ad-képviseletéhez kapcsolódó stratégiai gondossággal.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Emődi navigáljon a stratégiai Care** Application Integration oldalon, keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a Emődi, és [navigáljon a stratégiai gondozási ügyfélszolgálat csapatához](mailto:tech@gradesfirst.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Emődi navigáló stratégiai felügyelethez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Emődi navigáljon a stratégiai felügyeletre**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-eab-navigate-strategic-care-sso"></a>Emődi konfigurálása stratégiai Care SSO-hoz

Ha az egyszeri bejelentkezést a Emődi-re szeretné beállítani, akkor **navigáljon a stratégiai felügyelet** oldalára, az **alkalmazás-összevonási metaadatok URL-címét** kell elküldenie a [Emődi navigáló stratégiai tanácsadó csapat](mailto:tech@gradesfirst.com)számára. Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-eab-navigate-strategic-care-test-user"></a>Emődi létrehozása stratégiai Care-teszt felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Emődi-ben navigáljon a stratégiai felügyelethez. A Emődi használata a [stratégiai gondozási támogatással foglalkozó csapatával](mailto:tech@gradesfirst.com) a felhasználók hozzáadásához a Emődi navigáló stratégiai platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Emődi navigálása a stratégiai Care csempére kattint, akkor automatikusan be kell jelentkeznie a Emődi navigáló stratégiai gondossággal, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Emődi az Azure AD-vel való stratégiai gondossággal](https://aad.portal.azure.com/)