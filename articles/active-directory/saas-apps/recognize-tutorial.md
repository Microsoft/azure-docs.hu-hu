---
title: 'Oktatóanyag: Azure Active Directory integráció a felismeréssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és felismerheti.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 7195a126938deabf542e6fa3e52b72c2efdddac2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515219"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Oktatóanyag: Azure Active Directory integráció felismeréssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az felismerést Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel való felismerés integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a felismeréshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való felismerésre (egyszeri bejelentkezésre).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció felismeréssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Egyszeri bejelentkezésre engedélyezett előfizetés felismerése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A felismerés támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-recognize-from-the-gallery"></a>Felismerés hozzáadása a gyűjteményből

Az Azure AD-ben való felismerés integrálásának konfigurálásához fel kell vennie a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha fel szeretne venni egy felismerést a gyűjteményből, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **felismerés**, válassza a **felismerés** az eredményekből panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Felismerés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés felismeréssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[egyszeri bejelentkezés beállítása](#configure-recognize-single-sign-on)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A **[felismerési teszt felhasználójának létrehozása](#create-recognize-test-user)** – hogy a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés felismeréssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az alkalmazás-integráció **felismerése** lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    >[!NOTE]
    >A **szolgáltatói metaadat-fájlt** az oktatóanyag **felismerési egyszeri bejelentkezés beállítása** szakaszában találja.

    a. Kattintson a **metaadat-fájl feltöltése** elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** érték az alapszintű SAML-konfiguráció szakaszban automatikusan feltöltve lesz.

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak felismerése](common/sp-identifier.png)

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Ha az **azonosító** értéke nem jelenik meg automatikusan, az azonosító értékét a szolgáltatói metaadatok URL-címének az egyszeri bejelentkezés beállításai szakaszból való megnyitásával érheti el, amelyet később az oktatóanyag **felismerési egyszeri bejelentkezés konfigurálása** című szakaszában talál. A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték lekéréséhez forduljon az [ügyfél-támogatási csapathoz](mailto:support@recognizeapp.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **felismerés beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-recognize-single-sign-on"></a>Egyetlen Sign-On felismerésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a felismerő bérlőre rendszergazdaként.

2. A jobb felső sarokban kattintson a **menü** elemre. Válassza a **vállalati rendszergazda** lehetőséget.
   
    ![A képernyőfelvételen a beállítások menüből kiválasztott vállalati rendszergazda látható.](./media/recognize-tutorial/tutorial_recognize_000.png)

3. A bal oldali navigációs ablaktáblán kattintson a **Beállítások** elemre.
   
    ![A képernyőképen a navigációs oldalon kiválasztott beállítások láthatók.](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Hajtsa végre az alábbi lépéseket az **SSO-beállítások** szakaszban.
   
    ![Képernyőfelvétel: az S S O beállításai, ahol megadhatja a leírt értékeket.](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Az **SSO engedélyezése** beállításnál válassza **a be** lehetőséget.

    b. Az **identitásszolgáltató entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
    
    c. Az **SSO cél URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.
    
    d. A **slo-cél URL-címe** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portal másolt. 
    
    e. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **tanúsítvány** szövegmezőbe.
    
    f. Kattintson a **Beállítások mentése** gombra. 

5. Az **SSO-beállítások** szakasz mellett másolja az URL-címet a **szolgáltatói metaadatok URL-címe** alatt.
   
    ![A képernyőképen láthatók a szolgáltatói metaadatok másolásához használható megjegyzések.](./media/recognize-tutorial/tutorial_recognize_003.png)

6. A metaadat-dokumentum letöltéséhez nyissa meg a **metaadatok URL-hivatkozását** egy üres böngésző alatt. Ezután másolja a EntityDescriptor értéket (entityID) a fájlból, és illessze be az **azonosító** szövegmezőbe az **alapszintű SAML-konfigurációban** Azure Portalon.
    
    ![A képernyőképen egy egyszerű szöveget tartalmazó szövegmező jelenik meg, ahol lekérheti a D entitást.](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a felismeréshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **felismerés** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **felismerés** lehetőséget.

    ![Az alkalmazások lista felismerés hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-recognize-test-user&quot;></a>Felismerési teszt felhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezzenek az felismerésbe, fel kell venni őket a felismerésbe. Felismerés esetén a kiépítés manuális feladat.

Ez az alkalmazás nem támogatja a SCIM-létesítést, de a felhasználókra vonatkozó másodlagos felhasználói szinkronizálást is tartalmaz. 

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a vállalati webhely felismerése rendszergazdaként.

2. A jobb felső sarokban kattintson a **menü** elemre. Válassza a **vállalati rendszergazda** lehetőséget.

3. A bal oldali navigációs ablaktáblán kattintson a **Beállítások** elemre.

4. Hajtsa végre a következő lépéseket a **felhasználói szinkronizálás** szakaszban.
   
    ![Új felhasználó](./media/recognize-tutorial/tutorial_recognize_005.png &quot;Új felhasználó")
   
    a. Ha **engedélyezve van a szinkronizálás**, válassza **a be** lehetőséget.
   
    b. A **szinkronizálási szolgáltató** kiválasztása lapon válassza a **Microsoft/Office 365** lehetőséget.
   
    c. Kattintson a **felhasználói szinkronizálás futtatása** elemre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a felismerő csempére kattint, automatikusan be kell jelentkeznie arra az felismerésre, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)