---
title: 'Oktatóanyag: Azure Active Directory-integráció a Wanda RADAR rendszergazdájával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Wanda RADAR-rendszergazda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 383b49a7363fdca9327d419f5bed092d489a95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951810"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Oktatóanyag: a Wander RADAR-rendszergazda integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Wanda RADAR-rendszergazdáját Azure Active Directory (Azure AD) használatával. Ha a Wanda RADAR-rendszergazdáját az Azure AD-be integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Wanda RADAR-rendszergazdához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókokkal a bolyongó RADAR-Rendszergazdába.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Bolyongás RADAR rendszergazdai egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Wanda RADAR rendszergazdája támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t
* A Wander RADAR-rendszergazda konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>A Wander RADAR-rendszergazda hozzáadása a katalógusból

A Wander RADAR-rendszergazda az Azure AD-be való integrálásának konfigurálásához fel kell vennie a Wanda RADAR-rendszergazdát a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Bolyongás radar-rendszergazda** kifejezést a keresőmezőbe.
1. Válassza ki a **Wanda radar-rendszergazda** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Bolyongis RADAR rendszergazdájával egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Wanda RADAR-Rendszergazdában.

Az Azure AD SSO és a Wander RADAR-rendszergazda konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
   * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
   * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Bolyongás radar rendszergazdai SSO konfigurálása](#configure-wandera-radar-admin-sso)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
   * A **[Wanda radar rendszergazdai tesztelési felhasználójának létrehozása](#create-wandera-radar-admin-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-beli, a WANDa radar-rendszergazdában található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Wanda radar rendszergazdai** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez vegye fel a kapcsolatot a [Wanda radar rendszergazdai](https://www.wandera.com/about-wandera/contact/#supportsection) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti. Gondosan cserélje le a <tenant id> fenti URL-cím részét a Wander-fiókban található **Beállítások**  >  **Adminisztráció**  >  **egyszeri bejelentkezési** oldalán látható bérlői azonosítóra.


1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon a beállítások szerkesztéséhez kattintson a Szerkesztés/toll ikonra az SAML- **aláíró tanúsítványhoz** .

    ![Aláírási beállítás](common/signing-option.png)

    1. Válassza az **aláírás lehetőséget** az **SAML-válasz és az állítás aláírásához**.

    1. Válassza az **aláírási algoritmust** **SHA-256ként**.

1. A **Bolyongás radar-rendszergazda beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Wanda RADAR-rendszergazdához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Bolyongás radar-rendszergazda** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-wandera-radar-admin-sso"></a>A Bolyongás RADAR rendszergazdai egyszeri bejelentkezésének konfigurálása

1. A Bolyongás RADAR-rendszergazdán belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítás Bolyongás radar-rendszergazda** lehetőségre a Wanda radar rendszergazdai alkalmazásának irányításához. Itt adja meg a rendszergazdai hitelesítő adatokat a Wanda RADAR-rendszergazdának való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-4-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Wanda RADAR-rendszergazdáját, nyisson meg egy új böngészőablakot, és jelentkezzen be a Wanda RADAR rendszergazda vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső sarkában kattintson a **Beállítások**  >  **felügyelet**  >  **egyszeri bejelentkezés** elemre, majd jelölje be a **SAML 2,0 engedélyezése** a következő lépések végrehajtásához lehetőséget.

    ![A Wanda RADAR rendszergazdai konfigurációja](./media/wandera-tutorial/config01.png)

    a. Kattintson **vagy manuálisan adja meg a kötelező mezőket**.

    b. A **identitásszolgáltató EntityId** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    c. Nyissa meg az összevonási metaadatok XML-fájlját a Jegyzettömbben, másolja a tartalmát, és illessze be a **identitásszolgáltató Public X. 509 tanúsítvány** szövegmezőbe.

    d. Kattintson a **Mentés** gombra.

### <a name="create-wandera-radar-admin-test-user"></a>A Wanda RADAR rendszergazdai tesztelési felhasználójának létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Wanda RADAR-Rendszergazdában. a Wanda-beli [radar-felügyeleti támogatási csapattal](https://www.wandera.com/about-wandera/contact/#supportsection) együtt veheti fel a felhasználókat a Wanda radar felügyeleti platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Wanda RADAR-adminisztrátor csempére kattint, automatikusan be kell jelentkeznie a Wanda RADAR-Rendszergazdába, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
