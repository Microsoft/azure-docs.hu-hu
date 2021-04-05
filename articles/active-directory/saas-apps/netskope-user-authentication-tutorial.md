---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Netskope felhasználói hitelesítéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Netskope felhasználói hitelesítés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Netskope felhasználói hitelesítéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Netskope felhasználói hitelesítését Azure Active Directory (Azure AD) használatával. Ha integrálja az Netskope felhasználói hitelesítését az Azure AD-vel, a következőket teheti:

* A Netskope felhasználói hitelesítéshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Netskope a felhasználói hitelesítést az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Netskope felhasználói hitelesítés egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Netskope felhasználói hitelesítése támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Netskope felhasználói hitelesítés hozzáadása a gyűjteményből

A Netskope felhasználói hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Netskope felhasználói hitelesítését a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Netskope felhasználói hitelesítés** kifejezést a keresőmezőbe.
1. Válassza az **Netskope felhasználói hitelesítés** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Netskope felhasználói hitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t Netskope felhasználói hitelesítéssel egy **B. Simon** nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Netskope felhasználói hitelesítése során.

Az Azure AD SSO Netskope felhasználói hitelesítéssel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Netskope felhasználói hitelesítésének konfigurálása](#configure-netskope-user-authentication-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * Netskope-felhasználó- **[hitelesítési teszt felhasználó létrehozása](#create-netskope-user-authentication-test-user)** – a felhasználó Azure ad-Netskope kapcsolódó felhasználói hitelesítéssel rendelkező B. Simon-partneri kapcsolattal rendelkezhet.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Netskope felhasználói hitelesítés** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.goskope.com/<customer entered string>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezeket az értékeket az oktatóanyag későbbi részében ismertetjük.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > A bejelentkezési URL-címek értéke nem valódi. A bejelentkezési URL-cím értékének frissítése a tényleges bejelentkezési URL-címmel. A bejelentkezési URL-cím értékének beszerzéséhez lépjen kapcsolatba a [Netskope felhasználói hitelesítéssel](mailto:support@netskope.com) foglalkozó ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Netskope-felhasználó hitelesítésének beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Netskope felhasználói hitelesítéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Netskope felhasználói hitelesítés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netskope-user-authentication-sso"></a>A Netskope felhasználói hitelesítésének egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Netskope felhasználói hitelesítéssel rendelkező céges webhelyre rendszergazdaként.

1. Kattintson az **aktív platform** fülre.

    ![A képernyőképen a beállítások közül kiválasztott aktív platform látható.](./media/netskope-user-authentication-tutorial/user1.png)

1. Görgessen le a **proxy továbbításához** , és válassza az **SAML** lehetőséget.

    ![A képernyőfelvételen az aktív platformról kiválasztott SAML látható.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Az **SAML-beállítások** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: SAML-beállítások, ahol megadhatja a leírt értékeket.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Másolja az **SAML-entitás azonosítójának** értékét, és illessze be a Azure Portal **alapszintű SAML-konfiguráció** szakaszának **azonosító** szövegmezőbe.

    b. Másolja az **SAML ACS URL-címet** , és illessze be a **Válasz URL-** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

1. Kattintson a **fiók hozzáadása** lehetőségre.

    ![Képernyőfelvétel: a SAML ablaktáblán kiválasztott fiók hozzáadása.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Az **SAML-fiók hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![A képernyőképen az SAML-fiók hozzáadása látható, ahol megadhatja a leírt értékeket.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. A **név** szövegmezőben adja meg a nevet, például az Azure ad-t.

    b. A **identitásszolgáltató URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Az **IDENTITÁSSZOLGÁLTATÓ entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    d. Nyissa meg a letöltött metaadat-fájlt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **identitásszolgáltató-tanúsítvány** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-netskope-user-authentication-test-user"></a>Netskope felhasználói hitelesítési teszt felhasználó létrehozása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Netskope felhasználói hitelesítéssel rendelkező céges webhelyre rendszergazdaként.

1. Kattintson a bal oldali navigációs ablaktábla **Beállítások** fülére.

    ![Képernyőfelvétel: a kiválasztott beállítás.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Kattintson az **aktív platform** fülre.

    ![A képernyőképen a beállítások közül kiválasztott aktív platform látható.](./media/netskope-user-authentication-tutorial/user1.png)

1. Kattintson a **felhasználók** fülre.

    ![A képernyőképen az aktív platformról kiválasztott felhasználók láthatók.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Kattintson a **felhasználók hozzáadása** elemre.

    ![Képernyőfelvétel: a felhasználók párbeszédpanel, amelyen a felhasználók hozzáadása lehetőséget választhatja.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Adja meg a hozzáadni kívánt felhasználó e-mail-címét, majd kattintson a **Hozzáadás** gombra.

    ![Képernyőfelvétel: felhasználók hozzáadása, ahol megadhatja a felhasználók listáját.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Netskope felhasználói hitelesítés csempére kattint, automatikusan be kell jelentkeznie a Netskope felhasználói Hitelesítésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Netskope felhasználói hitelesítésének kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)