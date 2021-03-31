---
title: 'Oktatóanyag: Azure Active Directory integráció a Freedcamp-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freedcamp között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451492"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Oktatóanyag: a Freedcamp és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freedcamp a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Freedcamp-t az Azure AD-vel, a következőket teheti:

* A Freedcamp-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Freedcamp az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Freedcamp egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Freedcamp **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-freedcamp-from-the-gallery"></a>Freedcamp hozzáadása a gyűjteményből

A Freedcamp Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freedcamp a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Freedcamp** kifejezést a keresőmezőbe.
1. Válassza ki a **Freedcamp** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Freedcamp a **Britta Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freedcamp-ben.

Az Azure AD SSO és a Freedcamp konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a Freedcamp](#configure-freedcamp)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. **[Hozzon létre Freedcamp-tesztelési felhasználót](#create-freedcamp-test-user)** , hogy a Britta Simon a Freedcamp egy, a felhasználó Azure ad-képviseletéhez kapcsolódó partnere legyen.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Freedcamp** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A felhasználók a saját Ügyfélkörük alapján is megadhatják az URL-értékeket, és előfordulhat, hogy nem feltétlenül a minta, hanem az `freedcamp.com` alkalmazás példányára jellemző bármely ügyfél-tartományra érvényes értéket adhatnak meg. Emellett az URL-mintákkal kapcsolatos további információkért forduljon a Freedcamp ügyfélszolgálati [csapatához](mailto:devops@freedcamp.com) .

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Freedcamp beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp konfigurálása

1. A Freedcamp belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés Freedcamp** gombra a Freedcamp alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a Freedcamp való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Freedcamp, nyisson meg egy új böngészőablakot, és jelentkezzen be a Freedcamp vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső sarkában kattintson a **profil** elemre, majd keresse meg a **saját fiók** lehetőséget.

    ![Képernyőkép, amely a "profil" és a "saját fiók" elemet jeleníti meg.](./media/freedcamp-tutorial/config01.png)

5. A menüsáv bal oldalán kattintson az **SSO** elemre, majd az **SSO-kapcsolatok** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely a bal oldali menüsorban és a "saját S O-kapcsolatok" oldalon a beírt értékekkel és a "Küldés" gomb kiválasztásával jelenik meg.](./media/freedcamp-tutorial/config02.png)

    a. A **title (cím** ) mezőbe írja be a címet.

    b. Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    c. A **bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    d. Nyissa meg a Base64 kódolású tanúsítványt a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **tanúsítvány** szövegmezőbe.

    e. Kattintson a **Submit (Küldés**) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Freedcamp hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Freedcamp** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-freedcamp-test-user"></a>Freedcamp-tesztelési felhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a Freedcamp-be, hogy a Freedcamp-ben legyenek kiépítve. A Freedcamp-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be a Freedcamp biztonsági rendszergazdaként.

2. A lap jobb felső sarkában kattintson a **profil** elemre, majd keresse meg a **rendszer kezelése** lehetőséget.

    ![Freedcamp-konfiguráció](./media/freedcamp-tutorial/config03.png)

3. A rendszer kezelése lap jobb oldalán hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a "felhasználók hozzáadása vagy meghívása" gomb kiválasztásakor megjelenik az "E-mail" mező, és a "felhasználó hozzáadása" gomb van kiválasztva.](./media/freedcamp-tutorial/config04.png)

    a. Kattintson a **felhasználók hozzáadása vagy meghívása** elemre.

    b. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail-címet `Brittasimon@contoso.com` .

    c. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Freedcamp csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Freedcamp, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)