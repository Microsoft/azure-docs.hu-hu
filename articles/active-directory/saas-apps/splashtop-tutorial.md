---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Splashtop | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Splashtop között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Splashtop

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Splashtop a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Splashtop-t az Azure AD-vel, a következőket teheti:

* A Splashtop-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Splashtop az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Splashtop egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Splashtop támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Splashtop konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Splashtop hozzáadása a gyűjteményből

A Splashtop Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Splashtop a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Splashtop** kifejezést a keresőmezőbe.
1. Válassza ki a **Splashtop** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Splashtop

Konfigurálja és tesztelje az Azure AD SSO-t a Splashtop a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Splashtop-ben.

Az Azure AD SSO és a Splashtop konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SPLASHTOP SSO konfigurálása](#configure-splashtop-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Splashtop-teszt felhasználót](#create-splashtop-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Splashtop rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Splashtop** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://my.splashtop.com/login/sso`

1. A Splashtop alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, míg a **NameIdentifier** a **User. userPrincipalName** leképezéssel van leképezve. A TicketManager alkalmazás a **NameIdentifier** a **User. mail** használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Splashtop beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Splashtop.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Splashtop** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-splashtop-sso"></a>Splashtop SSO konfigurálása

Ebben a szakaszban egy új SSO-módszert kell alkalmaznia a [Splashtop web Portalról](https://my.splashtop.com/login).
1. A Splashtop web Portalon lépjen a **fiók adatai**  /  **csapat** lapra, és görgessen le az **egyszeri bejelentkezés** szakasz kereséséhez. Ezután kattintson **az alkalmaz új SSO-metódusra** elemre.

    ![Képernyőfelvétel: az egyszeri bejelentkezési oldal, amelyen az új S O-metódus alkalmazása lehetőség kiválasztható.](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Az alkalmazás ablakban adjon meg egy **egyszeri bejelentkezési nevet**. Például: új Azure, válassza az **Azure** -t a identitásszolgáltató típusaként, és szúrja be a **bejelentkezési URL-címet** és az **Azure ad-azonosítót** a Splashtop alkalmazásból a Azure Portal.

    ![A képernyőfelvétel megjeleníti az S S S O-metódusok érvényes oldalát, ahol megadhatja a nevet és egyéb információkat.](media/splashtop-tutorial/azure-sso-1.png)

1. A tanúsítvány adatainak megtekintéséhez kattintson a jobb gombbal a Splashtop alkalmazásból letöltött tanúsítvány fájlra Azure Portal, szerkessze a jegyzettömböt, majd másolja ki a tartalmat, illessze be a **tanúsítvány letöltése (Base64)** mezőbe.

    ![Képernyőfelvétel: a tanúsítványfájl kiválasztásának és a Jegyzettömbvel való megnyitásának megjelenítése.](media/splashtop-tutorial/cert-1.png)
    ![Képernyőfelvétel: a tanúsítványfájl tartalmát jeleníti meg.](media/splashtop-tutorial/cert-2.png)
    ![Képernyőfelvétel: a tanúsítvány letöltése szövegmező.](media/splashtop-tutorial/azure-sso-2.png)

1. Ennyi az egész! Kattintson a **Save (Mentés** ) gombra, és a Splashtop SSO ellenőrzési csapat felveszi Önnel a kapcsolatot az ellenőrzési adatokkal, majd aktiválja az egyszeri bejelentkezés módját.

### <a name="create-splashtop-test-user"></a>Splashtop-tesztelési felhasználó létrehozása

1. Az SSO-módszer aktiválása után ellenőrizze az újonnan létrehozott egyszeri bejelentkezéses módszert az **egyszeri bejelentkezés** szakaszban való engedélyezéséhez.

    ![Képernyőfelvétel: az egyszeri bejelentkezési oldal, amelyen engedélyezheti az új metódust.](media/splashtop-tutorial/enable.png)

1. Meghívja a teszt felhasználót, például a `B.Simon@contoso.com` Splashtop csapata számára az újonnan létrehozott SSO-metódussal.

    ![Képernyőfelvétel: a felhasználók meghívása oldal, ahol kiválaszthatja az új metódust.](media/splashtop-tutorial/invite.png)

1. A meglévő Splashtop-fiókokat egy SSO-fiókra is módosíthatja, lásd: [utasítások](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Ennyi az egész! Az SSO-fiók használatával bejelentkezhet a Splashtop web Portal vagy a Splashtop Business alkalmazásba.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Splashtop csempére kattint, automatikusan be kell jelentkeznie arra a Splashtop, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Splashtop kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Splashtop és a speciális láthatóság és vezérlők elleni védelem](/cloud-app-security/proxy-intro-aad)