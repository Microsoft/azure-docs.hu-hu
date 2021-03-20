---
title: 'Oktatóanyag: Azure Active Directory integráció a ON24 virtuális környezet SAML-kapcsolataival | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ON24 virtuális környezet SAML-kapcsolata között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 30fd3843b50ac6b075d33e961986b94ee2496fef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518517"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Oktatóanyag: Azure Active Directory integráció a ON24 virtuális környezet SAML-kapcsolataival

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ON24 virtuális környezet SAML-kapcsolódását Azure Active Directory (Azure AD) használatával.
A ON24 virtuális környezet SAML-kapcsolatainak az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a ON24 virtuális környezet SAML-kapcsolatához.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a virtuális környezet SAML-kapcsolatainak (egyszeri bejelentkezés) ON24 az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ON24 virtuális környezet SAML-kapcsolatban való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* ON24 virtuális környezet SAML-kapcsolatok egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ON24 virtuális környezet SAML-kapcsolatai támogatják az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>ON24 virtuális környezet SAML-kapcsolatainak hozzáadása a katalógusból

A ON24 virtuális környezet SAML-kapcsolatainak Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ON24 virtuális környezet SAML-csatlakozását a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A ON24 virtuális környezet SAML-kapcsolatainak a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ON24 virtuális környezet SAML-kapcsolódás** lehetőséget, válassza az **ON24 virtuális környezet SAML-kapcsolódás** az eredmény panelről elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ON24 virtuális környezet SAML-kapcsolatai az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a ON24 virtuális környezet SAML-kapcsolatain a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a ON24 virtuális környezet SAML-kapcsolaton belüli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés ON24 virtuális környezet SAML-kapcsolatban való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[ON24 virtuális környezet SAML-kapcsolatok egyszeri bejelentkezésének beállítása](#configure-on24-virtual-environment-saml-connection-single-sign-on)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[ON24 virtuális környezet SAML-kapcsolati teszt felhasználó létrehozása](#create-on24-virtual-environment-saml-connection-test-user)** – a ON24 virtuális környezet SAML-kapcsolatának Britta, amely a felhasználó Azure ad-beli képviseletéhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés ON24 virtuális környezet SAML-kapcsolatban való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **ON24 virtuális környezet SAML-kapcsolatok** alkalmazásának integrációja lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![ON24 virtuális környezet SAML-kapcsolatok tartománya és URL-címek egyszeri bejelentkezési adatai](common/idp-relay.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet:

     **Éles környezet URL-címe**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **QA-környezet URL-címe**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:

     **Éles környezet URL-címe**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **QA-környezet URL-címe**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Kattintson a **további URL-címek beállítása** elemre. 

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    ![Képernyőkép: "további U R ls beállítása" szakasz, a "Bejelentkezés U R L" szövegmező kiemelve.](common/both-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges továbbítási állapottal és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a [ON24 virtuális környezet SAML-kapcsolati ügyfelének ügyfélszolgálatához](https://www.on24.com/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **ON24 virtuális környezet SAML-csatlakozásának beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>A ON24 virtuális környezet SAML-kapcsolatainak konfigurálása egyetlen Sign-On

Az egyszeri bejelentkezés a **ON24 virtuális környezet SAML-kapcsolódási** oldalán való konfigurálásához el kell küldenie a letöltött **összevonás-metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portal [ON24 virtuális környezet SAML-kapcsolódási támogató csapatának](https://www.on24.com/about-us/support/). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a ON24 virtuális környezet SAML-kapcsolatához.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **ON24 virtuális környezet SAML-kapcsolatban** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **ON24 virtuális környezet SAML-kapcsolatok** elemet.

    ![Az ON24 virtuális környezet SAML-kapcsolati hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>ON24 virtuális környezet SAML-kapcsolatok tesztelése felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a ON24 virtuális környezet SAML-kapcsolatban. A [ON24 virtuális környezet SAML-kapcsolódási támogatási csapatának](https://www.on24.com/about-us/support/) használata a ON24 virtuális környezet SAML-kapcsolódási platformjának felhasználói hozzáadásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ON24 virtuális környezet SAML-kapcsolat csempére kattint, automatikusan be kell jelentkeznie a ON24 virtuális környezet SAML-kapcsolatára, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)