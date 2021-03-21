---
title: 'Oktatóanyag: Azure Active Directory integráció a AlertOps-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AlertOps között.
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
ms.openlocfilehash: 094b1a029c8e10abce3ca282de0cc99be34d313a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92318862"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Oktatóanyag: a AlertOps és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AlertOps a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AlertOps-t az Azure AD-vel, a következőket teheti:

* A AlertOps-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a AlertOps az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AlertOps egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A AlertOps **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-alertops-from-the-gallery"></a>AlertOps hozzáadása a gyűjteményből

A AlertOps Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a AlertOps a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **AlertOps** kifejezést a keresőmezőbe.
1. Válassza ki a **AlertOps** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a AlertOps a **Britta Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a AlertOps-ben.

Az Azure AD SSO és a AlertOps konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a AlertOps](#configure-alertops)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. **[Hozzon létre AlertOps-tesztelési felhasználót](#create-alertops-test-user)** , hogy a Britta Simon a AlertOps egy, a felhasználó Azure ad-képviseletéhez kapcsolódó partnere legyen.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **AlertOps** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.alertops.com`

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a AlertOps ügyfélszolgálati [csapatához](mailto:support@alertops.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **AlertOps beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps konfigurálása

1. A AlertOps belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés AlertOps** gombra a AlertOps alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a AlertOps való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a AlertOps, nyisson meg egy új böngészőablakot, és jelentkezzen be a AlertOps vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **Fiókbeállítások** lehetőségre a bal oldali navigációs panelen.

    ![Képernyőfelvétel: a AlertOps menü, a Fiókbeállítások neve.](./media/alertops-tutorial/configure1.png)

5. Az **előfizetés beállításai** lapon válassza az **SSO** lehetőséget, és hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az előfizetési beállítások ablak a S S O-hez az ebben a lépésben ismertetett értékekkel.](./media/alertops-tutorial/configure2.png)

    a. Jelölje be **az egyszeri bejelentkezés (SSO) használata** jelölőnégyzetet.

    b. A legördülő listából válassza az **Azure Active Directory** **SSO-szolgáltatóként** lehetőséget.

    c. A **kiállító URL-címe** szövegmezőben használja az azonosító értékét, amelyet a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában használt.

    d. Az **SAML-végpont URL-címe** szövegmezőbe illessze be a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    e. Az **slo-végpont URL-címe** szövegmezőbe illessze be a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    f. Válassza  ki a sha256 **SAML-aláírási algoritmusként** a legördülő listából.

    : Nyissa meg a letöltött tanúsítvány (Base64) fájlt a Jegyzettömbben. Másolja a vágólapra a tartalmát, majd illessze be az X. 509 tanúsítvány szövegmezőbe.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a AlertOps hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **AlertOps** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-alertops-test-user"></a>AlertOps-tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a AlertOps vállalati webhelyre rendszergazdaként.

2. A bal oldali navigációs panelen kattintson a **felhasználók** elemre.

    ![A képernyőképen a AlertOps menüt láthatja a felhasználók számára.](./media/alertops-tutorial/user1.png)

3. Válassza a **felhasználó hozzáadása** elemet.

    ![Képernyőfelvétel: a felhasználók ablak megjelenítése a felhasználó hozzáadása gombbal.](./media/alertops-tutorial/user2.png)

4. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A képernyőképen a felhasználók hozzáadása panel jelenik meg, amely az ebben a lépésben leírt értékekkel van megadva.](./media/alertops-tutorial/user3.png)

    a. A **bejelentkezési Felhasználónév** szövegmezőbe írja be a felhasználó felhasználónevét, például a **Brittasimon** nevet.

    b. A **hivatalos e-mail** szövegmezőbe írja be annak a felhasználónak az e-mail címét, mint a **Brittasimon \@ contoso.com**.

    c. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta** nevet.

    d. A **vezetéknév** szövegmezőbe írja be a felhasználó utónevét, például **Simon** nevet.

    e. Válassza ki a **típus** értéket a legördülő listából a saját szervezete szerint.

    f. Válassza ki a felhasználó **szerepkörét** a legördülő listából a saját szervezete számára.

    : Válassza a **Hozzáadás** lehetőséget.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a AlertOps csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a AlertOps, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)