---
title: 'Oktatóanyag: Azure Active Directory integráció a LaunchDarkly-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és LaunchDarkly között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ddfffb77ca889aea9ff32c7be1ce2e4cb7fc04a7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037602"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Oktatóanyag: Azure Active Directory integráció a LaunchDarkly

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LaunchDarkly a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az LaunchDarkly-t az Azure AD-vel, a következőket teheti:

* A LaunchDarkly-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a LaunchDarkly az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

    > [!NOTE]
    > A LaunchDarkly Azure Active Directory integráció egyirányú. Az integráció konfigurálása után az Azure AD segítségével kezelheti a felhasználókat, az SSO-t és a fiókokat a LaunchDarkly-ben, de **nem** használhatja a LaunchDarkly-t a felhasználók, az egyszeri bejelentkezés és az Azure-beli fiókok kezelésére.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* LaunchDarkly egyszeri bejelentkezésre engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A LaunchDarkly támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* A LaunchDarkly **csak időben támogatja a** felhasználók üzembe helyezését.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-launchdarkly-from-the-gallery"></a>LaunchDarkly hozzáadása a gyűjteményből

A LaunchDarkly Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LaunchDarkly a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **LaunchDarkly** kifejezést a keresőmezőbe.
1. Válassza ki a **LaunchDarkly** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Azure AD SSO konfigurálása és tesztelése a LaunchDarkly-hez

Konfigurálja és tesztelje az Azure AD SSO-t a LaunchDarkly a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a LaunchDarkly-ben.

Az Azure AD SSO és a LaunchDarkly konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[LAUNCHDARKLY SSO konfigurálása](#configure-launchdarkly-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre LaunchDarkly-teszt felhasználót](#create-launchdarkly-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-LaunchDarkly rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **LaunchDarkly** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `app.launchdarkly.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A tényleges válasz URL-címével frissíti az értéket, amelyet később az oktatóanyagban talál. Ha az alkalmazást **identitásszolgáltató** módban kívánja használni, hagyja üresen a **bejelentkezési URL-cím** mezőt, ellenkező esetben nem fogja tudni elindítani a bejelentkezést a **identitásszolgáltató**. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **LaunchDarkly beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a LaunchDarkly.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **LaunchDarkly** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-launchdarkly-sso"></a>LaunchDarkly SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LaunchDarkly vállalati webhelyre rendszergazdaként.

2. Válassza a **Fiókbeállítások** lehetőséget a bal oldali navigációs panelen.

    ![A képernyőképen az éles környezetben kiválasztott Fiókbeállítások elem látható.](./media/launchdarkly-tutorial/configure-1.png)

3. Kattintson a **Biztonság** fülre.

    ![Képernyőfelvétel: a Fiókbeállítások Biztonság lapja.](./media/launchdarkly-tutorial/configure-2.png)

4. Kattintson az **egyszeri bejelentkezés engedélyezése** , majd az **SAML-konfiguráció szerkesztése** elemre.

    ![Képernyőfelvétel: az egyszeri bejelentkezési oldal, amelyen ENGEDÉLYEZheti az S S O-t, és SZERKESZTHETi az SAML-KONFIGURÁCIÓt.](./media/launchdarkly-tutorial/configure-3.png)

5. Az **SAML-konfiguráció szerkesztése** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML-konfiguráció szerkesztése szakasz, ahol az itt ismertetett módosításokat végezheti el.](./media/launchdarkly-tutorial/configure-4.png)

    a. Másolja az **SAML fogyasztói szolgáltatás URL-címét** a példányhoz, és illessze be a válasz URL-szövegmezőbe a **LaunchDarkly tartomány és URL-címek** szakaszban Azure Portal.

    b. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Nyissa meg a letöltött tanúsítványt a Azure Portal a Jegyzettömbben, másolja a tartalmat, majd illessze be az **X. 509-tanúsítvány** mezőbe, vagy közvetlenül feltöltheti a tanúsítványt a **feltöltés** gombra kattintva.

    d. Kattintson a **Mentés** gombra.

### <a name="create-launchdarkly-test-user"></a>LaunchDarkly-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a LaunchDarkly-ben. A LaunchDarkly támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs művelet. Ha egy felhasználó még nem létezik a LaunchDarkly-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a LaunchDarkly, amelyhez be szeretné állítani az egyszeri bejelentkezést.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások LaunchDarkly csempére kattint, automatikusan be kell jelentkeznie arra a LaunchDarkly, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)használatába.

## <a name="next-steps"></a>Következő lépések

A LaunchDarkly konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
