---
title: 'Oktatóanyag: Azure Active Directory integráció a HighGear-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és HighGear között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: b3806d768e8335801e2dd097eac7a440b157de38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92444699"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Oktatóanyag: Azure Active Directory integráció a HighGear

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a HighGear-t Azure Active Directory (Azure AD) használatával.
A HighGear és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a HighGear.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a HighGear (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció HighGear való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Nagyvállalati vagy korlátlan licenccel rendelkező HighGear rendszer

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezés tesztelési környezetben történő konfigurálását.

* A HighGear támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-highgear-from-the-gallery"></a>HighGear hozzáadása a gyűjteményből

A HighGear Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a HighGear a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha HighGear szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **HighGear** kifejezést, válassza a **HighGear** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![HighGear az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a HighGear-rendszerrel a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a HighGear-rendszeren lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés HighGear-rendszerrel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[HighGear egyszeri bejelentkezés konfigurálása](#configure-highgear-single-sign-on)** – az HighGear-alkalmazás oldalának egyetlen Sign-On beállítása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre HighGear-teszt felhasználót](#create-highgear-test-user)** – hogy a felhasználó Azure ad-Britta összekapcsolt, a HighGear-hoz tartozó Simon-beli partnerrel rendelkezzen. 
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebből a szakaszból megtudhatja, hogyan engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portalban.

Az Azure AD egyszeri bejelentkezés az HighGear-rendszerrel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **HighGear** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe illessze be a **szolgáltatói entitás azonosító** mezőjének értékét, amely a HighGear rendszer egyetlen Sign-On beállítások lapján található.

    ![A szolgáltatói entitás azonosítója mező](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Be kell jelentkeznie a HighGear rendszerbe az egyetlen Sign-On beállítások oldal eléréséhez. Miután bejelentkezett, vigye az egérmutatót a HighGear felügyelet fülére, és kattintson az egyszeres Sign-On beállítások menüpontra.
    
    ![Az egyetlen Sign-On beállítások menüelem](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. A **Válasz URL-címe** szövegmezőbe illessze be a HighGear rendszer egyetlen Sign-On beállítások lapjáról az **állítási fogyasztói szolgáltatás (ACS) URL-címének** értékét.

    ![Az állítási fogyasztói szolgáltatás (ACS) URL-címe mező](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

     ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

     A **bejelentkezési URL-cím** szövegmezőbe illessze be a HighGear rendszer egyetlen Sign-On beállítások lapján található **szolgáltatói entitás azonosító** mezőjének értékét. (Ez az entitás-azonosító az SP által kezdeményezett bejelentkezéshez használandó HighGear rendszer alap URL-címe is.)

    ![A szolgáltatói entitás azonosítója mező](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító, a válasz URL-cím és a bejelentkezési URL-cím alapján a HighGear rendszer **egyetlen Sign-On beállítások** lapján. Ha segítségre van szüksége, lépjen kapcsolatba a [HighGear támogatási csapatával](mailto:support@highgear.com).

4. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány** letöltéséhez, és mentse a számítógépre. Szüksége lesz rá az egyszeres Sign-On konfiguráció egy későbbi lépésében.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **HighGear beállítása** szakaszban jegyezze fel a következő URL-címek helyét.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím. Erre az értékre szüksége lesz a következő lépésben #2 a **HighGear-alapú egyszeri bejelentkezés konfigurálása** alatt.

    b. Azure AD-azonosító. Erre az értékre szüksége lesz a következő lépésben #3 a **HighGear-alapú egyszeri bejelentkezés konfigurálása** alatt.

    c. Kijelentkezési URL-cím. Erre az értékre szüksége lesz a következő lépésben #4 a **HighGear-alapú egyszeri bejelentkezés konfigurálása** alatt.

### <a name="configure-highgear-single-sign-on"></a>HighGear egyetlen Sign-On konfigurálása

Az egyszeri bejelentkezés HighGear konfigurálásához jelentkezzen be a HighGear rendszerbe. Miután bejelentkezett, vigye az egérmutatót a HighGear felügyelet fülére, és kattintson az egyszeres Sign-On beállítások menüpontra.

![Az egyetlen Sign-On beállítások menüelem](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Az **identitás-szolgáltató neve** mezőbe írjon be egy rövid leírást, amely megjelenik a HighGear egyetlen Sign-On gombján a bejelentkezési oldalon. Például: Azure AD

2. A HighGear **Sign-On (SSO) URL-címe** mezőben illessze be az értéket a **bejelentkezési URL** -cím mezőből, amely az Azure-beli **HighGear beállítása** szakaszban található.

3. Illessze be az Azure AD- **szolgáltatói entitás azonosítója** mezőjében szereplő értéket az Azure **ad-azonosító** mezőjébe, amely a HighGear **HighGear beállítása** szakaszában található.

4. A HighGear **egyszeri kijelentkezés (SLO) URL-címe** mezőjébe illessze be az Azure-ban az **HighGear beállítása** szakaszban található **kijelentkezési URL** -Cím mező értékét.

5. A Jegyzettömb segítségével nyissa meg az Azure-beli **SAML-aláíró tanúsítvány** szakaszban letöltött tanúsítványt. A **tanúsítvány (Base64)** formátumát le kell töltenie. Másolja a tanúsítvány tartalmát a Jegyzettömbből, és illessze be a HighGear található **Identity Provider Certificate** mezőbe.

6. Küldjön e-mailt a [HighGear támogatási csapatának](mailto:support@highgear.com) a HighGear-tanúsítvány igényléséhez. Kövesse a tőlük kapott utasításokat a **HighGear-tanúsítvány** és a **HighGear-tanúsítvány jelszava** mezők kitöltéséhez.

7. Kattintson a **Save (Mentés** ) gombra az HighGear egyetlen Sign-On konfigurációjának mentéséhez.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a HighGear hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **HighGear** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **HighGear** lehetőséget.

    ![Az HighGear hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-highgear-test-user"></a>HighGear-tesztelési felhasználó létrehozása

Ha egy HighGear tesztelő felhasználót szeretne létrehozni az egyetlen Sign-On konfiguráció teszteléséhez, jelentkezzen be a HighGear rendszerbe.

1. Kattintson az **új Névjegy létrehozása** gombra.

    ![Az új Névjegy létrehozása gomb](media/highgear-tutorial/create-new-contact-button.png)

    Ekkor megjelenik egy menü, amely lehetővé teszi, hogy kiválassza a létrehozni kívánt kapcsolat típusát.

2. A HighGear-felhasználó létrehozásához kattintson az **egyes** menüpontokra.

    A jobb oldalon a panel kicsúszik, így beírhatja az új felhasználó adatait.  
    ![Az új kapcsolatfelvételi űrlap](media/highgear-tutorial/new-contact-form.png)

3. A **név** mezőbe írja be a partner nevét. Például: Britta Simon

4. Kattintson a **További beállítások** menüre, és válassza ki a **fiók adatai** menüpontot.

    ![A fiók adatai menüelemre kattintva](media/highgear-tutorial/account-info-menu-item.png)

5. Állítsa a **bejelentkezni** mezőt Igen értékre.

    Az **egyszeri bejelentkezés engedélyezése** mező automatikusan Igen értékre lesz állítva.

6. Az **egyszeres Sign-On felhasználói azonosító** mezőben adja meg a felhasználó azonosítóját. Például: BrittaSimon@contoso.com

    A fiókadatok szakasznak ekkor a következőhöz hasonlóan kell kinéznie:  
    ![Az elkészült fiók adatai szakasz](media/highgear-tutorial/finished-account-info-section.png)

7. A névjegy mentéséhez kattintson a panel alján található **Save (Mentés** ) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a HighGear csempére kattint, automatikusan be kell jelentkeznie arra a HighGear, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)