---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a saját Mood szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a saját hangulat között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: b1f8fb4c6586281352b6da05978c6ca4dac23ce5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92504796"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a saját Mood szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD). Ha az Azure AD-vel integrálja a saját hangulatot, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a főhangulathoz.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek a saját környezetbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezés (SSO) támogatja **az SP-** t
* A felépítés **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-supermood-from-the-gallery"></a>A saját Mood hozzáadása a katalógusból

A saját környezet Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: a keresőmezőbe írja be a **főhangulat** kifejezést.
1. Válassza az eredménypanelen az eredmények **panelen, majd** adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a saját Mood szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a saját Mood használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a főhangulatban.

Az Azure AD SSO a következő építőelemek konfigurálásával és tesztelésével végezhető el:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[felderített egyszeri bejelentkezés konfigurálása](#configure-supermood-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy saját Mood-teszt felhasználót](#create-supermood-test-user)** – ha a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-partneri jogosultsággal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a saját **hangulatú** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. **További URL-címek beállításának** engedélyezése.
    
    b. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `https://supermood.co/auth/sso/saml20`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépéseket, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://supermood.co/app/#!/loginv2`

1. Kattintson a **Mentés** gombra.

1. A felderítő alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a felderítő alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| ------|
    | firstName | User. givenName |
    | lastName | felhasználó. vezetéknév |

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a főhangulathoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **főhangulat** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-supermood-sso"></a>A felderített egyszeri bejelentkezés konfigurálása

1. Nyissa meg a Supermood.co felügyeleti paneljét biztonsági rendszergazdaként.

1. Kattintson a  fiókom (bal alsó) és az **egyszeri bejelentkezés (SSO)** elemre.

    ![A tanúsítvány egyetlen](./media/supermood-tutorial/tutorial_supermood_single.png)

1. Az **saml 2,0-konfigurációkon** kattintson az **SAML 2,0-konfiguráció hozzáadása egy E-mail tartományhoz** elemre.

    ![A tanúsítvány hozzáadása](./media/supermood-tutorial/tutorial_supermood_add.png)

1. Az **SAML 2,0-konfiguráció hozzáadása egy e-mail tartományhoz**. szakaszban hajtsa végre a következő lépéseket:

    ![A tanúsítvány SAML](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Az identitás-szolgáltató szövegmezőbe írja be a tartományát az **e-mail-tartományba** .

    b. A **metaadatok URL-címének használata** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portalból másolt.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="create-supermood-test-user"></a>Saját Mood-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a főhangulatban. A felépítés támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a főhangulatban, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [csapattal](mailto:hello@supermood.fr).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a főhangulat csempére kattint, automatikusan be kell jelentkeznie arra a főalkalmazásba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A My Mood kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)