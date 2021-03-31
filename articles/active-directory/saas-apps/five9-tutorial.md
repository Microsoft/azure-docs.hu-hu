---
title: 'Oktatóanyag: Azure Active Directory integráció a Five9 Plus adapterrel (CTI, Contact Center Agents) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Five9 plusz adapter (CTI, Contact Center-ügynökök) között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: 68de5b11c131fe33252178ebecdeb9c3855fe239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453433"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Azure Active Directory integráció a Five9 Plus adapterrel (CTI, Contact Center Agents)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Five9 Plus adaptert (CTI, Contact Center Agents) a Azure Active Directory (Azure AD) szolgáltatással.
Az Five9 Plus adapter (CTI, Contact Center Agents) és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Five9 Plus adapterhez (CTI, Contact Center Agent).
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Five9 Plus adapterbe (CTI, Contact Center Agents) (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Five9 Plus-adapterrel (CTI, Contact Center-ügynökökkel) való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Five9 Plus adapter (CTI, Contact Center-ügynökök) egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Five9 Plus adapter (CTI, Contact Center Agents) támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 Plus-adapter (CTI, Contact Center Agents) hozzáadása a katalógusból

A Five9 Plus-adapter (CTI, Contact Center-ügynökök) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Five9 Plus adaptert (CTI, Contact Center Agents) a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhat hozzá Five9 Plus adaptert (CTI, Contact Center Agents) a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Five9 Plus adapter (CTI, Contact Center Agents)**, válassza ki a **Five9 Plus adaptert (CTI, Contact Center Agents)** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Five9 Plus adapter (CTI, Contact Center-ügynökök) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Five9 Plus adapterrel (CTI, Contact Center Agents) egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Five9 Plus adapter (CTI, Contact Center Agents) kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Five9 Plus adapterrel (CTI, Contact Center Agents) a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Five9 Plus adaptert (CTI, Contact Center Agents) egyszeri bejelentkezés](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálásához.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Five9 Plus-adapter (CTI, Contact Center-ügynökök) létrehozása – tesztelje a felhasználót](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** – hogy a felhasználó Azure ad-Britta kapcsolódó Five9 és-adapter (CTI, Contact Center Agents) partnere legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Five9 Plus-adapterrel (CTI, Contact Center Agent) való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Five9 Plus adapter (CTI, Contact Center Agents)** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![Five9 Plus-adapter (CTI, Contact Center-ügynökök) tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | "Five9 Plus adapter a Microsoft Dynamics CRM-hez" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | A "Five9 Plus adapter for zendesk" kifejezésre | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Az ügynök asztali eszközkészletének" Five9 Plus adapteréhez | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | "Five9 Plus adapter a Microsoft Dynamics CRM-hez" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | A "Five9 Plus adapter for zendesk" kifejezésre | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Az ügynök asztali eszközkészletének" Five9 Plus adapteréhez | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Five9 és-adapter beállítása (CTI, Contact Center ügynökök)** szakaszban másolja a megfelelő URL-címeket (ka) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Five9 plusz adapter (CTI, Contact Center Agents) konfigurálása egyetlen Sign-On

1. Ha be szeretné állítani az egyszeri bejelentkezést a **Five9 Plus-adapteren (CTI, Contact Center-ügynökök)** , el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-t (ka) t a [Five9 Plus adapter (CTI, Contact Center Agents) támogatási csapatának](https://www.five9.com/about/contact). Emellett az SSO további konfigurálásához kövesse az alábbi lépéseket az adapternek megfelelően:

    a. "Five9 Plus adapter az ügynök asztali eszközkészletéhez" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adapter a Microsoft Dynamics CRM-hez" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adapter a zendesk" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Five9 Plus adapterhez (CTI, Contact Center Agents).

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza az **Five9 plusz adapter (CTI, Contact Center ügynökök)** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Five9 plusz adapter (CTI, Contact Center ügynökök)** lehetőséget.

    ![Az Five9 plusz adapter (CTI, Contact Center-ügynökök) hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 plusz adapter létrehozása (CTI, Contact Center Agents) test User

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Five9 Plus adapterben (CTI, Contact Center Agents). A [Five9 Plus adapter (CTI, Contact Center Agents) támogatási csapatának](https://www.five9.com/about/contact) használata a felhasználók hozzáadásához a Five9 Plus adapter (CTI, Contact Center Agents) platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Five9 Plus adapterre (CTI, Contact Center-ügynökök csempére) kattint, automatikusan be kell jelentkeznie a Five9 Plus adapterbe (CTI, Contact Center Agents), amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)