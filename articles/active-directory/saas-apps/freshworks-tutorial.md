---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshworks | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freshworks között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: c953297d4e66f737250451b9a5f42ce7f45dd2e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821254"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshworks

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freshworks a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Freshworks-t az Azure AD-vel, a következőket teheti:

* A Freshworks-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Freshworks az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Freshworks egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Freshworks támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-freshworks-from-the-gallery"></a>Freshworks hozzáadása a gyűjteményből

A Freshworks Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshworks a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Freshworks** kifejezést a keresőmezőbe.
1. Válassza ki a **Freshworks** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Freshworks

Konfigurálja és tesztelje az Azure AD SSO-t a Freshworks a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freshworks-ben.

Az Azure AD SSO és a Freshworks konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[FRESHWORKS SSO konfigurálása](#configure-freshworks-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Freshworks-teszt felhasználót](#create-freshworks-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Freshworks rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Freshworks** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.freshworks.com/login`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a Freshworks ügyfélszolgálati [csapatához](mailto:support@freshworks.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Ha módosítani szeretné az **aláírási**   beállításokat a követelmény szerint, kattintson a **Szerkesztés**   gombra az **SAML aláíró tanúsítvány**   párbeszédpanel megnyitásához.

     ![image](common/edit-certificate.png)

     ![A "szerkesztés" gomb kiválasztásával megjelenített "S A M L aláíró tanúsítvány" párbeszédpanelt megjelenítő képernyőkép.](./media/freshworks-tutorial/response.png)

    a. Válassza az **SAML-válasz** aláírása az **aláírásként lehetőséget**.

    b. Kattintson a **Mentés** gombra.

1. A **Freshworks beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Freshworks.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Freshworks**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-freshworks-sso"></a>Freshworks SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a Freshworks vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

2. A menü bal oldalán kattintson a **Biztonság**   ikonra, majd az **egyszeri bejelentkezés** lehetőségre, és válassza az **SAML SSO** lehetőséget a **hitelesítési módszerek**alatt.

    ![Képernyőkép: "a biztonsági hitelesítési módszerek" szakasz "egyszeri bejelentkezés" beállítással és "S A M L S O" kiválasztásával.](./media/freshworks-tutorial/configure01.png)

3. Az **egyszeri bejelentkezés** szakaszban hajtsa végre a következő lépéseket:

    ![Freshworks-konfiguráció](./media/freshworks-tutorial/configure02.png)

    a. A **Másolás** gombra kattintva másolja a szolgáltatói **(SP) entitás azonosítóját** a példányra, majd illessze be az **azonosítót (Entity ID)** szövegmezőbe az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.

    b. A identitásszolgáltató szövegmezőben **megadott entitás-azonosítóban** illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    c. Az **SAML SSO URL-cím** szövegmezőbe illessze be a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    d. Nyissa meg a Base64 kódolású tanúsítványt a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **biztonsági tanúsítvány** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-freshworks-test-user"></a>Freshworks-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Freshworks-ben. A [Freshworks ügyfél-támogatási csapattal](mailto:support@freshworks.com) együttműködve veheti fel a felhasználókat a Freshworks-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Freshworks csempére kattint, automatikusan be kell jelentkeznie arra a Freshworks, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Freshworks kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

