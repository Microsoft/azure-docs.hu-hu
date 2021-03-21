---
title: 'Oktatóanyag: Azure Active Directory integráció a LoginRadius-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és LoginRadius között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606419"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Oktatóanyag: Azure Active Directory integráció a LoginRadius

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LoginRadius a Azure Active Directory (Azure AD) szolgáltatással.

A LoginRadius és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a LoginRadius.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a LoginRadius (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció LoginRadius való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* LoginRadius egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A LoginRadius támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-loginradius-from-the-gallery"></a>LoginRadius hozzáadása a gyűjteményből

A LoginRadius Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LoginRadius a katalógusból a felügyelt SaaS-alkalmazások listájához.

**LoginRadius hozzáadása a gyűjteményből:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** ikont.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **új alkalmazás** gombot:

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LoginRadius** kifejezést, válassza az **LoginRadius** elemet az eredmények panelen, majd válassza a **Hozzáadás** gombot az alkalmazás hozzáadásához.

    ![LoginRadius az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az LoginRadius-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a LoginRadius kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés LoginRadius való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[LoginRadius egyszeri bejelentkezés konfigurálása](#configure-loginradius-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre LoginRadius-teszt felhasználót](#create-loginradius-test-user)** – hogy a LoginRadius Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés LoginRadius való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **LoginRadius** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** ikont az **alapszintű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakasza:

   ![LoginRadius tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

   1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet `https://secure.loginradius.com/login`

   1. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be az URL-címet `https://lr.hub.loginradius.com/`

   1. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be a LoginRadius ACS URL-címét. `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget az **összevonási metaadatok XML-kódjának** a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **LoginRadius beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

   - Bejelentkezési URL

   - Azure AD-azonosító

   - Kijelentkezési URL-cím

## <a name="configure-loginradius-single-sign-on"></a>LoginRadius egyetlen Sign-On konfigurálása

Ebben a szakaszban a LoginRadius felügyeleti konzolon engedélyezheti az Azure AD egyszeri bejelentkezést.

1. Jelentkezzen be a LoginRadius [felügyeleti konzoljának](https://adminconsole.loginradius.com/login) fiókjába.

2. Nyissa meg a [LoginRadius felügyeleti konzoljának](https://secure.loginradius.com/account/team) **Team Management** szakaszát.

3. Válassza az **egyszeri bejelentkezés** fület, majd válassza az **Azure ad** lehetőséget:

   ![A LoginRadius Team Management konzol egyszeri bejelentkezés menüjét bemutató képernyőkép](./media/loginradius-tutorial/azure-ad.png)
4. Az Azure AD-telepítő lapon hajtsa végre a következő lépéseket:

   ![A Azure Active Directory konfigurációját bemutató képernyőkép a LoginRadius Team Management konzolon](./media/loginradius-tutorial/single-sign-on.png)

    1. Az **azonosító szolgáltató helye** mezőben adja meg a bejelentkezési végpontot, amelyet az Azure ad-fiókból kap.

    1. Az **azonosító szolgáltató kijelentkezési URL-címe** mezőben adja meg a kijelentkezési végpontot, amelyet az Azure ad-fiókból kap.
 
    1. Az **azonosító-szolgáltató tanúsítványa** mezőben adja meg az Azure ad-fiókból beolvasott Azure ad-tanúsítványt. Adja meg a tanúsítvány értékét a fejlécben és az élőlábban. Például: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. A **szolgáltató tanúsítványa** és a **kiszolgáló-szolgáltató tanúsítvány kulcsában** adja meg a tanúsítványt és a kulcsot. 

       Létrehozhat egy önaláírt tanúsítványt a következő parancsok parancssorból történő futtatásával (Linux/Mac):

       - Az SP-tanúsítvány kulcsának beszerzésére szolgáló parancs: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Az SP tanúsítványának beszerzésére szolgáló parancs: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Ügyeljen arra, hogy a tanúsítvány és a tanúsítvány kulcsának értékeit a fejléc és az élőláb adja meg:
       > - Példa a tanúsítvány értékének formátumára: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - A tanúsítvány kulcsának értéke példa formátuma: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Az **adatleképezés** szakaszban válassza ki a MEZŐKET (SP-mezők), és adja meg a megfelelő Azure ad-mezőket (identitásszolgáltató-mezők).

    Az alábbiakban felsorolunk néhány, az Azure AD-ban felsorolt mezőneveket.

    | Mezők    | Profil kulcsa                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-mail     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Az **e-mail** mező leképezése kötelező. A **FirstName** és a **LastName** mező leképezése nem kötelező.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű teszt felhasználót a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználói** tulajdonságok területen hajtsa végre a következő lépéseket.

   ![A felhasználó párbeszédpanel](common/user-properties.png)

   1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
   1. A **Felhasználónév** mezőbe írja be a nevet `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com.

   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a LoginRadius hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **LoginRadius** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **LoginRadius** lehetőséget.

    ![Az LoginRadius hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** gombot, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Ezután kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

### <a name="create-loginradius-test-user"></a>LoginRadius-tesztelési felhasználó létrehozása

1. Jelentkezzen be a LoginRadius [felügyeleti konzoljának](https://adminconsole.loginradius.com/login) fiókjába.

2. Nyissa meg a LoginRadius felügyeleti konzoljának Team Management szakaszát.

   ![A LoginRadius felügyeleti konzolt megjelenítő képernyőkép](./media/loginradius-tutorial/team-management.png)
3. Az űrlap megnyitásához válassza a **Csoport hozzáadása** lehetőséget az oldal menüjében. 

4. A **csapattag hozzáadása** űrlapon létre kell hoznia egy Britta Simon nevű felhasználót a LoginRadius-helyen a felhasználó adatainak megadásával és a felhasználó által a kívánt engedélyek hozzárendelésével. Ha többet szeretne megtudni az engedélyekről a szerepkörök alapján, tekintse meg a LoginRadius [kezelése](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) című témakör [szerepkör-hozzáférési engedélyek](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) című szakaszát. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

1. A böngészőben nyissa meg a következőt: https://accounts.loginradius.com/auth.aspx és válassza a **fed SSO-bejelentkezés** lehetőséget.
2. Adja meg a LoginRadius-alkalmazás nevét, majd válassza a **Bejelentkezés** lehetőséget.
3. Meg kell nyitnia egy előugró ablakot, amely arra kéri, hogy jelentkezzen be az Azure AD-fiókjába.
4. A hitelesítés után az előugró ablak be lesz zárva, és bejelentkezik a LoginRadius felügyeleti konzolra.

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
