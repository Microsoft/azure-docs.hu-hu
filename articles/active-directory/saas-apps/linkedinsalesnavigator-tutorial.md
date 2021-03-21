---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn Sales Navigator szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a LinkedIn Sales Navigator között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn Sales Navigator szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LinkedIn Sales Navigatort Azure Active Directory (Azure AD) használatával. A LinkedIn Sales Navigator és az Azure AD integrálásával a következőket teheti:

* A LinkedIn Sales navigatorhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a LinkedIn Sales Navigatorba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A LinkedIn Sales Navigator egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A LinkedIn Sales Navigator támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A LinkedIn Sales Navigator **csak időben támogatja a** felhasználók üzembe helyezését
* A LinkedIn Sales Navigator támogatja a [felhasználók **automatikus** kiépítési felállítását](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn Sales Navigator hozzáadása a katalógusból

A LinkedIn Sales Navigator Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LinkedIn Sales Navigatort a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LinkedIn Sales Navigator** kifejezést a keresőmezőbe.
1. Válassza a **LinkedIn Sales Navigator** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a LinkedIn értékesítési Navigátorhoz

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn Sales Navigator használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a LinkedIn Sales Navigatorban.

Az Azure AD SSO és a LinkedIn Sales Navigator konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[LinkedIn Sales Navigator SSO konfigurálása](#configure-linkedin-sales-navigator-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * A **[LinkedIn Sales Navigator tesztelési felhasználójának létrehozása](#create-linkedin-sales-navigator-test-user)** – ha a LinkedIn Sales Navigatorban található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **LinkedIn Sales Navigator** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az **entitás azonosítójának** értékét, majd másolja a LinkedIn-portál entitás-azonosító értékét az oktatóanyag későbbi részében ismertetett értékre.

    b. A **Válasz URL-címe** szövegmezőbe írja be az " **ügyfél-hozzáférési (ACS-) URL-cím** " értéket, amely az oktatóanyag későbbi részében ismertetett, a LinkedIn-portálon megjelenő URL-cím értékét másolja.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. A LinkedIn Sales Navigator alkalmazás meghatározott formátumban várja az SAML-kikötéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a LinkedIn Sales Navigator alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | --- | --- |
    | e-mail| User. mail |
    | Részleg| felhasználó. részleg |
    | FirstName| User. givenName |
    | LastName| felhasználó. vezetéknév |
    | Egyedi felhasználói azonosító | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn Sales Navigator beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a LinkedIn Sales Navigator elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LinkedIn Sales Navigator** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-linkedin-sales-navigator-sso"></a>A LinkedIn Sales Navigator egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **LinkedIn Sales Navigator** webhelyére rendszergazdaként.

1. A **Account Centerben** kattintson a **globális beállítások** elemre a **Beállítások** területen. Emellett válassza a legördülő listából a **Sales Navigator** elemet.

    ![Képernyőfelvétel: az alkalmazás beállításai, ahol kiválaszthatja az értékesítési Navigátort.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson **vagy kattintson ide az egyes mezők az űrlapról való betöltéséhez és másolásához** , majd hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: egyetlen Sign-On, ahol megadhatja a leírt értékeket.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Másolja ki az **entitás azonosítóját** , és illessze be a Azure Portal **ALAPszintű SAML-konfigurációjának** **azonosító** szövegmezőbe.

    b. Másolja ki az alkalmazás **-hozzáférési (ACS) URL-címet** , és illessze be az **ALAPszintű SAML-konfiguráció** **Válasz URL-címe** szövegmezőbe a Azure Portal.

1. Ugrás a **LinkedIn admin Settings** szakaszra. Töltse fel a Azure Portal letöltött XML-fájlt az **XML-fájl feltöltése** lehetőségre kattintva.

    ![Képernyőfelvétel: a LinkedIn Service Provider S S O beállításainak konfigurálása, ahol feltölthető egy X M L-fájl.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Az egyszeri bejelentkezés engedélyezéséhez kattintson **a be** gombra. Az SSO-állapot változásai **nem** csatlakoztatott **kapcsolattal**

    ![Képernyőfelvétel: egyetlen Sign-On, ahol engedélyezheti a felhasználók hitelesítését S S O-vel.](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>A LinkedIn Sales Navigator tesztelési felhasználó létrehozása

A társított Sales Navigator-alkalmazás támogatja az igény szerinti (JIT) felhasználók üzembe helyezését, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A licencek **automatikus kiosztásának** aktiválása a felhasználóhoz való hozzárendeléshez.

   ![Azure AD-tesztkörnyezet létrehozása](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a LinkedIn Sales Navigator csempére kattint, automatikusan be kell jelentkeznie a LinkedIn Sales Navigatorba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A LinkedIn Sales Navigator kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)