---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pantheonval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Pantheon között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: 900f64856d8f828fe4650ad86d0e8c4366672314
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92506547"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pantheon"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Pantheonval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pantheont Azure Active Directory (Azure AD-val). A Pantheon és az Azure AD integrálásával a következőket teheti:

* A Pantheonhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Pantheonba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Pantheon egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.


* A Pantheon támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést




## <a name="adding-pantheon-from-the-gallery"></a>A Pantheon hozzáadása a katalógusból

A Pantheon Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjteményhez tartozó Pantheont a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Pantheon** kifejezést.
1. Válassza ki a **Pantheon** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-pantheon"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Pantheonhoz

Konfigurálja és tesztelje az Azure AD SSO-t a Pantheon használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a pantheonban található kapcsolódó felhasználó között.

Az Azure AD SSO és a Pantheon konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Pantheon SSO konfigurálása](#configure-pantheon-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Pantheon test User](#create-pantheon-test-user)** -t, hogy a "B. Simon" a Pantheon egyik párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Pantheon** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `urn:auth0:pantheon:<orgname>-SSO`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek megszerzéséhez forduljon a [Pantheon](https://pantheon.io/docs/getting-support/) ügyfélszolgálatához. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Pantheon alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A Pantheon alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail** használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és módosítja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Pantheon beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

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

Ebben a szakaszban a "B. Simon" lehetőséget fogja használni az Azure egyszeri bejelentkezés használatára azáltal, hogy hozzáférést biztosít a Pantheonhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Pantheon** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-pantheon-sso"></a>A Pantheon SSO konfigurálása

A **Pantheon** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítványt** és a megfelelő másolt URL-címeket a [Pantheon támogatási csapatának](https://pantheon.io/docs/getting-support/).

> [!Note]
> Emellett meg kell adnia az E-mail tartomány (ok) adatait és a dátum időpontját, amikor engedélyezni szeretné a kapcsolódást. További részleteket [itt](https://pantheon.io/docs/sso-organizations/) talál

### <a name="create-pantheon-test-user"></a>A Pantheon test User létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a pantheonban. A következő lépésekkel adhatja hozzá a felhasználót a pantheonban. 

>[!NOTE] 
>Ahhoz, hogy az SSO-t a munkahelyi felhasználó számára először létre kell hozni a pantheonban.

1. Jelentkezzen be a Pantheonba rendszergazdai hitelesítő adatokkal.

2. Navigáljon a **szervezeti** irányítópult lapra.
 
3. Kattintson a **személyek** elemre.

4. Kattintson a **felhasználó hozzáadása** elemre.

5. Adja meg a felhasználó e-mail-címét.

6. Válassza ki a felhasználó szerepkörét.

7. Kattintson a **felhasználó hozzáadása** elemre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Pantheon csempére kattint, automatikusan be kell jelentkeznie arra a Pantheonba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Pantheon kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)