---
title: 'Oktatóanyag: Azure Active Directory integráció az IBM Kenexa Survey Enterprise szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az IBM Kenexa Survey Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459150"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Oktatóanyag: Azure Active Directory integráció az IBM Kenexa Survey Enterprise-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az IBM Kenexa Survey Enterprise Azure Active Directory (Azure AD) szolgáltatással.
Az IBM Kenexa Survey Enterprise és az Azure AD integrálásával a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-t, aki hozzáfér az IBM Kenexa Survey Enterprise szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az IBM Kenexa Survey Enterprise (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az IBM Kenexa Survey Enterprise-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* IBM Kenexa Survey Enterprise egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az IBM Kenexa Survey Enterprise támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Az IBM Kenexa Survey Enterprise hozzáadása a katalógusból

Az IBM Kenexa Survey Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az IBM Kenexa Survey Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az IBM Kenexa Survey Enterprise a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **IBM Kenexa Survey Enterprise** kifejezést, válassza az **IBM Kenexa Survey Enterprise** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az IBM Kenexa Survey Enterprise az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az IBM Kenexa Survey Enterprise szolgáltatással konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az IBM Kenexa Survey Enterprise-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az IBM Kenexa Survey Enterprise használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az IBM Kenexa Survey Enterprise egyszeri bejelentkezést](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálásához.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozza létre az IBM Kenexa Survey Enterprise test User](#create-ibm-kenexa-survey-enterprise-test-user)** -t, hogy a Britta Simon partnere legyen az IBM Kenexa Survey Enterprise-ban, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az IBM Kenexa Survey Enterprise szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **IBM Kenexa Survey Enterprise** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![Az IBM Kenexa Survey Enterprise domain és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://surveys.kenexa.com/<companycode>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez vegye fel a kapcsolatot az [IBM Kenexa Survey Enterprise ügyfél-támogatási csapatával](https://www.ibm.com/support/home/?lnk=fcw) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az IBM Kenexa Survey Enterprise alkalmazás egy adott formátumban szeretné megkapni a biztonsági azonosítók Markup Language (SAML) állítását, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. A válaszban szereplő felhasználóazonosító-jogcím értékének meg kell egyeznie a Kenexa rendszeren konfigurált SSO-AZONOSÍTÓval. A megfelelő felhasználói azonosítónak a szervezeten belüli, SSO Internet Datagram Protocol (IDENTITÁSSZOLGÁLTATÓ) használatával történő leképezéséhez működjön együtt az [IBM Kenexa Survey Enterprise támogatási csapatával](https://www.ibm.com/support/home/?lnk=fcw).

    Alapértelmezés szerint az Azure AD az egyszerű felhasználónév (UPN) értékként állítja be a felhasználói azonosítót. Ezt az értéket a **felhasználói attribútumok** lapon módosíthatja, ahogy az alábbi képernyőképen is látható. Az integráció csak a leképezés megfelelő befejezése után működik.

    ![image](common/edit-attribute.png)

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **IBM Kenexa-felmérési vállalat beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Az IBM Kenexa Survey Enterprise Single Sign-On konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést az **IBM Kenexa Survey Enterprise** oldalon, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket Azure Portalról az [IBM Kenexa Survey Enterprise támogatási csapatához](https://www.ibm.com/support/home/?lnk=fcw). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az IBM Kenexa Survey Enterprise elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **IBM Kenexa Survey Enterprise** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **IBM Kenexa Survey Enterprise** elemet.

    ![Az IBM Kenexa Survey Enterprise hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise test User létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az IBM Kenexa Survey Enterprise-ban.

Az IBM Kenexa Survey Enterprise rendszerbeli felhasználók létrehozásához és az egyszeri bejelentkezés AZONOSÍTÓjának hozzárendeléséhez használhatja az [IBM Kenexa Survey Enterprise támogatási csapatát](https://www.ibm.com/support/home/?lnk=fcw). Az egyszeri bejelentkezési azonosító értékét az Azure AD felhasználói azonosító értékére is le kell rendelni. Ez az alapértelmezett beállítás az **attribútum** lapon módosítható.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az IBM Kenexa Survey Enterprise csempére kattint, automatikusan be kell jelentkeznie az IBM Kenexa Survey Enterprise szolgáltatásba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)