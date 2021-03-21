---
title: 'Oktatóanyag: Azure Active Directory integráció az optimalizálással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és optimalizálva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c10681a053631a338bd0b9a7d19c5625151a9a5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515797"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Oktatóanyag: Azure Active Directory integráció az optimalizálással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az optimalizálható Azure Active Directory (Azure AD) használatával.
Az Azure AD-val való optimális integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy a szolgáltatás optimalizálható legyen.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezve legyenek az Azure AD-fiókokkal optimalizálva (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció optimalizálással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Az egyszeri bejelentkezést engedélyező előfizetés optimalizálása

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az **SP** által kezdeményezett egyszeri bejelentkezés optimalizálása

## <a name="adding-optimizely-from-the-gallery"></a>Optimalizálás a katalógusból

Az Azure AD-be való optimalizálása integrálásának konfigurálásához optimalizálni kell a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha optimalizálni szeretné a gyűjteményt a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **optimalizálás**, válassza az **optimalizálás** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Optimalizálás az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését optimalizálja a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez szükség van egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létesítésére.

Az Azure AD egyszeri bejelentkezés optimalizálással történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[optimalizált egyszeri bejelentkezés konfigurálása](#configure-optimizely-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Optimalizálással tesztelheti a felhasználót](#create-optimizely-test-user)** , hogy a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó, optimalizálás alatt álló partnere legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés optimalizálással történő konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az alkalmazás-integráció **optimalizálása** lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak optimalizálása](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://app.optimizely.net/<instance name>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Ezek az értékek nem valódiak. A tényleges bejelentkezési URL-címmel és azonosítóval frissíti az értéket, amelyet később az oktatóanyagban talál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az optimalizált alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a "felhasználói attribútumok" párbeszédpanel, ahol a "szerkesztés" ikon látható a jobb felső sarokban.](common/edit-attribute.png)

6. A fentieken kívül az alkalmazás optimalizálása néhány további attribútumot vár az SAML-válaszban. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name | Forrás attribútum |
    | ---------------| --------------- |
    | e-mail | User. mail |
    
    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőkép, amely a "felhasználói jogcímek" párbeszédpanelt mutatja az "új jogcím hozzáadása" és a "Mentés" művelet kiemelésével.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **beállítás optimalizálásával** szakaszban másolja a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-optimizely-single-sign-on"></a>Optimalizált egyszeri Sign-On konfigurálása

1. Az egyszeri bejelentkezés **optimalizálási** oldalon való konfigurálásához lépjen kapcsolatba az optimalizálási fiók kezelőjével, és adja meg a letöltött **tanúsítvány (Base64)** és a megfelelő másolt URL-címeket.

2. Az e-mailekre adott válaszként optimalizálja a bejelentkezési URL-címet (SP által kezdeményezett SSO) és az azonosítót (szolgáltatói entitás AZONOSÍTÓját).

    a. Másolja az optimalizálással megadott **SP-kezdeményező SSO URL-címet** , és illessze be a **bejelentkezési URL** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszának Azure Portal.

    b. Másolja az optimalizálással megadott **szolgáltatói entitás azonosítóját** , és illessze be az **alapszintű SAML-konfiguráció** szakasz **azonosító** szövegmezőbe Azure Portal.

3. Egy másik böngészőablakban jelentkezzen be az optimalizált alkalmazásba.

4. Kattintson a jobb felső sarokban a fiók nevére, majd a **Fiókbeállítások** lehetőségre.

    ![Képernyőfelvétel: a jobb felső sarokban kiválasztott fiók neve, a menüből kiválasztott "Fiókbeállítások" beállítással.](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. A fiók lapon jelölje be az egyszeri bejelentkezés **engedélyezése** jelölőnégyzetet az **Áttekintés** szakaszban.
  
    ![Azure AD – egyetlen Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az optimalizáláshoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd kattintson az **optimalizálás** lehetőségre.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **optimalizálás** lehetőséget.

    ![Az alkalmazások listájának optimalizálási hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-optimizely-test-user"></a>Optimalizálási teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre optimalizálva.

1. A Kezdőlap lapon válassza a **közreműködők** fület.

2. Ha új közreműködőt szeretne hozzáadni a projekthez, kattintson az **új közreműködő** elemre.
   
    ![Képernyőkép, amely az optimalizált kezdőlapot jeleníti meg a "közreműködők" és az "új közreműködő" gomb kiválasztásával.](./media/optimizely-tutorial/create_aaduser_10.png)

3. Adja meg az e-mail-címet, és rendeljen hozzá egy szerepkört. Kattintson a **meghívás** gombra.

    ![Azure AD-tesztkörnyezet létrehozása](./media/optimizely-tutorial/create_aaduser_11.png)

4. E-mailben meghívót kapnak. Az e-mail-cím használatával optimalizálni kell a bejelentkezni.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az optimalizálás csempére kattint, akkor automatikusan be kell jelentkeznie az optimalizáláshoz, amelyhez az egyszeri bejelentkezést beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)