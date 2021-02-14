---
title: 'Oktatóanyag: Azure Active Directory integráció a beli jfrog Artifactory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és beli jfrog Artifactory között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: f0fafa5c0cc2e0b1bf0f4e11db3265824feb5296
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374701"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Oktatóanyag: a beli jfrog-Artifactory integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a beli jfrog-Artifactory a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a beli jfrog Artifactory, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a beli jfrog Artifactory.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a beli jfrog Artifactory az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Beli jfrog Artifactory egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A beli jfrog Artifactory támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A beli jfrog Artifactory **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-jfrog-artifactory-from-the-gallery"></a>Beli jfrog-Artifactory hozzáadása a gyűjteményből

A beli jfrog-Artifactory Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a beli jfrog-Artifactory a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **beli jfrog Artifactory** kifejezést a keresőmezőbe.
1. Válassza ki a **beli jfrog Artifactory** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a beli jfrog Artifactory egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a beli jfrog Artifactory.

Az Azure AD SSO beli jfrog-Artifactory való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[Beli jfrog-ARTIFACTORY SSO konfigurálása](#configure-jfrog-artifactory-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Beli jfrog-Artifactory-teszt felhasználó létrehozása](#create-jfrog-artifactory-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon beli jfrog-Artifactory rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **beli jfrog Artifactory** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<servername>.jfrog.io`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    - Artifactory 6. x esetén: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Artifactory 7. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    - Artifactory 6. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Artifactory 7. x esetén: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a beli jfrog Artifactory-ügyfélszolgálati [csapatához](https://support.jfrog.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A beli jfrog Artifactory alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![A képernyőképen a felhasználói attribútumok láthatók a szerkesztési vezérlővel.](common/edit-attribute.png)

1. A fentiek mellett a beli jfrog Artifactory számos további attribútumot vár, amelyeket vissza kell adni az SAML-válaszban. A **csoport jogcímek (előzetes verzió)** párbeszédpanel **felhasználói attribútumok & jogcímek** szakaszában hajtsa végre a következő lépéseket:

    a. Kattintson a **kérelemben visszaadott csoportok** melletti **tollra** .

    ![Képernyőfelvétel: felhasználói attribútumok & jogcímek kijelölése a szerkesztési ikonnal.](./media/jfrog-artifactory-tutorial/config04.png)

    ![Képernyőfelvétel: a Group jogcímek szakasz az összes kiválasztott csoporttal együtt.](./media/jfrog-artifactory-tutorial/config05.png)

    b. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

    c. Kattintson a **Mentés** gombra.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítványt (Base64)** , majd válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Konfigurálja a Artifactory (SAML-szolgáltató nevét) az "azonosító" mezővel (lásd: 4. lépés). A **beli jfrog Artifactory beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

   - Artifactory 6. x esetén: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Artifactory 7. x esetén: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-jfrog-artifactory-sso"></a>Beli jfrog Artifactory SSO konfigurálása

Minden, amire szüksége lehet az egyszeri bejelentkezés konfigurálásához a **beli jfrog Artifactory** oldalon, a Artifactory rendszergazdája konfigurálható az SAML configugration képernyőjén.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a beli jfrog Artifactory.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **beli jfrog Artifactory** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-jfrog-artifactory-test-user"></a>Beli jfrog Artifactory-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a beli jfrog Artifactory. A beli jfrog Artifactory támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a beli jfrog Artifactory, a hitelesítés után létrejön egy új.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a beli jfrog Artifactory csempére kattint, automatikusan be kell jelentkeznie arra a beli jfrog-Artifactory, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)