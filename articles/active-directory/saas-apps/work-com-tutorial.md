---
title: 'Oktatóanyag: Azure Active Directory integráció a Work.com-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Work.com között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636153"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Oktatóanyag: Azure Active Directory integráció a Work.com

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Work.com a Azure Active Directory (Azure AD) szolgáltatással.
A Work.com és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Work.com.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Work.com (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Work.com való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Work.com egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Work.com támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-workcom-from-the-gallery"></a>Work.com hozzáadása a gyűjteményből

A Work.com Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Work.com a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Work.com szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Work.com** kifejezést, válassza a **Work.com** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Work.com az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Work.com-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Work.com kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Work.com való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Work.com egyszeri bejelentkezés konfigurálása](#configure-workcom-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Work.com-teszt felhasználót](#create-workcom-test-user)** – hogy a Work.com Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

>[!NOTE]
>Az egyszeri bejelentkezés konfigurálásához még egy egyéni Work.com-tartománynevet kell beállítania. Meg kell határoznia legalább egy tartománynevet, tesztelni a tartománynevet, és telepítenie kell a teljes szervezet számára.

Az Azure AD egyszeri bejelentkezés Work.com való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Work.com** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Work.com tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez forduljon a Work.com ügyfélszolgálati [csapatához](https://help.salesforce.com/articleView?id=000159855&type=3) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Work.com beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-workcom-single-sign-on"></a>Work.com egyetlen Sign-On konfigurálása

1. Jelentkezzen be rendszergazdaként a Work.com-bérlőbe.

2. Nyissa meg a **telepítőt**.
   
    ![Képernyőfelvétel: a felhasználó menüből kiválasztott beállítás.](./media/work-com-tutorial/ic794108.png "Beállítás")

3. A bal oldali navigációs ablaktábla **felügyelet** szakaszában kattintson a **tartományi felügyelet** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **saját tartomány** lehetőségre a **saját tartomány** lap megnyitásához. 
   
    ![Képernyőfelvétel: a tartomány kiválasztott tartományának felügyelete az adminisztráció ablaktáblán.](./media/work-com-tutorial/ic767825.png "Saját tartomány")

4. Annak ellenőrzéséhez, hogy a tartomány megfelelően van-e beállítva, győződjön meg arról, hogy a "**4. lépés központi telepítése a felhasználók számára**" értékre van állítva, és tekintse át a "**saját tartomány beállításai**" című részt.
   
    ![A felhasználó számára központilag telepített tartomány](./media/work-com-tutorial/ic784377.png "A felhasználó számára központilag telepített tartomány")

5. Jelentkezzen be a Work.com-bérlőbe.

6. Nyissa meg a **telepítőt**.
    
    ![Képernyőfelvétel: a felhasználó menüből kiválasztott beállítás.](./media/work-com-tutorial/ic794108.png "Beállítás")

7. Bontsa ki a **biztonsági vezérlők** menüt, majd kattintson az **egyszeres Sign-On beállítások** elemre.
    
    ![Egyszeres Sign-On beállítások](./media/work-com-tutorial/ic794113.png "Egyszeres Sign-On beállítások")

8. Az **egyszeres Sign-On beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![SAML engedélyezve](./media/work-com-tutorial/ic781026.png "SAML engedélyezve")
    
    a. Válassza az **SAML engedélyezve** lehetőséget.
    
    b. Kattintson az **új** gombra.

9. Az **SAML-Sign-On beállításai** szakaszban hajtsa végre a következő lépéseket:
    
    ![SAML egyszeri Sign-On beállítás](./media/work-com-tutorial/ic794114.png "SAML egyszeri Sign-On beállítás")
    
    a. A **név** szövegmezőbe írja be a konfiguráció nevét.  
       
    > [!NOTE]
    > A **név** értékének megadásakor a rendszer automatikusan kitölti az **API-név** szövegmezőt.
    
    b. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
    
    c. A letöltött tanúsítvány Azure Portalból való feltöltéséhez kattintson a **Tallózás** gombra.
    
    d. Az **entitás-azonosító** szövegmezőbe írja be a következőt: `https://salesforce-work.com` .
    
    e. **SAML-identitás típusaként** válassza **az érvényesítés az összevonási azonosítót tartalmazza a felhasználói objektumból** lehetőséget.
    
    f. **SAML-identitás helyeként** válassza az **identitás elemet a tulajdonos utasítás NameIdentfier elemében**.
    
    : Az **Identity Provider bejelentkezési URL-címe** szövegmezőben illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    h. Az **Identity Provider kijelentkezési URL-címe** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.
    
    i. Ha a **szolgáltató kezdeményezte a kérelem kötését**, válassza a **http post** lehetőséget.
    
    j. Kattintson a **Mentés** gombra.

10. A Work.com klasszikus portál bal oldali navigációs paneljén kattintson a **tartományi felügyelet** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **saját tartomány** lehetőségre a **saját tartomány** lap megnyitásához. 
    
    ![Képernyőfelvétel: a tartomány-felügyeleti listából kiválasztott tartomány.](./media/work-com-tutorial/ic794115.png "Saját tartomány")

11. A **saját tartomány** lapon a **bejelentkezési oldal védjegyezése** szakaszban kattintson a **Szerkesztés** elemre.
    
    ![Képernyőfelvétel: a bejelentkezési oldal védjegyezési szakasza, ahol a szerkesztés lehetőséget választhatja.](./media/work-com-tutorial/ic767826.png "Bejelentkezési oldal arculata")

12. A **bejelentkezési oldal védjegyezése** lapon a **hitelesítési szolgáltatás** szakaszban megjelenik az **SAML SSO-beállítások** neve. Jelölje ki, majd kattintson a **Mentés** gombra.
    
    ![A képernyőképen a bejelentkezési oldal arculata látható, ahol kiválaszthatja a beállítás nevét, amely P P E.](./media/work-com-tutorial/ic784366.png "Bejelentkezési oldal arculata")

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Work.com hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Work.com** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Work.com** lehetőséget.

    ![Az Work.com hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-workcom-test-user"></a>Work.com-tesztelési felhasználó létrehozása

Ahhoz Azure Active Directory, hogy a felhasználók bejelentkezhetnek, a Work.com kell kiépíteni. Work.com esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Work.com vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **telepítőt**.
   
    ![Képernyőfelvétel: a felhasználó menüből kiválasztott beállítás.](./media/work-com-tutorial/ic794108.png "Beállítás")

3. Nyissa meg a **felhasználók kezelése \>** lehetőséget.
   
    ![Felhasználók kezelése](./media/work-com-tutorial/IC784369.png "Felhasználók kezelése")

4. Kattintson az **új felhasználó** elemre.
   
    ![Minden felhasználó](./media/work-com-tutorial/IC794117.png "Minden felhasználó")

5. A felhasználó szerkesztése szakaszban hajtsa végre a következő lépéseket a kapcsolódó szövegmezők felvenni kívánt érvényes Azure ad-fiók attribútumaiban:
   
    ![Felhasználó szerkesztése](./media/work-com-tutorial/ic794118.png "Felhasználó szerkesztése")
   
    a. A **Utónév** szövegmezőbe írja be a felhasználó **Britta** **utónevét** .
    
    b. A **vezetéknév** szövegmezőbe írja be a Simon **nevű felhasználó vezetéknevét** . 
    
    c. Az **alias** szövegmezőbe írja be a felhasználó **BrittaS** **nevét** .
    
    d. Az **e-mail** szövegmezőbe írja be a felhasználó **e-mail-címét** Brittasimon@contoso.com .
    
    e. A **Felhasználónév** szövegmezőbe írja be a felhasználó nevét, például: Brittasimon@contoso.com .
    
    f. A **Nick neve** szövegmezőbe írja be a **Simon** felhasználó **nick-nevét** .
    
    : Válassza a **szerepkör**, a **felhasználói licenc** és a **profil** lehetőséget.
    
    h. Kattintson a **Mentés** gombra.  
      
    > [!NOTE]
    > Az Azure AD-fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítését, mielőtt az aktívvá válna.
    > 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Work.com csempére kattint, automatikusan be kell jelentkeznie arra a Work.com, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)