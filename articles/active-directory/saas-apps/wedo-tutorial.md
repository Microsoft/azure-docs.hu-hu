---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a WEDO | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és WEDO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 529c4e6433d16f9d70530ba516b5ec1426806984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a WEDO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a WEDO a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az WEDO-t az Azure AD-vel, a következőket teheti:

* A WEDO-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a WEDO az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* WEDO egyszeri bejelentkezés (SSO) engedélyezett előfizetése. Az SSO-előfizetés beszerzéséhez lépjen kapcsolatba a [WeDo](mailto:info@wedo.swiss) ügyfélszolgálatával.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A WEDO támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

* [A WeDo konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>WEDO hozzáadása a gyűjteményből

A WEDO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a WEDO a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **WeDo** kifejezést a keresőmezőbe.
1. Válassza ki a **WeDo** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a WEDO

Konfigurálja és tesztelje az Azure AD SSO-t a WEDO a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a WEDO-ben.

Az Azure AD SSO és a WEDO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[WeDo SSO konfigurálása](#configure-wedo-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre WeDo-teszt felhasználót](#create-wedo-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-WeDo rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **WeDo** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a WeDo ügyfélszolgálati [csapatához](mailto:info@wedo.swiss) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A WEDO alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    | Name | Forrás attribútum|
    | ------------ | --------- |
    | e-mail | user.email |
    | firstName | felhasználó. firstName |
    | lastName | User. lasttName |
    | userName (Felhasználónév) | User. userName |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **WeDo beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a WEDO.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **WeDo** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-wedo-sso"></a>WEDO SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a WEDO-ben.

1. Jelentkezzen be a [WeDo](https://login.wedo.swiss/). **Rendszergazdai szerepkörrel** kell rendelkeznie.
1. A profil beállításai területen válassza a **hálózati beállítások** szakaszban a menü **hitelesítés** lehetőséget.
1. Az **SAML hitelesítés** lapon hajtsa végre a következő lépéseket:

   ![SAML-hitelesítés hivatkozása](media/wedo-tutorial/network-security-authentification.png)

   a. **SAML-hitelesítés** engedélyezése.

   b. Válassza az **Identity Provider Metadata (XML)** fület.

   c. Nyissa meg a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** Azure Portal a Jegyzettömbben, és másolja ki a metaadatok XML-fájlját, és illessze be az **X. 509 tanúsítvány** szövegmezőbe.

   d. Kattintson a **Mentés** gombra.

### <a name="create-wedo-test-user"></a>WEDO-tesztelési felhasználó létrehozása

Ebben a szakaszban egy WEDO nevű teszt felhasználót hoz létre a következő néven: Bob Simon. Az információknak meg kell egyezniük az *Azure ad-tesztkörnyezet létrehozásával*.

1. A WEDO-beli profil beállítása területen válassza  a felhasználók *hálózati beállításokból* lehetőséget.
1. Kattintson a **felhasználó hozzáadása** elemre.
1. A felhasználó hozzáadása felugró ablakban töltse ki a felhasználó adatait

    a. Keresztnév `B` .

    b. Vezetéknév `Simon` .

    c. Adja meg az e-mailt `username@companydomain.extension` . Például: `B.Simon@contoso.com`. A vállalat rövid nevével megegyező tartományba tartozó e-mail-cím megadása kötelező.

    d. Felhasználó típusa `User`

    e. Kattintson a **felhasználó létrehozása** gombra.

    f. A *csoportok kiválasztása* lapon kattintson a **Mentés** gombra.

    :  A *felhasználó meghívása* lapon kattintson az **Igen** gombra.

1. A felhasználó ellenőrzése e-mailben kapott hivatkozás használatával

> [!NOTE]
> Ha hamis felhasználót szeretne létrehozni (a fenti e-mail-cím nem létezik a hálózaton), forduljon a [támogatási szolgálathoz](mailto:info@wedo.swiss) , és ellenőrizze a felhasználót *.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a WEDO csempére kattint, automatikusan be kell jelentkeznie arra a WEDO, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A WEDO kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A WEDO és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)