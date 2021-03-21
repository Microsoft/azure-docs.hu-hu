---
title: 'Oktatóanyag: Azure Active Directory integráció a meta Networks-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a meta Networks Connector között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516868"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Oktatóanyag: Azure Active Directory integráció a meta Networks-összekötővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a meta Networks Connectort Azure Active Directory (Azure AD) használatával.
A meta Networks-összekötő az Azure AD-vel való integrálásához a következő előnyöket nyújtja:

* A meta Networks-összekötőhöz hozzáféréssel rendelkező Azure AD-t vezérelheti.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a meta Networks-Összekötőbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a meta Networks-összekötővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Meta Networks Connector egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A meta Networks Connector támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t
 
* A meta Networks Connector **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta Networks-összekötő hozzáadása a katalógusból

A meta Networks Connector Azure AD-integrációjának konfigurálásához hozzá kell adnia a meta Networks Connectort a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A meta Networks-összekötő a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **meta Networks Connector** kifejezést, válassza a **meta Networks Connector** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Meta Networks-összekötő az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a meta Networks összekötővel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a meta Networks Connector kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés meta Networks-összekötővel történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[meta Networks Connector egyszeri bejelentkezésének konfigurálása](#configure-meta-networks-connector-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre meta Networks Connector tesztelési felhasználót](#create-meta-networks-connector-test-user)** – hogy a meta Networks-összekötőn a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés meta Networks-összekötővel történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **meta Networks Connector** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/both-advanced-urls.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. A **továbbítási állapot** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Az oktatóanyag későbbi részében a tényleges azonosító, a válasz URL-cím és a Sign-On URL-cím alapján frissítse ezeket az értékeket.

6. A meta Networks Connector alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)
    
7. A fentieken kívül a meta Networks Connector alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Name | Forrás attribútum | Névtér|
    | ---------------| --------------- | -------- |
    | FirstName | User. givenName | |
    | LastName | felhasználó. vezetéknév | |
    | EmailAddress| User. mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | User. userPrincipalName| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | User. telephoneNumber | |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a felhasználói jogcímek új jogcím hozzáadására szolgáló lehetőséggel jelennek meg.](common/new-save-attribute.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése párbeszédpanel, amelyen megadhatja a leírt értékeket.](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

8. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. A **meta Networks Connector beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-meta-networks-connector-single-sign-on"></a>A meta Networks Connector egyetlen Sign-On konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a meta Network Connector rendszergazdai fiókjába.
    
    > [!NOTE]
    > A meta Networks Connector egy biztonságos rendszer. Így a portálhoz való hozzáférés előtt hozzá kell adnia a nyilvános IP-címét az engedélyezési listához az oldalon. A nyilvános IP-cím lekéréséhez kövesse az [itt](https://whatismyipaddress.com/)megadott hivatkozást. Küldje el az IP-címet a [meta Networks Connector ügyfél-támogatási csapatának](mailto:support@metanetworks.com) , hogy az IP-címet hozzáadja az engedélyezési listához.
    
2. Nyissa meg a **rendszergazdát** , és válassza a **Beállítások** lehetőséget.
    
    ![A képernyőképen az adminisztráció menüben kiválasztott beállítások láthatók.](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Győződjön meg arról, hogy a **naplózási internetes forgalom** és a **kényszerített VPN MFA** értéke off.
    
    ![Képernyőfelvétel: a beállítások kikapcsolása.](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Nyissa meg a **rendszergazdát** , és válassza az **SAML** lehetőséget.
    
    ![A képernyőképen az adminisztráció menüjében kiválasztott SAML látható.](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Hajtsa végre a következő lépéseket a **részletek** lapon:
    
    ![Képernyőfelvétel: a részleteket tartalmazó oldal, ahol megadhatja a leírt értékeket.](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Másolja az **SSO URL-címet** , és illessze be a **bejelentkezési URL** -szövegmezőbe a **meta Networks Connector tartomány és URL** -cím szakaszban.
    
    b. Másolja a **címzett URL-címét** , és illessze be a **Válasz URL-** szövegmezőbe a **meta Networks Connector tartomány és URL** -cím szakaszban.
    
    c. Másolja a **célközönség URI-ját (SP-entitás azonosítója)** , és illessze be a **meta Networks Connector tartomány és URL-cím** szakasz **azonosító (entitás azonosítója)** szövegmezőbe.
    
    d. Az SAML engedélyezése
    
6. Az **általános** lapon végezze el a következő lépéseket:

    ![Képernyőfelvétel: az általános lap, amelyen megadhatja a leírt értékeket.](./media/metanetworksconnector-tutorial/configure5.png)

    a. Az **identitás-szolgáltató egyetlen Sign-On URL-címében** illessze be azt a **bejelentkezési URL** -címet, amelyet a Azure Portalból másolt.

    b. Az **Identity Provider kiállítójában** illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Nyissa meg a letöltött tanúsítványt Azure Portal a Jegyzettömbben, illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    d. Az igény szerinti **kiépítés** engedélyezése.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension nevet.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a meta Networks Connector elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **meta Networks Connector** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **meta Networks Connector** elemet.

    ![A meta Networks Connector hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Connector tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a meta Networks Connectorban. A meta Networks Connector az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a meta Networks-összekötőben, a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a meta Networks-összekötőhöz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [meta Networks Connector ügyfél-támogatási csapatához](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a meta Networks Connector csempére kattint, automatikusan be kell jelentkeznie a meta Networks-Összekötőbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)