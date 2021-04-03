---
title: 'Oktatóanyag: Azure Active Directory integráció a Workspot-vezérlővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Workspot vezérlőhöz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 65e424c6275f9990a5d5a42a54bc4131300a45bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895035"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Oktatóanyag: Azure Active Directory integráció a Workspot-vezérlővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Workspot-vezérlést Azure Active Directory (Azure AD) használatával. Ha integrálja az Workspot-vezérlőt az Azure AD-val, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Workspot vezérlőhöz.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjaik használatával automatikusan bejelentkezzenek a Workspot vezérlőbe (egyszeri bejelentkezés [SSO]).
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való SaaS-alkalmazások integrálásával kapcsolatos további információkért lásd: [egyszeri bejelentkezés az Azure ad-alkalmazásokba](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Workspot-vezérlővel való konfigurálásához a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

* Egy Workspot vezérlő egyszeri bejelentkezésre képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

> [!Note]
> A Workspot vezérlő támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.


## <a name="adding-workspot-control-from-the-gallery"></a>Workspot-vezérlő hozzáadása a katalógusból

A Workspot-vezérlés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Workspot vezérlőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Workspot-vezérlő a katalógusból való hozzáadásához kövesse az alábbi lépéseket:**

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, és válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Az ablak tetején válassza az **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Workspot vezérlőt**, válassza a **Workspot vezérlés** lehetőséget az eredmények panelen, majd válassza a **Hozzáadás** lehetőséget.

     !["Hozzáadás a katalógusból" ablak](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Workspot-vezérléssel a Britta Simon segítségével.
Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Workspot-vezérlőben.

Az Azure AD egyszeri bejelentkezés Workspot-vezérlővel való konfigurálásához és teszteléséhez a következő feladatokat kell elvégeznie:

1. Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
2. A [Workspot-vezérlés egyszeri bejelentkezésének konfigurálásával](#configure-workspot-control-single-sign-on) konfigurálhatja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon szolgáltatásban.
4. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. [Hozzon létre egy Workspot vezérlőt](#create-a-workspot-control-test-user) , amely a felhasználó Azure ad-képviseletéhez kapcsolódó, a Workspot-vezérlőben található Britta Simon elérési útjának létrehozását mutatja be.
6. Az [egyszeri bejelentkezés tesztelésével](#test-single-sign-on) ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Workspot-vezérlővel való konfigurálásához kövesse az alábbi lépéseket:

1. Az Azure Portal **Workspot vezérlő** alkalmazás-integráció lapján válassza [](https://portal.azure.com/)az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezési **módszer kiválasztása** ablakban válassza az **SAML** mód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Válassza ki az egyszeri bejelentkezés kiválasztási módszerét ablakban](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** (ceruza) ikont az **alapszintű SAML-konfiguráció** eléréséhez.

    ![Szerkesztés ikon kiemelve az "alapszintű SAML-konfiguráció"](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást identitásszolgáltató módban szeretné konfigurálni, kövesse az alábbi lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    1. Az **azonosító** szövegmezőbe írja be az URL-címet a következő mintában:<br/>
    ***https://<<i></i> példánynév> – SAML.workspot.com/SAML/metadata***

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő mintában:<br/>
    ***https://<<i></i> példánynév> – SAML.workspot.com/SAML/Assertion***

5. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget.

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL** szövegmezőben adjon meg egy URL-címet a következő mintában:<br/>
    ***https://<<i></i> példánynév> – SAML.workspot.com/***

    > [!NOTE]
    > Ezek az értékek nem valósak. Cserélje le ezeket az értékeket a tényleges azonosítóra, a válasz URL-címére és a bejelentkezési URL-címre. Az értékek lekéréséhez lépjen kapcsolatba a [Workspot-vezérlő](mailto:support@workspot.com) ügyfélszolgálatával. Vagy a Azure Portal **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** letöltéséhez az elérhető lehetőségek közül a követelmények szerint. Mentse a számítógépére.

    ![A tanúsítvány (Base64) letöltési hivatkozása](common/certificatebase64.png)

7. A **Workspot-vezérlő beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények szerint:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - **Bejelentkezési URL-cím**

    - **Azure AD-azonosító**

    - **Kijelentkezési URL-cím**

### <a name="configure-workspot-control-single-sign-on"></a>Workspot-vezérlés egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workspot Control szolgáltatásba biztonsági rendszergazdaként.

2. A lap tetején található eszköztáron válassza a **beállítás** , majd az **SAML** lehetőséget.

    ![Beállítási lehetőségek](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. A **Security Assertion Markup Language konfiguráció** ablakban hajtsa végre az alábbi lépéseket:
 
    ![Security Assertion Markup Language konfigurációs ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Az **entitás azonosítója** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosítót** .

    1. Illessze be a Azure Portalból másolt **bejelentkezési URL-címet** a **szolgáltatás URL-címe** mezőbe.

    1. A **kijelentkezési szolgáltatás URL-címe** mezőben illessze be a Azure Portalból másolt **KIJELENTKEZÉSI URL-címet** .

    1. Válassza ki a **fájl frissítése** elemet az X. 509 tanúsítványba való feltöltéshez a Azure Portal által letöltött base-64 kódolású tanúsítványt.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**, a **felhasználók**, majd az **összes felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget az ablak tetején.

    ![Az "új felhasználó" gomb](common/new-user.png)

3. A felhasználó tulajdonságainál kövesse az alábbi lépéseket:

    ![A felhasználó tulajdonságai ablak](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A Felhasználónév mezőbe írja be a **brittasimon@* yourcompanydomain. Extension * * * **nevet** . Adja meg például a következőt: **BrittaSimon@contoso . <i></i> com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Ezután jegyezze fel a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban Simon Britta-hozzáférést biztosít a Workspot vezérlőhöz, hogy lehetővé tegye az Azure-beli egyszeri bejelentkezés használatát.

1. A Azure Portal válassza a **vállalati alkalmazások**, **minden alkalmazás**, majd a **Workspot vezérlő** elemet.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Workspot vezérlő** elemet.

    ![A Workspot vezérlő hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** gombot. Ezután válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** ablakban.

    ![A "hozzárendelés hozzáadása" ablak](common/add-assign-user.png)

5. A **felhasználók és csoportok** ablakban válassza a **Britta Simon** elemet a **felhasználók** listából. Ezután kattintson a **Kiválasztás** elemre.

6. Ha az SAML-kijelentésben bármely szerepkör értéke várható, válassza ki a megfelelő szerepkört a felhasználó számára a **szerepkör kiválasztása** ablakban lévő listából. Ezután kattintson az alul található **kijelölés** elemre.

7. A **hozzárendelés hozzáadása** ablakban válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-workspot-control-test-user"></a>Workspot-vezérlő tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Workspot-vezérlőbe, a Workspot vezérlőbe kell azokat kiépíteni. A kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez kövesse az alábbi lépéseket:**

1. Jelentkezzen be a Workspot-vezérlőbe biztonsági rendszergazdaként.

2. A lap tetején található eszköztáron válassza a **felhasználók** , majd a **felhasználó hozzáadása** elemet.

    !["Felhasználók" beállításai](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Az **új felhasználó hozzáadása** ablakban kövesse az alábbi lépéseket:

    !["Új felhasználó hozzáadása" ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Az **Utónév** mezőbe írja be a felhasználó utónevét (például **Britta**).

    1. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét (például **Simon**).

    1. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét, például: **Brittasimon@contoso . <i></i> com**.

    1. Válassza ki a megfelelő felhasználói szerepkört a **szerepkör** legördülő listából.

    1. Válassza ki a megfelelő felhasználói csoportot a **csoport** legördülő listából.

    1. Válassza a **felhasználó hozzáadása** elemet.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a *hozzáférési panelen* teszteljük az Azure ad egyszeri bejelentkezési konfigurációját.

Ha a hozzáférési panelen a **Workspot vezérlő** csempére kattint, automatikusan be kell jelentkeznie arra a Workspot-vezérlőre, amelyhez be kell ÁLLÍTANIA az SSO-t. További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)