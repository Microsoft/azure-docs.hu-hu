---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PurelyHR | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és PurelyHR között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515185"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PurelyHR

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PurelyHR a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az PurelyHR-t az Azure AD-vel, a következőket teheti:

* A PurelyHR-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a PurelyHR az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* PurelyHR egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A PurelyHR támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A PurelyHR **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-purelyhr-from-the-gallery"></a>PurelyHR hozzáadása a gyűjteményből

A PurelyHR Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PurelyHR a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PurelyHR** kifejezést a keresőmezőbe.
1. Válassza ki a **PurelyHR** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a PurelyHR

Konfigurálja és tesztelje az Azure AD SSO-t a PurelyHR a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a PurelyHR-ben.

Az Azure AD SSO és a PurelyHR konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[PURELYHR SSO konfigurálása](#configure-purelyhr-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre PurelyHR-teszt felhasználót](#create-purelyhr-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-PurelyHR rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **PurelyHR** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<companyID>.purelyhr.com/sso-consume`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címével és Sign-On URL-címmel. Az értékek lekéréséhez forduljon a PurelyHR ügyfélszolgálati [csapatához](https://support.purelyhr.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PurelyHR beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a PurelyHR.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **PurelyHR** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-purelyhr-sso"></a>PurelyHR SSO konfigurálása

1. A PurelyHR belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **PurelyHR beállítása** elemre, majd a PurelyHR alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a PurelyHR való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a PurelyHR, nyisson meg egy új böngészőablakot, és jelentkezzen be a PurelyHR vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Nyissa meg az **irányítópultot** az eszköztáron található beállítások közül, majd kattintson az **SSO-beállítások** elemre.

1. Illessze be a mezőkben szereplő értékeket az alább leírtak szerint:

    ![Egyetlen Sign-On konfigurálása](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Nyissa meg a Jegyzettömbben Azure Portal letöltött **tanúsítványt (Bas64)** , és másolja a tanúsítvány értékét. Illessze be a másolt értéket az **X. 509-tanúsítvány** mezőbe.

    b. A **identitásszolgáltató kiállító URL-címe** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosítót** .

    c. A **identitásszolgáltató-végpont URL-címe** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL-címet** . 

    d. Jelölje be a **felhasználók automatikus létrehozása** jelölőnégyzetet az automatikus felhasználó-kiépítés engedélyezéséhez a PurelyHR-ben.

    e. A beállítások mentéséhez kattintson a **módosítások mentése** gombra.

### <a name="create-purelyhr-test-user"></a>PurelyHR-tesztelési felhasználó létrehozása

Ez a lépés általában nem szükséges, mivel az alkalmazás csak időben támogatja a felhasználók üzembe helyezését. Ha az automatikus felhasználó-kiépítés nincs engedélyezve, a manuális felhasználói létrehozás az alább leírtak szerint végezhető el.

Jelentkezzen be a Velpic SAML-céges webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a kezelés fülre, és lépjen a felhasználók szakaszra, majd kattintson az új gombra a felhasználók hozzáadásához.

    ![felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

2. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![felhasználó!](./media/velpicsaml-tutorial/velpic_8.png)

    a. Az **Utónév** szövegmezőbe írja be a B utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a Simon vezetéknevét.

    c. A **Felhasználónév** szövegmezőbe írja be a B. Simon nevű felhasználónevet.

    d. Az **e-mail** szövegmezőbe írja be a fiók e-mail-címét B.Simon@contoso.com .

    e. A többi adat megadása nem kötelező, szükség esetén kitöltheti.

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a PurelyHR csempére kattint, automatikusan be kell jelentkeznie arra a PurelyHR, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A PurelyHR kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)