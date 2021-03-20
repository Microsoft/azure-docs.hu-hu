---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Claromentis | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Claromentis között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455901"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Claromentis

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Claromentis a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Claromentis-t az Azure AD-vel, a következőket teheti:

* A Claromentis-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Claromentis az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Claromentis egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Claromentis támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Claromentis **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-claromentis-from-the-gallery"></a>Claromentis hozzáadása a gyűjteményből

A Claromentis Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Claromentis a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Claromentis** kifejezést a keresőmezőbe.
1. Válassza ki a **Claromentis** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Claromentis

Konfigurálja és tesztelje az Azure AD SSO-t a Claromentis a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Claromentis-ben.

Az Azure AD SSO és a Claromentis konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[CLAROMENTIS SSO konfigurálása](#configure-claromentis-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Claromentis-teszt felhasználót](#create-claromentis-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Claromentis rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Claromentis** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az azonosító értékét a szervezete által támasztott követelménynek megfelelően.

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel, amelyet később a turorial ismertet.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Claromentis beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Claromentis.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Claromentis** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-claromentis-sso"></a>Claromentis SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Claromentis webhelyre.

1. Kattintson az **Alkalmazások ikonra** , és válassza a **rendszergazda** elemet.

    ![Képernyőfelvétel: a Claromentis webhely, amelyen a rendszergazda van kiválasztva.](./media/claromentis-tutorial/config1.png)

1. Válassza az **Egyéni bejelentkezési kezelő** fület.

    ![Képernyőfelvétel: az adminisztrációs oldal, amelyen az egyéni bejelentkezési kezelő van kiválasztva.](./media/claromentis-tutorial/config2.png)

1. Válassza az **SAML-konfiguráció** lehetőséget.

    ![A képernyőfelvételen az SAML konfigurációs oldala látható.](./media/claromentis-tutorial/config3.png)

1. Az **SAML-konfiguráció** lapon görgessen le a **konfiguráció** szakaszhoz, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az oldal konfigurációs szakasza, amelyen megadhatja az ebben a lépésben ismertetett információt.](./media/claromentis-tutorial/config4.png)

    a. A **technikai kapcsolattartó neve** szövegmezőbe írja be a technikai kapcsolattartó személy nevét.

    b. A **technikai kapcsolattartási e-mail** szövegmezőbe írja be a technikai kapcsolattartó e-mail-címét.

    c. Adja meg a jelszót az **Auth admin password** szövegmezőben.

1. Görgessen le a **hitelesítési forrásokhoz** , és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az Auth sources (hitelesítési források) szakasz, ahol megadhatja az ebben a lépésben ismertetett információkat.](./media/claromentis-tutorial/config5.png)

    a. A **identitásszolgáltató** szövegmezőbe írja be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    b. Az **entitás-azonosító** szövegmezőbe írja be az entitás azonosítójának értékét.

    c. Töltse fel az **összevonási metaadatok XML-** fájlját, amelyet a Azure Portal letöltött.

    d. Kattintson a **Mentés** gombra.

1. Ekkor megjelenik az összes URL-cím az **SAML-konfiguráció** szakasz **Identity Provider** szakaszában.

    ![A képernyőképen látható az U R ls-val feltöltött Identity Provider oldal.](./media/claromentis-tutorial/config6.png)

    a. Másolja az **azonosító (Entity ID)** értéket, illessze be ezt az értéket a Azure Portal **alapszintű SAML-konfiguráció** szakaszának **azonosító** szövegmezőbe.

    b. Másolja a **Válasz URL-címe** értéket, illessze be ezt az értéket a **Válasz URL-címe** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    c. Másolja a **bejelentkezési URL-cím** értéket, illessze be ezt az értéket a **bejelentkezési URL** -szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

### <a name="create-claromentis-test-user"></a>Claromentis-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Claromentis-ben. A Claromentis támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Claromentis-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Claromentis csempére kattint, automatikusan be kell jelentkeznie arra a Claromentis, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Claromentis kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)