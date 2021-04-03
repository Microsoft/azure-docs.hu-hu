---
title: 'Oktatóanyag: Azure Active Directory integráció a UserVoice-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és UserVoice között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 39b3104f9d6cbc004383bf2c50d07c4dd345fa3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353591"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Oktatóanyag: Azure Active Directory integráció a UserVoice

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a UserVoice a Azure Active Directory (Azure AD) szolgáltatással.
A UserVoice és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a UserVoice.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a UserVoice (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció UserVoice való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* UserVoice egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A UserVoice támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice hozzáadása a gyűjteményből

A UserVoice Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a UserVoice a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha UserVoice szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **UserVoice** kifejezést, válassza a **UserVoice** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![UserVoice az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az UserVoice-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a UserVoice kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés UserVoice való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[UserVoice egyszeri bejelentkezés konfigurálása](#configure-uservoice-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre UserVoice-teszt felhasználót](#create-uservoice-test-user)** – hogy a UserVoice Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés UserVoice való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **UserVoice** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![UserVoice tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.UserVoice.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a UserVoice ügyfélszolgálati [csapatához](https://www.uservoice.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **UserVoice beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-uservoice-single-sign-on"></a>UserVoice egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a UserVoice vállalati webhelyre rendszergazdaként.

2. A felső eszköztáron kattintson a **Beállítások** elemre, majd válassza a menü **webes portál** menüpontját.
   
    ![Beállítások szakasz az alkalmazás oldalán](./media/uservoice-tutorial/ic777519.png "Beállítások")

3. A **webes portál** lap **felhasználói hitelesítés** szakaszában kattintson a **Szerkesztés** elemre a **felhasználói hitelesítés szerkesztése** párbeszédpanel megnyitásához.
   
    ![Webportál lap](./media/uservoice-tutorial/ic777520.png "Webes portál")

4. A **felhasználó hitelesítésének szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználói hitelesítés szerkesztése](./media/uservoice-tutorial/ic777521.png "Felhasználói hitelesítés szerkesztése")
   
    a. Kattintson az **egyszeri Sign-On (SSO)** lehetőségre.
 
    b. Illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt az **egyszeri bejelentkezéses távoli bejelentkezési** szövegmezőbe.

    c. Illessze be a **kijelentkezési URL-címet** , amelyet a Azure Portal az **egyszeri bejelentkezéses távoli Sign-Out szövegmezőbe** másolt.
 
    d. Illessze be a Azure Portalba másolt **ujjlenyomat** -értéket az **aktuális tanúsítvány SHA1 ujjlenyomat** szövegmezőbe.
    
    e. Kattintson a **hitelesítési beállítások mentése** lehetőségre.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a UserVoice hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **UserVoice** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **UserVoice** lehetőséget.

    ![Az UserVoice hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-uservoice-test-user"></a>UserVoice-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a UserVoice, a UserVoice kell kiépíteni őket. UserVoice esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **UserVoice** -bérlőbe.

2. Válassza a **Beállítások lehetőséget**.
   
    ![Beállítások](./media/uservoice-tutorial/ic777811.png "Beállítások")

3. Kattintson az **általános** elemre.

4. Kattintson **az ügynökök és engedélyek** elemre.
   
    ![Ügynökök és engedélyek](./media/uservoice-tutorial/ic777812.png "Ügynökök és engedélyek")

5. Kattintson a **rendszergazdák hozzáadása** lehetőségre.
   
    ![Rendszergazdák hozzáadása](./media/uservoice-tutorial/ic777813.png "Rendszergazdák hozzáadása")

6. A **rendszergazdák meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Rendszergazdák meghívása](./media/uservoice-tutorial/ic777814.png "Rendszergazdák meghívása")
   
    a. Az e-mailek szövegmezőbe írja be a létrehozni kívánt fiók e-mail-címét, majd kattintson a **Hozzáadás** gombra.
   
    b. Kattintson a **meghívás** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a UserVoice által biztosított UserVoice felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a UserVoice csempére kattint, automatikusan be kell jelentkeznie arra a UserVoice, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)