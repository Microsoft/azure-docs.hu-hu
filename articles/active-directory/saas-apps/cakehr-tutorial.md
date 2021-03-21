---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CakeHR | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és CakeHR között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 08e028ba057ad57f3d600bc59bf7595c0b1d354c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a CakeHR

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CakeHR a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az CakeHR-t az Azure AD-vel, a következőket teheti:

* A CakeHR-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a CakeHR az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* CakeHR egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A CakeHR támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-cakehr-from-the-gallery"></a>CakeHR hozzáadása a gyűjteményből

A CakeHR Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a CakeHR a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CakeHR** kifejezést a keresőmezőbe.
1. Válassza ki a **CakeHR** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a CakeHR

Konfigurálja és tesztelje az Azure AD SSO-t a CakeHR a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a CakeHR-ben.

Az Azure AD SSO és a CakeHR konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[CAKEHR SSO konfigurálása](#configure-cakehr-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre CakeHR-teszt felhasználót](#create-cakehr-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-CakeHR rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **CakeHR** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<yourcakedomain>.cake.hr/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel és a válasz URL-címével. Az értékek lekéréséhez forduljon a CakeHR ügyfélszolgálati [csapatához](mailto:info@cake.hr) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat** értékét, és mentse a Jegyzettömbbe.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **CakeHR beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a CakeHR.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **CakeHR** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cakehr-sso"></a>CakeHR SSO konfigurálása

1. A CakeHR belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **CakeHR beállítása** elemre, majd a CakeHR alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a CakeHR való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a CakeHR, nyisson meg egy új böngészőablakot, és jelentkezzen be a CakeHR vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. A lap jobb felső sarkában kattintson a **profil** elemre, majd navigáljon a **Beállítások** pontra.

    ![Képernyőfelvétel: a kiválasztott beállításokkal rendelkező profilt jeleníti meg.](./media/cakehr-tutorial/config01.png)

1. A menüsáv bal oldalán kattintson az **integráció**  >  **SAML SSO** elemre, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a beállítások ablaktábla, ahol elvégezheti ezeket a lépéseket.](./media/cakehr-tutorial/config02.png)

    a. Az **entitás-azonosító** szövegmezőbe írja be a következőt: `cake.hr` .

    b. A **hitelesítés URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    c. A **kulcs ujjlenyomata (SHA1 formátuma)** szövegmezőbe illessze be a Azure Portalból másolt **ujjlenyomat** -értéket.

    d. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.

    e. Kattintson a **Mentés** gombra.

### <a name="create-cakehr-test-user"></a>CakeHR-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a CakeHR, a CakeHR kell kiépíteni őket. A CakeHR-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a CakeHR biztonsági rendszergazdaként.

2. A menüsáv bal oldalán kattintson a **vállalati**  >  **Hozzáadás** elemre.

    ![Képernyőfelvétel: a CakeHR a VÁLLALATtal és a kijelölt hozzáadása.](./media/cakehr-tutorial/config03.png)

3. Az **új alkalmazott hozzáadása** felugró ablakban végezze el a következő lépéseket:

     ![Képernyőfelvétel: új alkalmazott hozzáadása, ahol elvégezheti ezeket a lépéseket.](./media/cakehr-tutorial/config04.png)

    a. A **teljes név** szövegmezőbe írja be a felhasználó nevét, például: B. Simon.

    b. A **munkahelyi e-mailek** szövegmezőbe írja be a felhasználóhoz hasonló e-mail-címet `B.Simon@contoso.com` .

    c. Kattintson a **fiók létrehozása** lehetőségre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a CakeHR csempére kattint, automatikusan be kell jelentkeznie arra a CakeHR, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A CakeHR kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)