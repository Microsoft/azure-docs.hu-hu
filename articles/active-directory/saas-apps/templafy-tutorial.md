---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Templafy egy SAML2 | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Templafy egy SAML2 között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d83bf1b6e986f867a3781b5da7d2784e65815520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515820"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy-saml2"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Templafy-egy SAML2

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Templafy-egy SAML2 a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Templafy egy SAML2, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Templafy egy SAML2.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Templafy egy SAML2 az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Templafy egy SAML2 egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Templafy egy SAML2 támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Templafy egy SAML2 **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-templafy-saml2-from-the-gallery"></a>Templafy-egy SAML2 hozzáadása a gyűjteményből

A Templafy-egy SAML2 Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Templafy-egy SAML2 a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Templafy egy saml2** kifejezést a keresőmezőbe.
1. Válassza ki a **TEMPLAFY egy saml2** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy-saml2"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Templafy egy SAML2

Konfigurálja és tesztelje az Azure AD SSO-t a Templafy egy SAML2 egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Templafy egy SAML2.

Az Azure AD SSO Templafy-egy SAML2 való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Templafy-egy SAML2 SSO konfigurálása](#configure-templafy-saml2-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Templafy-egy saml2-teszt felhasználó létrehozása](#create-templafy-saml2-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon TEMPLAFY-egy saml2 rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Templafy egy saml2** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a Templafy egy SAML2-ügyfélszolgálati [csapatához](mailto:support@templafy.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Templafy egy SAML2 alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Templafy egy SAML2 alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum| Névtér  |
    | ---------------| --------------- | --------- |
    | givenname | User. givenName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | surname | felhasználó. vezetéknév | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | EmailAddress | User. mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | streetAddress | User. streetAddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | User. City | `http://schemas.templafy.com/2016/06/identity/claims`|
    | Irányítószám | User. irányítószám | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | StateorProvince | felhasználó. állapot | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | ország | felhasználó. ország | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | beosztás | User. beosztás | `http://schemas.templafy.com/2016/06/identity/claims`|
    | Részleg | felhasználó. részleg | `http://schemas.templafy.com/2016/06/identity/claims`|
    | telefonszám | User. telephoneNumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | User. érték facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | UPN | User. userPrincipalName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | User. mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Templafy egy SAML2.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **TEMPLAFY egy saml2** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-templafy-saml2-sso"></a>Templafy egy SAML2 SSO konfigurálása

Az egyszeri bejelentkezés **Templafy-egy saml2** való konfigurálásához el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [Templafy egy saml2 támogatási csapatának](mailto:support@templafy.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-templafy-saml2-test-user"></a>Templafy egy SAML2-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Templafy egy SAML2. A Templafy egy SAML2 támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a Templafy egy SAML2, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Templafy egy SAML2 csempére kattint, automatikusan be kell jelentkeznie arra a Templafy-egy SAML2, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Templafy egy SAML2 az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)