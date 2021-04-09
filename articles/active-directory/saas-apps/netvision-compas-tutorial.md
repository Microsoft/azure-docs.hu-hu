---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a net Vision Compas | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és net Vision Compas között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519401"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a net Vision-Compas

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a net Vision-Compas a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a net Vision Compas, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a net Vision Compas.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a net Vision Compas az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* NET Vision Compas egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A net Vision Compas támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A net Vision-Compas konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>NET Vision-Compas hozzáadása a gyűjteményből

A net Vision-Compas Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a net Vision-Compas a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **net Vision Compas** kifejezést a keresőmezőbe.
1. Válassza ki a **net Vision Compas** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a net Vision Compas

Konfigurálja és tesztelje az Azure AD SSO-t a net Vision Compas egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a net Vision Compas.

Az Azure AD SSO net Vision-Compas való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Net Vision-COMPAS SSO konfigurálása](#configure-netvision-compas-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Net Vision-Compas-teszt felhasználó konfigurálása](#configure-netvision-compas-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli net Vision-Compas van.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **net Vision Compas** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a net Vision Compas-ügyfélszolgálati [csapatához](mailto:contact@net.vision) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a metaadatok letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)



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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a net Vision Compas.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **net Vision Compas** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netvision-compas-sso"></a>NET Vision Compas SSO konfigurálása

Ebben a szakaszban engedélyezheti az SAML SSO-t a **net Vision Compas**.
1. Jelentkezzen be a **net Vision Compas** egy rendszergazdai fiókkal, és nyissa meg az adminisztrációs területét.

    ![Felügyeleti körzet](media/netvision-compas-tutorial/admin.png)

1. Keresse meg **a rendszerkörnyezetet** , és válassza az **identitás-szolgáltatók** elemet.

    ![Rendszergazdai IDP](media/netvision-compas-tutorial/admin-idps.png)

1. Válassza ki a **Hozzáadás** műveletet az Azure ad új identitásszolgáltató való regisztrálásához.

    ![IDENTITÁSSZOLGÁLTATÓ hozzáadása](media/netvision-compas-tutorial/idps-add.png)

1. Válassza ki az **SAML** elemet a **szolgáltató típusaként**.
1. Adjon meg értelmes értékeket a **megjelenítendő név** és a **Leírás** mezőkhöz.
1. Rendelje hozzá a **net Vision Compas** -felhasználókat a identitásszolgáltató az **elérhető felhasználók** listájának kiválasztásával, majd a **kijelöltek hozzáadása** gombra kattintva. A kiépítési eljárást követve a felhasználókat a IDENTITÁSSZOLGÁLTATÓ is hozzá lehet rendelni.
1. A **metaadatok** SAML beállításnál kattintson a **Fájl választása** gombra, és válassza ki a korábban mentett metaadat-fájlt a számítógépen.
1. Kattintson a **Mentés** gombra.

    ![IDENTITÁSSZOLGÁLTATÓ szerkesztése](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>NET Vision Compas-tesztelési felhasználó konfigurálása

Ebben a szakaszban egy meglévő felhasználót konfigurál a **net Vision Compas** , hogy az Azure ad-t használja az SSO-hoz.
1. Kövesse a **net Vision Compas** felhasználói üzembe helyezési eljárást a vállalat által meghatározott módon, vagy szerkesszen egy meglévő felhasználói fiókot.
1. A felhasználó profiljának meghatározásakor ellenőrizze, hogy a felhasználó E-mail címe **(személyes)** egyezik-e az Azure ad-felhasználónévvel: username@companydomain.extension . Például: `B.Simon@contoso.com`.

    ![Felhasználó szerkesztése](media/netvision-compas-tutorial/user-config.png)

Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban teszteli az Azure AD egyszeri bejelentkezési konfigurációját.

### <a name="using-the-access-panel-idp-initiated"></a>A hozzáférési panel (IDENTITÁSSZOLGÁLTATÓ által kezdeményezett) használata.

Ha a hozzáférési panelen a net Vision Compas csempére kattint, automatikusan be kell jelentkeznie arra a net Vision-Compas, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Közvetlenül a net Vision-Compas (SP által kezdeményezett) elérésére.

1. Hozzáférés a **net Vision Compas** URL-címéhez. Például: `https://tenant.compas.cloud`.
1. Adja meg a **net Vision Compas** felhasználónevét, és kattintson a **Tovább gombra**.

    ![Felhasználói bejelentkezés](media/netvision-compas-tutorial/login-user.png)

1. **(nem kötelező)** Ha a felhasználó több IDP van hozzárendelve a **net Vision-Compas** belül, megjelenik az elérhető IDP listája. Válassza ki a korábban a **net Vision Compas** KONFIGURÁLT Azure ad-identitásszolgáltató.

    ![Bejelentkezés választása](media/netvision-compas-tutorial/login-choose.png)

1. A rendszer átirányítja az Azure AD-ba a hitelesítés elvégzéséhez. A sikeres hitelesítés után automatikusan be kell jelentkeznie arra a net Vision- **Compas** , amelyhez be szeretné állítani az egyszeri bejelentkezést.

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a net Vision Compas az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)