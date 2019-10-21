---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BlueJeans for Azure AD-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure AD Azure Active Directory és BlueJeans között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a BlueJeans for Azure AD-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure AD BlueJeans az Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure ad-val integrálja az BlueJeans-t az Azure AD-be, a következőket teheti:

* A BlueJeans for Azure AD-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókkal való BlueJeans.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* BlueJeans az Azure AD egyszeri bejelentkezés (SSO) számára engedélyezett előfizetés esetén.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az BlueJeans for Azure AD támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A BlueJeans for Azure AD támogatja a [felhasználók **automatikus** kiépítési felállítását](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>BlueJeans hozzáadása az Azure AD-hez a katalógusból

Az Azure ad-hez készült BlueJeans az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BlueJeans for Azure AD-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **BLUEJEANS for Azure ad** kifejezést a keresőmezőbe.
1. Válassza az **BlueJeans for Azure ad** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az Azure AD-hez készült BlueJeans

Konfigurálja és tesztelje az Azure AD SSO-t a BlueJeans for Azure AD-vel egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BlueJeans for Azure AD-ben.

Az Azure AD SSO az Azure AD-vel való BlueJeans való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Azure ad SSO BlueJeans konfigurálása](#configure-bluejeans-for-azure-ad-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[BlueJeans létrehozása az Azure ad test userhez](#create-bluejeans-for-azure-ad-test-user)** – ha az Azure ad-hez tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-BlueJeans rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **Azure ad** -alkalmazás integrációjának BlueJeans lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba az [Azure ad-ügyfél támogatási csapatának BlueJeans](https://support.bluejeans.com/contact) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Azure ad BlueJeans beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a BlueJeans for Azure AD-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **BlueJeans az Azure ad**-hoz lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Az Azure AD SSO BlueJeans konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **BlueJeans az Azure ad** vállalati webhelyre rendszergazdaként.

2. Lépjen a **rendszergazda \> csoport beállításai \> biztonság**menüpontra.

    ![Felügyeleti](./media/bluejeans-tutorial/ic785868.png "Felügyeleti")

3. A **Biztonság** szakaszban hajtsa végre a következő lépéseket:

    ![SAML egyszeri bejelentkezés](./media/bluejeans-tutorial/ic785869.png "SAML egyszeri bejelentkezés")

    a. Válassza **az SAML egyszeri bejelentkezés**lehetőséget.

    b. Válassza az **automatikus kiépítés engedélyezése**lehetőséget.

4. A következő lépésekkel léphet be:

    ![Tanúsítvány elérési útja](./media/bluejeans-tutorial/ic785870.png "Tanúsítvány elérési útja")

    a. Kattintson a **Fájl választása**gombra a Azure Portal által letöltött base-64 kódolt tanúsítvány feltöltéséhez.

    b. A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. A **jelszó-módosítási URL** szövegmezőbe illessze be a **jelszó módosítása URL-címet** , amelyet a Azure Portal másolt.

    d. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

5. A következő lépésekkel léphet be:

    ![Módosítások mentése](./media/bluejeans-tutorial/ic785874.png "Módosítások mentése")

    a. A **felhasználói azonosító** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Az **e-mail** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kattintson a **módosítások mentése**gombra.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans létrehozása az Azure AD test userhez

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót az Azure AD BlueJeans. Az Azure AD-hez készült BlueJeans támogatja a felhasználók automatikus üzembe helyezését, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](bluejeans-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **BlueJeans az Azure ad** vállalati webhelyre rendszergazdaként.

2. Lépjen a **rendszergazda \> a felhasználók kezelése \> a felhasználó hozzáadása**lehetőségre.

    ![Felügyeleti](./media/bluejeans-tutorial/ic785877.png "Felügyeleti")

    > [!IMPORTANT]
    > A **felhasználó hozzáadása** lap csak akkor érhető el, ha a **biztonsági lapon**az **automatikus kiépítés engedélyezése** nincs bejelölve.

3. A **felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/ic785886.png "Felhasználó hozzáadása")

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    c. A **válasszon BlueJeans for Azure ad-felhasználónevet** szövegmezőbe írja be a felhasználó (például a **Brittasimon** ) felhasználónevét.

    d. A **jelszó létrehozása** szövegmezőbe írja be a jelszavát.

    e. A **vállalati** szövegmezőbe írja be a vállalatát.

    f. Az **e-mail-cím** szövegmezőbe írja be a (z) `b.simon\@contoso.com` felhasználó e-mail-címét.

    g. A **BlueJeans létrehozása az Azure ad Meeting I. D** szövegmezőbe mezőben adja meg az értekezlet azonosítóját.

    h. A **válasszon egy moderátori PIN-kódot** szövegmezőbe írja be a PIN-kódját.

    i. Kattintson a **Folytatás**gombra.

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/ic785887.png "Felhasználó hozzáadása")

    J. Kattintson a **felhasználó hozzáadása**elemre.

> [!NOTE]
> A BlueJeans for Azure ad által biztosított Azure ad felhasználói fiók létrehozási eszközeihez vagy API-khoz bármilyen más BlueJeans is használhat az Azure ad felhasználói fiókjainak kiépítéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a BlueJeans for Azure AD csempére kattint, automatikusan be kell jelentkeznie a BlueJeans for Azure AD-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az Azure ad BlueJeans kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

