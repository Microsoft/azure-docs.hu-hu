---
title: 'Oktatóanyag: Azure Active Directory integráció a ENVI MMIS | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ENVI MMIS között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 6ccf755a73cafa4b855f602aa18246d710e5e1ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454011"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Oktatóanyag: Azure Active Directory integráció a ENVI MMIS

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ENVI-MMIS a Azure Active Directory (Azure AD) szolgáltatással.
A ENVI MMIS és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a ENVI MMIS.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a ENVI MMIS (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ENVI MMIS való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* ENVI MMIS egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ENVI MMIS támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-envi-mmis-from-the-gallery"></a>ENVI-MMIS hozzáadása a gyűjteményből

A ENVI-MMIS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ENVI-MMIS a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A ENVI MMIS a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **ENVI MMIS**, válassza a **ENVI MMIS** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ENVI MMIS az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az ENVI MMIS-mel konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a ENVI MMIS kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés ENVI-MMIS való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[ENVI MMIS-alapú egyszeri bejelentkezés konfigurálása](#configure-envi-mmis-single-sign-on)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[ENVI-MMIS-teszt felhasználó létrehozása](#create-envi-mmis-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó ENVI-MMIS.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés ENVI-MMIS való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **ENVI MMIS** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű S L konfiguráció" kifejezést jeleníti meg az "azonosító", "válasz U R L" és "Mentés" gomb kiemelésével.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![ENVI MMIS tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a ENVI MMIS-ügyfélszolgálati [csapatához](mailto:support@ioscorp.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeri Sign-On beállítása az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **ENVI MMIS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-envi-mmis-single-sign-on"></a>ENVI-MMIS konfigurálása egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be a ENVI MMIS-webhelyre rendszergazdaként.

2. Kattintson a **saját tartomány** fülre.

    ![A "saját tartomány" beállítással rendelkező "felhasználó" menüt megjelenítő képernyőkép.](./media/envimmis-tutorial/configure1.png)

3. Kattintson a **Szerkesztés** gombra.

    ![Képernyőkép, amely megjeleníti a "szerkesztés" gombot.](./media/envimmis-tutorial/configure2.png)

4. Jelölje be a **távoli hitelesítés használata** jelölőnégyzetet, majd válassza a **http-átirányítás** lehetőséget a **Hitelesítés típusa** legördülő listából.

    ![A "Remote Authentication" (távoli hitelesítés használata) és a "H T t P-átirányítás" beállítású "Részletek" lapot megjelenítő képernyőkép.](./media/envimmis-tutorial/configure3.png)

5. Válassza az **erőforrások** fület, majd kattintson a **metaadatok feltöltése** elemre.

    ![Képernyőkép, amely az "erőforrások" fület jeleníti meg a "metaadatok feltöltése" művelet kiválasztásával.](./media/envimmis-tutorial/configure4.png)

6. A **metaadatok feltöltése** előugró ablakban végezze el a következő lépéseket:

    ![Képernyőfelvétel: "a metaadatok feltöltése" felugró ablak a "fájl" lehetőség kiválasztásával, valamint a "fájl kiválasztása" ikon és az "OK" gomb kiemelve.](./media/envimmis-tutorial/configure5.png)

    a. Válassza a **fájl** lehetőséget a **feltöltésből** a legördülő listából.

    b. Töltse fel a letöltött metaadat-fájlt Azure Portal a **fájl kiválasztása ikonra** kattintva.

    c. Kattintson az **OK** gombra.

7. A letöltött metaadat-fájl feltöltése után a mezők automatikusan feltöltve lesznek. Kattintson a **Frissítés** parancsra

    ![Egyetlen Sign-On mentés gomb konfigurálása](./media/envimmis-tutorial/configure6.png)

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a ENVI-MMIS.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **ENVI MMIS** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **ENVI MMIS** elemet.

    ![Az ENVI MMIS hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben a szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-envi-mmis-test-user"></a>ENVI MMIS-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a ENVI MMIS, a ENVI-MMIS kell kiépíteni őket. ENVI MMIS esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ENVI MMIS vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználói lista** fülre.

    ![Képernyőkép, amely a "felhasználó" menüt jeleníti meg a "felhasználói lista" lehetőség kiválasztásával.](./media/envimmis-tutorial/user1.png)

3. Kattintson a **felhasználó hozzáadása** gombra.

    ![A "felhasználók" szakaszt tartalmazó képernyőkép, amely a "felhasználó hozzáadása" gomb kiválasztásával jelenik meg.](./media/envimmis-tutorial/user2.png)

4. A **felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user3.png)

    a. A **Felhasználónév** szövegmezőbe írja be a Britta Simon-fiók (például **brittasimon \@ contoso.com**) felhasználónevét.
    
    b. Az **Utónév** szövegmezőbe írja be a BrittaSimon (például **Britta**) utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a BrittaSimon vezetéknevét, például **Simon**.

    d. Adja meg a felhasználó címét a szövegmező **címében** .
    
    e. Az **E-mail cím** szövegmezőbe írja be a Britta Simon-fiók, például a **brittasimon \@ contoso.com** e-mail címét.

    f. Az **SSO-Felhasználónév** szövegmezőbe írja be a Britta Simon-fiókjának felhasználónevét, például a **brittasimon \@ contoso.com** nevet.

    : Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ENVI MMIS csempére kattint, automatikusan be kell jelentkeznie arra a ENVI-MMIS, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)