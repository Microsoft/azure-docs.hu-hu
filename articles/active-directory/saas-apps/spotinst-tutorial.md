---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Spotinst | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Spotinst között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 402b5a975b3ded8327edcea81b680e9990bac39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Spotinst

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Spotinst a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Spotinst-t az Azure AD-vel, a következőket teheti:

* A Spotinst-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Spotinst az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Spotinst egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Spotinst támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst hozzáadása a gyűjteményből

A Spotinst Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Spotinst a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Spotinst** kifejezést a keresőmezőbe.
1. Válassza ki a **Spotinst** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Spotinst

Konfigurálja és tesztelje az Azure AD SSO-t a Spotinst a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Spotinst-ben.

Az Azure AD SSO és a Spotinst konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SPOTINST SSO konfigurálása](#configure-spotinst-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Spotinst-teszt felhasználót](#create-spotinst-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Spotinst rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Spotinst** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást identitásszolgáltató kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

   1. Győződjön meg arról, hogy a **Válasz URL-címe** a következőre van beállítva: https://console.spotinst.com/auth/saml .
   1. A **továbbítási állapot** mezőben adja meg a Spotinst-szervezet azonosítóját, amelyet az **SSO** lapon is ellenőrizheti.
   1. **A bejelentkezési URL-címnek** üresnek kell lennie.

1. Kattintson a **Mentés** gombra.

1. A Spotinst alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Spotinst alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | -----| --------------- |
    | E-mail | User. mail |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Spotinst beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Spotinst.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Spotinst** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-spotinst-sso"></a>Spotinst SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Spotinst biztonsági rendszergazdaként.

2. Kattintson a képernyő jobb felső részén található **felhasználó ikonra** , majd a **Beállítások** elemre.

    ![A képernyőképen a felhasználó ikonból kiválasztott beállítások láthatók.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kattintson a felül található **Biztonság** lapra, majd válassza az **identitás-szolgáltatók** lehetőséget, majd hajtsa végre a következő lépéseket:

    ![Spotinst biztonság](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Másolja a **továbbítási állapot** értékét a példányhoz, és illessze be a **továbbítási állapot** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **Tallózás** gombra a letöltött metaadatok XML-fájljának feltöltéséhez Azure Portal

    c. Kattintson a **Mentés** gombra.

### <a name="create-spotinst-test-user"></a>Spotinst-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Spotinst-ben.

1. Ha az alkalmazást az **SP** által kezdeményezett módban konfigurálta, hajtsa végre a következő lépéseket:

   a. Egy másik böngészőablakban jelentkezzen be a Spotinst biztonsági rendszergazdaként.

   b. Kattintson a képernyő jobb felső részén található **felhasználó ikonra** , majd a **Beállítások** elemre.

    ![A képernyőképen a felhasználó ikonból kiválasztott beállítások láthatók.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kattintson a **felhasználók** elemre, majd válassza a **felhasználó hozzáadása** elemet.

    ![Képernyőfelvétel: a felhasználók által kiválasztott felhasználó hozzáadása.](./media/spotinst-tutorial/adduser1.png)

    d. A felhasználó hozzáadása szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználó hozzáadása szakasz, ahol megadhatja a leírt értékeket.](./media/spotinst-tutorial/adduser2.png)

    * A **teljes név** szövegmezőbe írja be a felhasználóhoz hasonló teljes nevet `BrittaSimon` .

    * Az **e-mail** szövegmezőbe írja be a felhasználó e-mail címét, például: `brittasimon@contoso.com` .

    * Válassza ki a szervezeti **szerepkör, a fiók szerepkör és a fiókok** szervezetre vonatkozó részleteit.

2. Ha az alkalmazást a **identitásszolgáltató** által kezdeményezett módban konfigurálta, akkor ebben a szakaszban nincs erre a műveletre vonatkozó elem. A Spotinst támogatja az igény szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Új felhasználó jön létre a Spotinst elérésére tett kísérlet során, ha még nem létezik.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Spotinst csempére kattint, automatikusan be kell jelentkeznie arra a Spotinst, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Spotinst kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)