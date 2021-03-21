---
title: 'Oktatóanyag: Azure Active Directory a Cloud felügyeleti portál integrációja Microsoft Azurehoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Felhőbeli felügyeleti portál között Microsoft Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 950744e0b8b2f722d801c911d6fd2c9112f035ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522495"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Oktatóanyag: Azure Active Directory a Cloud felügyeleti portál integrációja Microsoft Azure

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Felhőbeli felügyeleti portál a Microsoft Azurehoz Azure Active Directory (Azure AD) használatával.
A Microsoft Azure Azure AD-vel való integrációja a felhőalapú felügyeleti portál a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy a Microsoft Azurehoz milyen felhőalapú felügyeleti portál férhet hozzá.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a felhőbe felügyeleti portál Microsoft Azure (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Microsoft Azure felhőalapú felügyeleti portál szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Felhőalapú felügyeleti portál Microsoft Azure egyszeri bejelentkezésre alkalmas előfizetés esetén

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Cloud felügyeleti portál for Microsoft Azure támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Felhőbeli felügyeleti portál hozzáadása Microsoft Azurehoz a katalógusból

Ahhoz, hogy az Azure AD-ben Microsoft Azure a Cloud felügyeleti portál integrációját, a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Cloud felügyeleti portál Microsoft Azure.

**A következő lépésekkel adhat hozzá Felhőbeli felügyeleti portál a Microsoft Azurehoz a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **cloud felügyeleti portál for Microsoft Azure**, válassza a **felhő felügyeleti portál Microsoft Azure** az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Felhőbeli felügyeleti portál az eredmények listájában Microsoft Azure](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Cloud felügyeleti portál for Microsoft Azure a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Felhőbeli felügyeleti portál kapcsolódó felhasználó közötti kapcsolatra van szükség a Microsoft Azure létrehozásához.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Cloud felügyeleti portál for Microsoft Azure esetében a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[felhőalapú felügyeleti portál konfigurálása Microsoft Azure egyszeri bejelentkezéshez](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** – az egyes Sign-On beállítások az alkalmazás oldalán konfigurálhatók.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Felhőbeli felügyeleti portál létrehozása a Microsoft Azure teszteléséhez](#create-cloud-management-portal-for-microsoft-azure-test-user)** , hogy a Britta Simon a felhőben felügyeleti portál a felhasználó Azure ad-képviseletéhez kapcsolódó Microsoft Azure.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a felhőalapú felügyeleti portál a Microsoft Azure használatára szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **felhőalapú felügyeleti portál Microsoft Azure** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Felhőalapú felügyeleti portál Microsoft Azure tartomány és URL-címek egyszeri bejelentkezési adataihoz](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    ```http
    https://portal.newsignature.com/<instancename>
    https://portal.igcm.com/<instancename>
    ```

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    ```http
    https://<subdomain>.igcm.com
    https://<subdomain>.newsignature.com
    ```

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    ```http
    https://<subdomain>.igcm.com/<instancename>
    https://<subdomain>.newsignature.com
    https://<subdomain>.newsignature.com/<instancename>
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel [a kapcsolatot a Cloud felügyeleti portál a Microsoft Azure ügyfél-támogatási csapattal](mailto:jczernuszka@newsignature.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **felhőbeli felügyeleti portál beállítása Microsoft Azure** szakaszban másolja a megfelelő URL-címet (ka) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>A felhőalapú felügyeleti portál konfigurálása Microsoft Azure egyetlen Sign-On

Ha az egyszeri bejelentkezést szeretné konfigurálni a **felhőbeli felügyeleti portál Microsoft Azure** oldalon, akkor a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket el kell küldenie a Azure Portalről a [felhőbe felügyeleti portál Microsoft Azure támogatási csapatnak](mailto:jczernuszka@newsignature.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban az Azure egyszeri bejelentkezés használatát engedélyező Britta, ha Microsoft Azurehoz hozzáférést biztosít a Cloud felügyeleti portálhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza **a Felhőbeli felügyeleti portál a Microsoft Azure**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **felhőbeli felügyeleti portál lehetőséget Microsoft Azurehoz**.

    ![Az alkalmazások listájában Microsoft Azure hivatkozáshoz tartozó Felhőbeli felügyeleti portál](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Felhőalapú felügyeleti portál létrehozása Microsoft Azure tesztelési felhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Cloud felügyeleti portál for Microsoft Azure. Működjön együtt a [cloud felügyeleti portál for Microsoft Azure támogatási csapatával](mailto:jczernuszka@newsignature.com) , és adja hozzá a felhasználókat a felhőbeli felügyeleti portál Microsoft Azure platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Microsoft Azure csempéhez tartozó Felhőbeli felügyeleti portálra kattint, automatikusan be kell jelentkeznie a felhőbe felügyeleti portál azon Microsoft Azureekhez, amelyekhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)