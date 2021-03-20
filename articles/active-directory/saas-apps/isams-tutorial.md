---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az iSAMs szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az iSAM között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: e994e39cc916c51e3ad6b00015d710bb422cccc9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-isams"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az iSAMs szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az iSAM-ket Azure Active Directory (Azure AD) használatával. Ha az iSAM-t az Azure AD-vel integrálja, a következőket teheti:

* Az olyan Azure AD-beli vezérlők, akik hozzáférése van az iSAM-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az iSAM-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* az iSAM egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.


* az iSAMs támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* Az iSAM konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-isams-from-the-gallery"></a>Az iSAM hozzáadása a gyűjteményből

Az iSAM-alkalmazások Azure AD-be való integrálásának konfigurálásához az iSAM-ket hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **isams** kifejezést a keresőmezőbe.
1. Válassza az **isams** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-isams"></a>Az Azure AD SSO konfigurálása és tesztelése az iSAM-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az iSAMs használatával egy **B. Simon** nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az iSAM-ben.

Az Azure AD SSO és az iSAM szolgáltatás konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[ISAM SSO konfigurálása](#configure-isams-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre ISAM-teszt felhasználót](#create-isams-test-user)** – a felhasználó Azure ad-beli képviseletéhez csatolt, B. Simon-beli partnerként.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **ISAM** -alkalmazások integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2/acs`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.isams.cloud/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A következő értékek megszerzéséhez forduljon az [isams ügyfél-támogatási csoporthoz](mailto:support@isams.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure-beli egyszeri bejelentkezést az iSAM-khoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza az **isams** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-isams-sso"></a>Az iSAM egyszeri bejelentkezésének konfigurálása

1. Jelentkezzen be az iSAM-be rendszergazdaként.

1. Navigáljon a vezérlőpulthoz, és nyissa meg a **hitelesítési** modult.
1. A jobb oldali menüben válassza az identitás- **szolgáltatók** elemet.

    ![A képernyőképen Active Directory konfiguráció a kiválasztott Identity Providers beállítással látható.](./media/isams-tutorial/click-identity-provider.png)

1. Válassza a **szolgáltató hozzáadása** lehetőséget

    ![Képernyőfelvétel: az identitás-szolgáltatók kijelölése a szolgáltatók hozzáadása lehetőséggel.](./media/isams-tutorial/add-identity-provider.png)


1. Hajtsa végre a következő lépéseket a következő oldalon:

    ![Képernyőfelvétel: az Identity Providers varázsló, amelyen elvégezheti az ismertetett lépéseket.](./media/isams-tutorial/configure-isams.png)

    a. A **név** szövegmezőben adjon meg egy érvényes nevet, például: `Saml2 Azure` . Ez a név fog megjelenni a bejelentkezési oldalon.

    b. A metaadatok URL-címe mezőbe írja be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.
    
    c. Nyomja le az **Importálás** gombot.
    
    d. Az **engedélyezett ügyfélalkalmazások** szakaszban található **alkalmazások** listában válassza ki az összes olyan ISAM-alkalmazást, amelyet a szolgáltatónak meg kíván jeleníteni a bejelentkezési oldalán.

    e. Kattintson a **mentés & Bezárás** gombra.

### <a name="create-isams-test-user"></a>ISAM-teszt felhasználó létrehozása

1. Jelentkezzen be az iSAM-be rendszergazdaként.

2.  Lépjen a **Vezérlőpult kezdőlap**  ->  **biztonsági & engedélyek**  ->  **felhasználói fiókok**  ->  **felhasználói beállítások & feladatok**  ->  **felhasználói tulajdonságok módosítása** elemre.

    ![Képernyőfelvétel: a felhasználói fiókok lap, amelyen a felhasználói tulajdonságok módosítva beállítás látható.](./media/isams-tutorial/modify-user-properties.png)


3. Az eredményül kapott előugró ablakban válassza ki a **fiók adatai** lapot, és módosítsa az **engedélyt** az újonnan létrehozott identitás-szolgáltatóra.

    ![A képernyőképen a fiók adatai szerepelnek az engedélyezés értékével.](./media/isams-tutorial/account-details.png)

4. Kattintson a **mentés & Bezárás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az iSAMs csempére kattint, automatikusan be kell jelentkeznie azokhoz az iSAMokhoz, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Az iSAM kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)