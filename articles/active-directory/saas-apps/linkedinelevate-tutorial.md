---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn emelt szintű szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a LinkedIn jogosultságszint-emelése között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: d4410a39cc9b04565d7b753b7821e11c8ece2593
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458540"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a LinkedIn emelt szintű szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LinkedIn-t a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja a LinkedIn-t az Azure AD-val, a következőket teheti:

* A LinkedIn jogosultságszint-emeléshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a LinkedIn szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A LinkedIn megemelő egyszeri bejelentkezés (SSO) engedélyezett előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.



* A LinkedIn Jogosultságszint-emelés támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A LinkedIn jogosultságszint-emelési **szolgáltatás csak időben támogatja a** felhasználók üzembe helyezését
* A LinkedIn jogosultságszint-emelése támogatja az [ **automatikus** felhasználó-kiépítés használatát](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn-jogosultságszint hozzáadása a katalógusból

A LinkedIn emelt szintű Azure AD-integrációjának konfigurálásához hozzá kell adnia a LinkedIn-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LinkedIn Jogosultságszint-emelés** elemet a keresőmezőbe.
1. Válassza a **LinkedIn Jogosultságszint-emelés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a LinkedIn jogosultságszint-emeléshez

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn emelt szintű használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a LinkedIn jogosultságszint-emelésben.

Az Azure AD SSO konfigurálásához és a LinkedIn emelt szintű teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[LinkedIn jogosultságszint-emelésének beállítása](#configure-linkedin-elevate-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[LinkedIn jogosultságszint-emelési teszt felhasználójának létrehozása](#create-linkedin-elevate-test-user)** – ha a LinkedIn emelt szintű, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **LinkedIn jogosultságszint-emelése** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az **entitás azonosítójának** értékét, majd másolja a LinkedIn-portál entitás-azonosító értékét az oktatóanyag későbbi részében ismertetett értékre.

    b. A **Válasz URL-címe** szövegmezőbe írja be az " **ügyfél-hozzáférési (ACS-) URL-cím** " értéket, amely az oktatóanyag későbbi részében ismertetett, a LinkedIn-portálon megjelenő URL-cím értékét másolja.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. A LinkedIn jogosultságszint-emelési alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A LinkedIn jogosultságszint-emelési alkalmazás azt várja, hogy a NameIdentifier a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a Szerkesztés ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a LinkedIn jogosultságszint-emelési alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | -------| -------------|
    | Részleg | felhasználó. részleg |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn jogosultságszint-emelésének beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a LinkedIn jogosultságszint-emeléshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LinkedIn Jogosultságszint-emelés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-linkedin-elevate-sso"></a>A LinkedIn-emelt egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LinkedIn emelt szintű bérlőre rendszergazdaként.

1. A **Account Centerben** kattintson a **globális beállítások** elemre a **Beállítások** területen. A legördülő listából válassza az **emelt szintű HRE teszt** lehetőséget.

    ![A képernyőképen a globális beállítások láthatók, ahol kiválaszthatja a D tesztet.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson **vagy kattintson ide az egyes mezők az űrlapról való betöltéséhez és másolásához** , majd hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: egyetlen Sign-On, ahol megadhatja a leírt értékeket.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Másolja ki az **entitás azonosítóját** , és illessze be a Azure Portal **ALAPszintű SAML-konfigurációjának** **azonosító** szövegmezőbe.

    b. Másolja ki az alkalmazás **-hozzáférési (ACS) URL-címet** , és illessze be az **ALAPszintű SAML-konfiguráció** **Válasz URL-címe** szövegmezőbe a Azure Portal.

1. Ugrás a **LinkedIn admin Settings** szakaszra. Töltse fel a Azure Portal letöltött XML-fájlt az XML-fájl feltöltése lehetőségre kattintva.

    ![Képernyőfelvétel: a LinkedIn Service Provider S S O beállításainak konfigurálása, ahol feltölthető egy X M L-fájl.](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Az egyszeri bejelentkezés engedélyezéséhez kattintson **a be** gombra. Az SSO-állapot **nem** kapcsolódik **kapcsolathoz**

    ![Képernyőfelvétel: egyetlen Sign-On, ahol kiválaszthatja a licencek automatikus hozzárendelését.](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn jogosultságszint-emelési teszt felhasználó létrehozása

A LinkedIn jogosultságszint-emelési alkalmazás a felhasználók üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A LinkedIn jogosultságszint-emelési portál rendszergazdai beállítások lapján a kapcsoló **automatikusan hozzárendeli a licenceket** az aktív üzembe helyezéshez, és ez a felhasználóhoz is hozzárendel egy licencet. A LinkedIn Jogosultságszint-emelés a felhasználók automatikus üzembe helyezését is támogatja. [További részletekért](linkedinelevate-provisioning-tutorial.md) tekintse meg az automatikus felhasználó-kiépítés konfigurálását ismertető témakört.

   ![Azure AD-tesztkörnyezet létrehozása](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a LinkedIn Jogosultságszint-emelés csempére kattint, automatikusan be kell jelentkeznie arra a LinkedIn-jogosultságszint-emelésre, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A LinkedIn-jogosultságszint kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)