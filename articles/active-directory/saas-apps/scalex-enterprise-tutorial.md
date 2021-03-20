---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ScaleX Enterprise-nal | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a ScaleX Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: a2065286581b6e7de61e4d1a29382b98563cdbb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895056"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ScaleX Enterprise-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ScaleX Enterprise-t Azure Active Directory (Azure AD-val). Ha integrálja a ScaleX Enterprise-t az Azure AD-vel, a következőket teheti:

* A ScaleX Enterprise-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ScaleX Enterprise szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ScaleX Enterprise egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A ScaleX Enterprise támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ScaleX Enterprise hozzáadása a katalógusból

A ScaleX Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ScaleX Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ScaleX Enterprise** kifejezést a keresőmezőbe.
1. Válassza ki a **ScaleX Enterprise** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a ScaleX Enterprise-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a ScaleX Enterprise használatával egy **B. Simon** nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a ScaleX Enterprise kapcsolódó felhasználója között.

Az Azure AD SSO és a ScaleX Enterprise konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ScaleX vállalati egyszeri bejelentkezés konfigurálása](#configure-scalex-enterprise-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy ScaleX Enterprise test User](#create-scalex-enterprise-test-user)** -t, hogy rendelkezzen a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ScaleX.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **ScaleX Enterprise** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://platform.rescale.com/saml2/<company id>/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba a [ScaleX Enterprise ügyfél-támogatási csapatával](https://info.rescale.com/contact_sales) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A ScaleX Enterprise-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol az **EmailAddress** a **User. mail**-sel van leképezve. A ScaleX Enterprise-alkalmazás az **EmailAddress** -t a **User. userPrincipalName** leképezésével rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **ScaleX Enterprise beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a ScaleX Enterprise-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **ScaleX Enterprise** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-scalex-enterprise-sso"></a>ScaleX vállalati egyszeri bejelentkezés konfigurálása

1. A ScaleX Enterprise-on belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **ScaleX Enterprise beállítása** elemre, és a ScaleX Enterprise alkalmazásra irányítja. A ScaleX Enterprise-ba való bejelentkezéshez adja meg a rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a ScaleX Enterprise-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a ScaleX Enterprise vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a jobb felső sarokban található menüre, majd válassza a **contoso felügyelet** lehetőséget.

    > [!NOTE]
    > A contoso csak példaként szolgál. Ez legyen a vállalat tényleges neve.

    ![A jobb felső sarokban található menüből kiválasztott cég nevét bemutató képernyőkép.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Válassza az **integrációk** lehetőséget a felső menüben, majd válassza az **egyszeri bejelentkezés** lehetőséget.

    ![A legördülő menüből kiválasztott "integrációs" és "egyszeri bejelentkezés" képernyőt megjelenítő képernyőkép.](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Töltse ki az űrlapot a következőképpen:

    ![Egyszeri bejelentkezés konfigurálása](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Válassza **a minden olyan felhasználó létrehozása, aki tud hitelesíteni az SSO-t**

    b. **Szolgáltató SAML**: illessze be az **_urn: Oasis: Names: TC: SAML: 2.0: nameid-Format: perzisztens_ értéket.**

    c. A **személyazonosság-szolgáltató e-mail-mezőjének neve az ACS-válaszban**: illessze be az értéket`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor-entitás azonosítója:** Illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    e. **Identity Provider SingleSignOnService URL-címe:** Illessze be a **bejelentkezési URL-címet** a Azure Portal.

    f. **Identitáskezelés nyilvános X509 tanúsítványa:** Nyissa meg az Azure-ban a Jegyzettömbben letöltött X509-tanúsítványt, és illessze be a mezőbe a tartalmakat. Győződjön meg arról, hogy a tanúsítvány tartalmának közepén nincsenek sortörések.

    : Jelölje be a következő jelölőnégyzeteket: **engedélyezve, titkosítás NameID és AuthnRequests aláírása.**

    h. A beállítások mentéséhez kattintson az **SSO-beállítások frissítése** elemre.

### <a name="create-scalex-enterprise-test-user"></a>ScaleX vállalati tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a ScaleX Enterprise-ba, a ScaleX Enterprise-ban kell kiépíteni őket. A ScaleX Enterprise esetében a kiépítés automatikus feladat, és nincs szükség manuális lépésekre. Az egyszeri bejelentkezéses hitelesítő adatokkal sikeresen hitelesítő felhasználók automatikusan kiépítve lesznek a ScaleX oldalon.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ScaleX Enterprise csempére kattint, akkor automatikusan be kell jelentkeznie a ScaleX Enterprise-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A ScaleX Enterprise kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)