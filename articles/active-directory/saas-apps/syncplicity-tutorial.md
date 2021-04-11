---
title: 'Oktatóanyag: Azure Active Directory integráció a syncplicity-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és syncplicity között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201501"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Oktatóanyag: a syncplicity és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a syncplicity a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az syncplicity-t az Azure AD-vel, a következőket teheti:

* A syncplicity-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a syncplicity az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, 12 hónapos ingyenes próbaverziót kaphat [itt](https://azure.microsoft.com/free/).
* Syncplicity egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A syncplicity támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity hozzáadása a gyűjteményből

A syncplicity Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a syncplicity a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. A **Létrehozás** területen kattintson a **vállalati alkalmazás** lehetőségre.
1. A **Tallózás az Azure ad** -katalógusban szakaszban írja be a **syncplicity** kifejezést a keresőmezőbe.
1. Válassza ki a **syncplicity** az eredmények panelen, majd kattintson a **Létrehozás** gombra az alkalmazás hozzáadásához. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a syncplicity a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a syncplicity-ben.

Az Azure AD SSO és a syncplicity konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[SYNCPLICITY SSO konfigurálása](#configure-syncplicity-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre syncplicity-teszt felhasználót](#create-syncplicity-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-syncplicity rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.
7. **[SSO frissítése](#update-sso)**) – Ha módosította az egyszeri bejelentkezési beállításokat az Azure AD-ben, módosítsa a szükséges módosításokat a syncplicity-ben.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **syncplicity** alkalmazás-integráció lapján keresse meg a **első lépések** szakaszt, és válassza az **egyszeri bejelentkezés beállítása** lehetőséget.
2. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.syncplicity.com/sp`

    b. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.syncplicity.com`
    
    c. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a syncplicity ügyfélszolgálati [csapatához](https://www.syncplicity.com/contact-us) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** elemre. Ezután a párbeszédpanelen kattintson a három pontot ábrázoló gombra az aktív tanúsítvány mellett, és válassza a **PEM-tanúsítvány letöltése** lehetőséget.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

    > [!NOTE]
    > A PEM-tanúsítványra van szüksége, mivel a syncplicity nem fogadja el a tanúsítványokat CER formátumban.

6. A **syncplicity beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso&quot;></a>Syncplicity SSO konfigurálása

1. Jelentkezzen be a **syncplicity** -bérlőbe.

1. A felső menüben kattintson a **rendszergazda** elemre, válassza a **Beállítások** lehetőséget, majd kattintson az **egyéni tartomány és az egyszeri bejelentkezés** lehetőségre.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png &quot;Syncplicity")

1. Az **egyszeri Sign-On (SSO)** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri Sign-On \( egyszeri bejelentkezés\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Az **egyéni tartomány** szövegmezőbe írja be a tartomány nevét.
  
    b. Válassza az **engedélyezve** lehetőséget **egyetlen Sign-On állapotként**.

    c. Az **entitás-azonosító** szövegmezőbe illessze be az **azonosító (Entity ID)** értéket, amelyet a Azure Portal **alapszintű SAML-konfigurációjában** használt.

    d. A **bejelentkezési oldal URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    e. A **kijelentkezési oldal URL-címe** szövegmezőbe illessze be azt a **KIjelentkezési URL-címet** , amelyet a Azure Portal másolt.

    f. Az **identitás-szolgáltató tanúsítványa** területen kattintson a **fájl kiválasztása** elemre, majd töltse fel a Azure Portalból letöltött tanúsítványt.

    : Kattintson a **módosítások mentése** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
3. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:

   a. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.

   b. A **Név** mezőbe írja a következőt: `B.Simon`.  
   
   c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   
   d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a syncplicity.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **syncplicity** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó/csoport hozzáadása** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)
1. A **hozzárendelés hozzáadása** oldalon válassza a **felhasználók** lehetőséget. 
1. A **felhasználók** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** lapon kattintson a **hozzárendelés** gombra.

### <a name="create-syncplicity-test-user"></a>Syncplicity-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók be tudják jelentkezni, a syncplicity alkalmazásnak kell kiépíteni. Ez a szakasz azt ismerteti, hogyan hozhatók létre Azure AD-beli felhasználói fiókok a syncplicity-ben.

**Ha felhasználói fiókot szeretne kiépíteni a syncplicity, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **syncplicity** -bérlőbe (például: `https://company.Syncplicity.com` ).

1. Kattintson a **rendszergazda** elemre, és válassza a **felhasználói fiókok** lehetőséget, majd kattintson **a felhasználó hozzáadása** elemre.

    ![Felhasználók kezelése](./media/syncplicity-tutorial/ic769764.png "Felhasználók kezelése")

1. Írja be a kiépíteni kívánt Azure AD **-fiók e-mail címét** , válassza  a felhasználó **szerepkörként** lehetőséget, majd kattintson a **tovább** gombra.

    ![Fiókadatok](./media/syncplicity-tutorial/ic769765.png "Fiókadatok")

    > [!NOTE]
    > Az Azure AD-fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítésére és aktiválására szolgáló hivatkozást.

1. Válasszon ki egy csoportot a cégnél, hogy az új felhasználó tagja legyen, majd kattintson a **tovább** gombra.

    ![Csoporttagság](./media/syncplicity-tutorial/ic769772.png "Csoporttagság")

    > [!NOTE]
    > Ha nincsenek csoportok felsorolva, kattintson a **tovább** gombra.

1. Válassza ki azokat a mappákat, amelyeket a syncplicity vezérlőben szeretne elhelyezni a felhasználó számítógépén, majd kattintson a **tovább** gombra.

    ![Syncplicity mappák](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappák")

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a syncplicity által biztosított syncplicity felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a syncplicity csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a syncplicity, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

### <a name="update-sso"></a>Egyszeri bejelentkezés frissítése

Ha módosítania kell az egyszeri bejelentkezést, ellenőriznie kell a használt SAML- **aláíró tanúsítványt** . Ha a tanúsítvány megváltozott, ügyeljen arra, hogy töltse fel az újat a syncplicity az **[SYNCPLICITY SSO konfigurálása](#configure-syncplicity-sso)** című cikkben leírtak szerint.

Ha a syncplicity Mobile alkalmazást használja, akkor segítségért forduljon a syncplicity ügyfélszolgálatához ( support@syncplicity.com ).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
