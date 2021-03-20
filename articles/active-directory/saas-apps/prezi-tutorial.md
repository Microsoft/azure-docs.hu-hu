---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Prezi | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Prezi között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515321"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Prezi

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Prezi a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Prezi-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja, hogy ki férhet hozzá a Prezi az Azure AD-ben.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Prezi az Azure AD-fiókjával.
* A fiókokat a Azure Portal kezelheti.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezéssel (SSO) rendelkező Prezi-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Prezi az SP és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést is támogatja.
* A Prezi támogatja az igény szerinti felhasználói üzembe helyezést.
* A Prezi konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-prezi-from-the-gallery"></a>Prezi hozzáadása a gyűjteményből

A Prezi Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Prezi a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. A bal szélső panelen válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Prezi** kifejezést a keresőmezőbe.
1. Válassza az **Prezi** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Azure AD SSO konfigurálása és tesztelése a Prezi-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Prezi egy B. Simon nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Prezi-ben.

Az Azure AD SSO és a Prezi konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad SSO teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad SSO használatát.
1. [Konfigurálja a PREZI SSO](#configure-prezi-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy Prezi](#create-a-prezi-test-user) , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon Prezi rendelkezik.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezése a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/) **Prezi** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** ikont az **alapszintű SAML-konfiguráció** beállításainak szerkesztéséhez.

   ![Az alapszintű SAML-konfigurációs beállítások szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak semmilyen lépést nem kell tennie, mert az alkalmazás már előre integrálva van az Azure-ban.

1. Válassza a **további URL-címek beállítása** lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP**-kezdeményezésű módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** mezőbe írja be a következő URL-címet: `https://prezi.com/login/sso/` .

1. Kattintson a **Mentés** gombra.

1. A Prezi alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Felhasználói attribútumok & jogcímek](common/default-attributes.png)

1. A Prezi alkalmazás Emellett néhány további attribútumot is vár az SAML-válaszban, ahogy az itt látható. Ezek az attribútumok előre fel vannak töltve, de a követelmények alapján áttekinthetők.
    
    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | given_name | User. givenName |
    | family_name | felhasználó. vezetéknév |

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** szolgáltatást. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Prezi beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal szélső paneljén válassza a **Azure Active Directory** lehetőséget. Lépjen a **felhasználók**, majd a **minden felhasználó** elemre.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A felhasználó tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .
   1. A **Felhasználónév** mezőbe írja be például a `username@companydomain.extension` következőt: `B.Simon@contoso.com` .
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure SSO használatát azáltal, hogy hozzáférést biztosít a Prezi.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Prezi** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A felhasználók és csoportok hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a felhasználók listából, és kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a **kiválasztás** gombra a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

## <a name="configure-prezi-sso"></a>Prezi SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Prezi-be a csapat fiókjával, és nyissa meg a [felügyeleti konzolt](https://prezi.com/organizations/manage).

1. A **felügyeleti konzolon** kattintson a **Beállítások** fülre.

    ![Beállítások lap](./media/prezi-tutorial/settings-image.png)

1. Nyissa meg az **egyszeri Sign-On (SSO)** szakaszt, és kapcsolja be a kapcsolót az egyszeri bejelentkezés engedélyezéséhez.
    
    ![Egyszeri Sign-On (SSO) váltógomb](./media/prezi-tutorial/single-signon.png)

1. Az **egyszeri bejelentkezés (SSO)** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés (SSO) szakasz](./media/prezi-tutorial/configuration.png)

    1. Az **azonosító vagy a kiállító URL-címe** mezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    1. Az **SAML 2,0-végpont (http)** mezőben illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    1. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben. Másolja a tanúsítvány tartalmát, és illessze be a tartalmat a **tanúsítványba (X. 509)** .

    1. Kattintson a **Mentés** gombra.

### <a name="create-a-prezi-test-user"></a>Prezi-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Prezi-ben. A Prezi támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs művelet. Ha egy felhasználó még nem létezik a Prezi-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával teszteli.

Amikor kiválasztja a Prezi csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Prezi-fiókba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
- [A Prezi kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)
- [A Prezi és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)