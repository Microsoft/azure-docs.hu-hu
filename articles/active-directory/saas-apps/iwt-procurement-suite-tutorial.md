---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a belvízi beszerzési csomaggal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a belvízi adatgyűjtési csomag között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2020
ms.author: jeedes
ms.openlocfilehash: c39326074763a3ebcaec7682c10e6e1d39aa5f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iwt-procurement-suite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a belvízi adatgyűjtési csomaggal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a belvízi beszerzési csomagot Azure Active Directory (Azure AD-val). A belvízi beszerzési csomag Azure AD-vel való integrálásakor a következőket teheti:

* A belvízi adatgyűjtési csomaghoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a belvízi adatgyűjtési csomagba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A belvízi beszerzési csomag egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A belvízi beszerzési csomag támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A belvízi adatgyűjtési csomag konfigurálása után kényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását is biztosítja. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iwt-procurement-suite-from-the-gallery"></a>BELVÍZI beszerzési csomag hozzáadása a katalógusból

A belvízi adatgyűjtési csomag Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájára kell felvennie a belvízi adatgyűjtési csomagot.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **belvízi beszerzés Suite** a keresőmezőbe.
1. Válassza a **belvízi beszerzési csomag** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iwt-procurement-suite"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a belvízi adatgyűjtési csomaghoz

Konfigurálja és tesztelje az Azure AD SSO-t a belvízi adatgyűjtési csomaggal egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a belvízi adatgyűjtési csomagban.

Az Azure AD SSO a belvízi adatgyűjtési csomaggal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[belvízi adatgyűjtési csomag egyszeri bejelentkezésének konfigurálása](#configure-iwt-procurement-suite-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[belvízi adatgyűjtési csomag tesztelési felhasználójának létrehozása](#create-iwt-procurement-suite-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-partneri kapcsolattal rendelkezik a belvízi adatgyűjtési csomagban.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **belvízi adatgyűjtési csomag** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://[customersubdomain].ionwave.net/sso/[customerid]`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://[customersubdomain].ionwave.net/sso/[customerid]`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot a [belvízi adatgyűjtési csomag ügyfél-támogatási csoportjával](mailto:support@ionwave.net) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A belvízi közbeszerzés Suite-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol az **EmailAddress** a **User. mail**-sel van leképezve. A belvízi közbeszerzés Suite-alkalmazás az **EmailAddress** -t a **User. userPrincipalName** használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/default-attributes.png)



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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a belvízi adatgyűjtési csomaghoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **belvízi beszerzési csomag** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-iwt-procurement-suite-sso"></a>A belvízi beszerzések csomagjának egyszeri bejelentkezésének konfigurálása

A **belvízi adatgyűjtési csomag** egyszeri bejelentkezésének konfigurálásához el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [belvízi adatgyűjtési csomag támogatási csapatának](mailto:support@ionwave.net). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-iwt-procurement-suite-test-user"></a>A belvízi adatgyűjtési csomag tesztelési felhasználójának létrehozása

Ebben a szakaszban létrehoz egy Britta Simon nevű felhasználót a belvízi adatgyűjtési csomagban. A belvízi adatgyűjtési [csomag támogatási csapatával](mailto:support@ionwave.net) felveheti a felhasználókat a belvízi adatgyűjtési csomag platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a belvízi beszerzések csomagja csempére kattint, automatikusan be kell jelentkeznie a belvízi adatgyűjtési csomagba, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A belvízi adatgyűjtési csomag kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A belvízi adatgyűjtési csomag védetté tétele fejlett láthatósággal és ellenőrzésekkel](/cloud-app-security/proxy-intro-aad)