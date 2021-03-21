---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a StatusPage | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és StatusPage között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a StatusPage

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a StatusPage a Azure Active Directory (Azure AD) szolgáltatással.
A StatusPage és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a StatusPage.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a StatusPage (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció StatusPage való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* StatusPage egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A StatusPage támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-statuspage-from-the-gallery"></a>StatusPage hozzáadása a gyűjteményből

A StatusPage Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a StatusPage a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **StatusPage** kifejezést a keresőmezőbe.
1. Válassza ki a **StatusPage** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Azure AD SSO konfigurálása és tesztelése a StatusPage-hez

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az StatusPage-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a StatusPage kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD SSO és a StatusPage konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[STATUSPAGE SSO konfigurálása](#configure-statuspage-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre StatusPage-teszt felhasználót](#create-statuspage-test-user)** – hogy a StatusPage Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **AskYourTeam** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:

    | Azonosító |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

     | Válasz URL-cím |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Az [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) egyszeri bejelentkezés konfigurálásához szükséges metaadatok igényléséhez lépjen kapcsolatba a StatusPage támogatási csoportjával. 
    >
    > a. A metaadatokból másolja ki a kiállító értékét, majd illessze be az **azonosító** szövegmezőbe.
    >
    > b. A metaadatok között másolja a válasz URL-címét, majd illessze be a **Válasz URL-** szövegmezőbe.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **StatusPage beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a StatusPage hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **StatusPage** lehetőséget.

2. Az alkalmazások listában válassza a **StatusPage** lehetőséget.

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-statuspage-sso"></a>StatusPage SSO konfigurálása

1. A StatusPage belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése** lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **StatusPage beállítása** elemre, majd a StatusPage alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a StatusPage való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a StatusPage, egy másik böngészőablakban jelentkezzen be a StatusPage vállalati webhelyre rendszergazdaként.

1. A fő eszköztáron kattintson a **fiók kezelése** lehetőségre.

    ![Képernyőfelvétel: a StatusPage vállalati webhelyről kiválasztott fiók kezelése.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson az **egyszeri bejelentkezés** fülre.

    ![A képernyőképen az egyszeri bejelentkezés lap látható.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:

    ![A képernyőképen az S S O Setup (S S O Setup) oldal jelenik meg, ahol megadhatja a leírt értékeket.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Képernyőfelvétel: a konfiguráció mentése gomb.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Az **SSO cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be a **tanúsítvány** szövegmezőbe.

    c. Kattintson a **konfiguráció mentése** gombra.

### <a name="create-statuspage-test-user"></a>StatusPage-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a StatusPage-ben.

A StatusPage az igény szerinti üzembe helyezést is támogatja. Már engedélyezte az [Azure ad egyszeri bejelentkezés konfigurálásához](#configure-azure-ad-sso).

**A következő lépések végrehajtásával hozhat létre egy Britta Simon nevű felhasználót a StatusPage-ben:**

1. Jelentkezzen be a StatusPage vállalati webhelyre rendszergazdaként.

1. A felső menüben kattintson a **fiók kezelése** lehetőségre.

    ![Képernyőfelvétel: a StatusPage vállalati webhelyről kiválasztott fiók kezelése.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson a **csapattagok** fülre.
  
    ![Képernyőfelvétel: a csapattagok lapot jeleníti meg.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kattintson a **csapattag hozzáadása** elemre.
  
    ![Képernyőfelvétel: a csapattag hozzáadása gomb.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Írja be annak az érvényes felhasználónak az **E-mail címét**, **utónevét** és **vezetéknevét** , amelyet szeretne a kapcsolódó szövegmezőbe beírni. 

    ![Képernyőfelvétel: a felhasználó hozzáadása párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **Szerepkörként** válassza az **ügyfél rendszergazdája** elemet.

1. Kattintson a **fiók létrehozása** lehetőségre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját.

* Kattintson az alkalmazás tesztelése Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a StatusPage, amelyhez be szeretné állítani az egyszeri bejelentkezést

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások StatusPage csempére kattint, automatikusan be kell jelentkeznie arra a StatusPage, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A StatusPage konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).