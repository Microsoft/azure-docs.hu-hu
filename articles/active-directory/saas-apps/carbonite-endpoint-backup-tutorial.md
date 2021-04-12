---
title: 'Oktatóanyag: Azure Active Directory integráció a Carbonite Endpoint Backup szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Carbonite-végpont biztonsági mentése között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456483"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Oktatóanyag: a Carbonite-végpontok biztonsági mentésének integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Carbonite-végpontok biztonsági mentését Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Carbonite-végpont biztonsági mentését, a következőket teheti:

* A Carbonite-végpont biztonsági mentéséhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Carbonite Endpoint Backup szolgáltatásba az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Carbonite Endpoint Backup egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Carbonite Endpoint Backup támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>A Carbonite-végpont biztonsági mentésének hozzáadása a katalógusból

A Carbonite-végpont biztonsági mentésének Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Carbonite-végpont biztonsági mentését a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Carbonite Endpoint Backup** kifejezést a keresőmezőbe.
1. Válassza ki a **Carbonite-végpont biztonsági mentését** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Carbonite Endpoint Backup segítségével egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Carbonite Endpoint Backup szolgáltatásban.

Az Azure AD SSO és a Carbonite-végpont biztonsági mentésének konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[Carbonite-végpont biztonsági mentésének SSO](#configure-carbonite-endpoint-backup-sso)** -beállítása – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. A **[Carbonite-végpont biztonsági mentési tesztelési felhasználójának létrehozása](#create-carbonite-endpoint-backup-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó, B. Simon-beli, a Carbonite-végpont biztonsági másolatában található
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/), a **Carbonite Endpoint Backup** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az alábbi URL-címek egyikét:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. A **Válasz URL-címe** szövegmezőbe írja be az alábbi URL-címek egyikét:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-címek egyikét:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Carbonite-végpont biztonsági mentésének beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>A Carbonite-végpont biztonsági mentési egyszeri bejelentkezésének konfigurálása

1. A Carbonite-végpont biztonsági másolatán belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Carbonite Endpoint Backup beállítása** gombra a Carbonite-végpont biztonsági mentési alkalmazásának irányításához. Itt adja meg a rendszergazdai hitelesítő adatokat a Carbonite Endpoint Backup szolgáltatásba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Carbonite-végpont biztonsági mentését, nyisson meg egy új böngészőablakot, és jelentkezzen be a Carbonite-végpont biztonsági mentési céges webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **vállalatra** a bal oldali ablaktáblán.

    ![Képernyőfelvétel: a kiválasztott vállalatnál látható Carbonite-végpont.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Kattintson az **egyszeri bejelentkezés** lehetőségre.

    ![Képernyőfelvétel: a vállalat egyszeri bejelentkezéssel van kiválasztva.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Kattintson az **Engedélyezés** elemre, majd kattintson a **beállítások szerkesztése** elemre a konfiguráláshoz.

    ![A képernyőképen az egyszeri bejelentkezés lap jelenik meg, amely lehetővé teszi az engedélyezési és a szerkesztési beállítások kihívását.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Az **egyszeri bejelentkezési** beállítások oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az egyszeri bejelentkezés lap, amely az ebben a lépésben ismertetett információkat tartalmazza.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Az **identitás-szolgáltató neve** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    1. Az **Identity Provider URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    1. Kattintson a **fájl kiválasztása** lehetőségre a letöltött **tanúsítvány (Base64)** fájljának a Azure Portal való feltöltéséhez.

    1. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezést használja a Carbonite-végpont biztonsági mentéshez való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza ki a **Carbonite-végpont biztonsági mentése** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite-végpont biztonsági mentési tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a Carbonite-végpont biztonsági mentési céges webhelyre rendszergazdaként.

1. Kattintson a bal oldali ablaktáblán a **felhasználók** elemre, majd kattintson a **felhasználó hozzáadása** elemre.

    ![Képernyőfelvétel: a Carbonite-végpont lapja a felhasználók és a kiválasztott felhasználók hozzáadásával.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. A **felhasználó hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználó hozzáadása lap, amelyen elvégezheti az itt ismertetett lépéseket.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Adja meg a felhasználó **e-mail-címét**, **utónevét** és **vezetéknevét** , és adja meg a szükséges engedélyeket a felhasználónak a szervezeti követelményeknek megfelelően.

    1. Kattintson a **felhasználó hozzáadása** elemre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Carbonite-végpont biztonsági mentési csempére kattint, automatikusan be kell jelentkeznie a Carbonite-végpont biztonsági másolatából, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)