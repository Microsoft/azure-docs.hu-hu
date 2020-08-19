---
title: 'Oktatóanyag: Azure Active Directory integráció a Questetra BPM Suite szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Questetra BPM Suite között.
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
ms.openlocfilehash: 11a6df348ae623ccf75e61bc1abbb5e28c832237
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548961"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Oktatóanyag: Azure Active Directory integráció a Questetra BPM Suite szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Questetra BPM Suite-t Azure Active Directory (Azure AD) használatával.
A Questetra BPM Suite és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Questetra BPM Suite-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Questetra BPM Suite-ba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Questetra BPM Suite-vel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Questetra BPM Suite egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Questetra BPM Suite támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Questetra BPM-csomag hozzáadása a katalógusból

A Questetra BPM Suite Azure AD-integrációjának konfigurálásához hozzá kell adnia a Questetra BPM-csomagot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Questetra BPM Suite a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **QUESTETRA BPM Suite**kifejezést, válassza a **Questetra BPM Suite** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Questetra BPM-csomag az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Questetra BPM Suite-vel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Questetra BPM Suite-ban található kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Questetra BPM Suite-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[QUESTETRA BPM Suite egyszeri bejelentkezés konfigurálása](#configure-questetra-bpm-suite-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[QUESTETRA BPM Suite-teszt felhasználó létrehozása](#create-questetra-bpm-suite-test-user)** – a Questetra BPM Suite-ban található, a felhasználó Azure ad-Britta kapcsolódó, a
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Questetra BPM Suite-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Questetra BPM Suite** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Questetra BPM Suite-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Ezeket az értékeket az **QUESTETRA BPM Suite** céges webhelyén található **SP Information** szakaszból szerezheti be, amelyet az oktatóanyag későbbi részében talál, vagy a [Questetra BPM Suite ügyfélszolgálati csapatához](https://www.questetra.com/contact/)fordulhat. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **QUESTETRA BPM-csomag beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Questetra BPM Suite egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **QUESTETRA BPM Suite** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Rendszerbeállítások**elemre. 
   
    ![Azure AD egyszeri bejelentkezés][10]

3. A **SingleSignOnSAML** lap megnyitásához kattintson az **SSO (SAML)** elemre. 
   
    ![Azure AD egyszeri bejelentkezés][11]

4. A **QUESTETRA BPM Suite** céges webhelyén, az **SP Information** szakaszban hajtsa végre a következő lépéseket:

    a. Másolja az **ACS URL-címét**, majd illessze be az **ALAPszintű SAML-konfiguráció** szakasz **bejelentkezési URL-címe** szövegmezőbe Azure Portal.
    
    b. Másolja ki az **entitás azonosítóját**, majd illessze be az **alapszintű SAML-konfiguráció** szakasz **azonosító** szövegmezőbe Azure Portal.

5. A **QUESTETRA BPM Suite** vállalati webhelyén hajtsa végre a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][15]
   
    a. Válassza az **egyszeri bejelentkezés engedélyezése**lehetőséget.
   
    b. Az **entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
    
    c. A **bejelentkezési oldal URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.
    
    d. A kijelentkezési **oldal URL-címe** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.
    
    e. A **NameID formátum** szövegmezőbe írja be a következőt: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Nyissa meg az **alapszintű 64** -kódolású tanúsítványt a jegyzettömbben, Azure Portal letöltve, másolja a vágólapra a tartalmát, majd illessze be az **ellenőrzési tanúsítvány** szövegmezőbe. 

    : Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Questetra BPM Suite elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Questetra BPM Suite**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **QUESTETRA BPM Suite**elemet.

    ![Az Questetra BPM Suite-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-questetra-bpm-suite-test-user"></a>Questetra BPM Suite tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Questetra BPM Suite-ban.

**Ha Britta Simon nevű felhasználót szeretne létrehozni a Questetra BPM Suite-ban, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Questetra BPM Suite vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **Rendszerbeállítások > felhasználói lista > új felhasználó lehetőséget**.
 
3. Az új felhasználó párbeszédpanelen hajtsa végre a következő lépéseket: 
   
    ![Tesztelési felhasználó létrehozása][300] 
   
    a. A **név** szövegmezőbe írja be a felhasználó **nevét** britta.simon@contoso.com .
   
    b. Az **e-mail** szövegmezőbe írja be a felhasználó **e-mail-címét** britta.simon@contoso.com .
   
    c. A **jelszó** szövegmezőbe írja be a felhasználó **jelszavát** .
    
    d. Kattintson az **új felhasználó hozzáadása**lehetőségre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Questetra BPM-csomag csempére kattint, automatikusan be kell jelentkeznie a Questetra BPM-csomagba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png