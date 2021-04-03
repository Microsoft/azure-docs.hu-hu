---
title: 'Oktatóanyag: Azure Active Directory integráció a LearnUpon-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és LearnUpon között.
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
ms.openlocfilehash: 297395fbd0a7fd3fc8959eed5c0aeabd7b8e5751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458687"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Oktatóanyag: Azure Active Directory integráció a LearnUpon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a LearnUpon a Azure Active Directory (Azure AD) szolgáltatással.
A LearnUpon és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a LearnUpon.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a LearnUpon (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció LearnUpon való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* LearnUpon egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.


* A LearnUpon támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A LearnUpon **csak időben támogatja a** felhasználók kiépítési folyamatát


## <a name="adding-learnupon-from-the-gallery"></a>LearnUpon hozzáadása a gyűjteményből

A LearnUpon Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LearnUpon a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha LearnUpon szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LearnUpon** kifejezést, válassza a **LearnUpon** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![LearnUpon az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az LearnUpon-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a LearnUpon kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés LearnUpon való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[LearnUpon egyszeri bejelentkezés konfigurálása](#configure-learnupon-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre LearnUpon-teszt felhasználót](#create-learnupon-test-user)** – hogy a LearnUpon Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés LearnUpon való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **LearnUpon** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![LearnUpon tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-reply.png)

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a LearnUpon ügyfélszolgálati [csapatához](https://www.learnupon.com/features/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri Sign-On beállítása az SAML-vel** lapon keresse meg az **ujjlenyomatot** – a rendszer hozzáadja a LearnUpon SAML-beállításokhoz.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **LearnUpon beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-learnupon-single-sign-on"></a>LearnUpon egyetlen Sign-On konfigurálása

1. Nyisson meg egy másik böngésző-példányt, és jelentkezzen be a LearnUpon-be egy rendszergazdai fiókkal.

1. Kattintson a **Beállítások** fülre.

    ![Képernyőfelvétel: a beállítások lap.](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kattintson az **egyszeri bejelentkezés – SAML** elemre, majd kattintson az **általános beállítások** elemre az SAML-beállítások konfigurálásához.
   
    ![Képernyőfelvétel: egyszeri bejelentkezés – az SAML kiválasztott általános beállításokkal.](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Az **általános beállítások** szakaszban hajtsa végre a következő lépéseket:
   
    ![Képernyőfelvétel: az általános beállítások szakasz, ahol megadhatja a leírt értékeket.](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Válassza az **Engedélyezve** lehetőséget.

    b. Válasszon **verziót** **2,0**-ként.

    c. Válassza a **nem** lehetőséget a **feltételek kihagyása** lehetőségre.

    d. Az **SAML-jogkivonat post params neve** szövegmezőbe írja be a kérelem post paraméterét a fent jelzett SAML fogyasztói URL-címre, amely tartalmazza az ellenőrizni és HITELESÍTENI kívánt SAML-jogkivonatot (például **SAMLResponse**).

    e. A **név azonosítójának formázása** szövegmezőbe írja be azt az értéket, amely azt jelzi, hogy az SAML-azonosító hol található a felhasználók azonosítója (e-mail-cím), például: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. A **szolgáltató helyének azonosítása** szövegmezőbe írja be azt az értéket, amely megadja, hogy a rendszer hová küldje a felhasználókat, ha a feltöltött ikonra kattint a Azure Portal bejelentkezési képernyőjén.
  
    : A kijelentkezési **URL-cím** szövegmezőbe illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    h. Kattintson az **ujjas nyomatok kezelése** elemre, majd töltse fel a letöltött tanúsítvány Finger printjét.

1. Kattintson a **felhasználói beállítások** elemre, majd hajtsa végre a következő lépéseket:

     ![Képernyőfelvétel: a felhasználói beállítások szakasz, ahol megadhatja a leírt értékeket.](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Az **Utónév formázása** szövegmezőbe írja be azt az értéket, amely megadja, hogy hol található az SAML-azonosító a felhasználók FirstName-ben – például: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. A **vezetéknév-azonosító formázása** szövegmezőbe írja be azt az értéket, amely megadja, hogy hol található az SAML-alkalmazásban a felhasználók LastName (például: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` ).

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a LearnUpon hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **LearnUpon** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **LearnUpon** lehetőséget.

    ![Az LearnUpon hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-learnupon-test-user"></a>LearnUpon-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a LearnUpon-ben. A LearnUpon támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a LearnUpon-ben, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [LearnUpon támogatási csapatával](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a LearnUpon csempére kattint, automatikusan be kell jelentkeznie arra a LearnUpon, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)