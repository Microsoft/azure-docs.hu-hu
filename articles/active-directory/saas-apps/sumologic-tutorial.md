---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SumoLogic | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SumoLogic között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521894"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SumoLogic

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SumoLogic a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SumoLogic-t az Azure AD-vel, a következőket teheti:

* A SumoLogic-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SumoLogic az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SumoLogic egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SumoLogic támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic hozzáadása a gyűjteményből

A SumoLogic Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SumoLogic a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SumoLogic** kifejezést a keresőmezőbe.
1. Válassza ki a **SumoLogic** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a SumoLogic

Konfigurálja és tesztelje az Azure AD SSO-t a SumoLogic a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SumoLogic-ben.

Az Azure AD SSO és a SumoLogic konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SUMOLOGIC SSO konfigurálása](#configure-sumologic-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre SumoLogic-teszt felhasználót](#create-sumologic-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-SumoLogic rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SumoLogic** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a SumoLogic ügyfélszolgálati [csapatához](https://www.sumologic.com/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A SumoLogic alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a SumoLogic alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    |  Name | Forrás attribútum |
    | ---------------| --------------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | Szerepkörök | User. assignedroles |

    > [!NOTE]
    > [Ide kattintva](../develop/active-directory-enterprise-app-role-management.md) megtudhatja, hogyan konfigurálhatja a **szerepkört** az Azure ad-ben.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **SumoLogic beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a SumoLogic.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **SumoLogic** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sumologic-sso"></a>SumoLogic SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SumoLogic vállalati webhelyre rendszergazdaként.

1. Lépjen a **\> biztonság kezelése** gombra.

    ![Kezelés](./media/sumologic-tutorial/ic778556.png "Kezelés")

1. Kattintson az **SAML** elemre.

    ![Globális biztonsági beállítások](./media/sumologic-tutorial/ic778557.png "Globális biztonsági beállítások")

1. A **válasszon ki egy konfigurációt, vagy hozzon létre egy újat** listából válassza az **Azure ad** lehetőséget, majd kattintson a **Konfigurálás** elemre.

    ![Képernyőfelvétel: az SAML 2,0 konfigurálása, ahol kiválaszthatja az Azure A D-t.](./media/sumologic-tutorial/ic778558.png "SAML 2,0 konfigurálása")

1. Az **SAML 2,0 konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az SAML 2,0 konfigurálása párbeszédpanel, ahol megadhatja a leírt értékeket.](./media/sumologic-tutorial/ic778559.png "SAML 2,0 konfigurálása")

    a. A **konfiguráció neve** szövegmezőbe írja be az **Azure ad** nevet.

    b. Válassza a **hibakeresési mód** lehetőséget.

    c. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    d. A **Authn-kérelem URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portalból másolt.

    e. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja vágólapra a tartalmát, majd illessze be a teljes tanúsítványt **X. 509 tanúsítvány** szövegmezőbe.

    f. **E-mail-attribútumként** válassza az **SAML-tárgy használata** lehetőséget.  

    : Válassza az **SP kezdeményezett bejelentkezési konfiguráció** elemet.

    h. A **bejelentkezési útvonal** szövegmezőbe írja be az **Azure** nevet, majd kattintson a **Mentés** gombra.

### <a name="create-sumologic-test-user"></a>SumoLogic-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a SumoLogic, a SumoLogic kell kiépíteni. SumoLogic esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **SumoLogic** -bérlőbe.

1. Lépjen a **\> felhasználók kezelése** gombra.

    ![A képernyőfelvételen a kezelés menüből kiválasztott felhasználók láthatók.](./media/sumologic-tutorial/ic778561.png "Felhasználók")

1. Kattintson a **Hozzáadás** parancsra.

    ![Képernyőfelvétel: a Hozzáadás gomb a felhasználók számára.](./media/sumologic-tutorial/ic778562.png "Felhasználók")

1. Az **új felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Új felhasználó](./media/sumologic-tutorial/ic778563.png "Új felhasználó")

    a. Írja be annak az Azure AD-fióknak a kapcsolódó adatait, amelyet az **Utónév**, a **vezetéknév** és az **e-mail** szövegmezőbe szeretne kiépíteni.
  
    b. Válasszon egy szerepkört.
  
    c. **Állapotként** válassza az **aktív** lehetőséget.
  
    d. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a SumoLogic által biztosított SumoLogic felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SumoLogic csempére kattint, automatikusan be kell jelentkeznie arra a SumoLogic, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A SumoLogic kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)