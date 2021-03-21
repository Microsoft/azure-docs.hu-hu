---
title: 'Oktatóanyag: Azure Active Directory integráció a végtelen Kampusztal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a végtelen Campus között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460232"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Oktatóanyag: Azure Active Directory integráció a végtelen Kampusztal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a végtelen Kampuszt Azure Active Directory (Azure AD) használatával.
A végtelen kampusz és az Azure AD integrálásával a következő előnyöket nyújtja:

* Megadhatja az Azure AD-t, aki hozzáfér a végtelen kampuszhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a végtelen Kampuszba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a végtelen kampusz használatával való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Korlátlan egyetemi egyszeri bejelentkezésre alkalmas előfizetés
* Minimális követelmény, hogy Azure Active Directory rendszergazdának kell lennie, és a konfiguráció befejezéséhez a "Student Information System (SIS)" biztonsági szerepkörrel kell rendelkeznie.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A végtelen Campus támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-infinite-campus-from-the-gallery"></a>A végtelen Campus hozzáadása a katalógusból

A végtelen kampusz Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához a végtelen Campust.

**Ha a katalógusból szeretné hozzáadni a végtelen Campusot, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **végtelen Campus** kifejezést, válassza ki a **végtelen Campus** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Végtelen campus az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a végtelen campuson konfigurálja és teszteli a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség a végtelen campuson.

Az Azure AD egyszeri bejelentkezés a végtelen Campus használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[végtelen Campus egyszeri bejelentkezésének konfigurálása](#configure-infinite-campus-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre végtelen Campus test User](#create-infinite-campus-test-user)** -t, hogy a Britta Simon a végtelen Egyetemen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a végtelen kampusz használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **végtelen kampusz** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű SAML-konfiguráció szakaszban hajtsa végre a következő lépéseket (vegye figyelembe, hogy a tartomány a üzemeltetési modellel eltérő lesz, de a **teljes tartomány** értékének meg kell egyeznie a végtelen Campus-telepítéssel):

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Végtelen kampusz tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>A végtelen Campus egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a végtelen Kampuszra biztonsági rendszergazdaként.

2. A menü bal oldalán kattintson a **rendszerfelügyelet** elemre.

    ![A rendszergazda](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigáljon a **felhasználói biztonság**  >  **SAML-kezelési**  >  **egyszeri bejelentkezés szolgáltatás szolgáltatójának konfigurációjához**.

    ![Az SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Az SSO-szolgáltató **konfigurációja** lapon hajtsa végre a következő lépéseket:

    ![Az egyszeri bejelentkezés](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Válassza **az SAML egyszeri bejelentkezés engedélyezése** lehetőséget.

    b. Az **opcionális attribútum nevének** szerkesztése a **név** tárolására

    c. Az **identitásszolgáltató-kiszolgáló adatainak beolvasására szolgáló lehetőség kiválasztása** szakaszban válassza ki a **metaadatok URL-címét**, illessze be az alkalmazás- **összevonási metaadatok URL-címét** , amelyet a mezőben a Azure Portal másolt, majd kattintson a **szinkronizálás** elemre.

    d. A **szinkronizálás** gombra kattintás után automatikusan kitölti az **egyszeri bejelentkezés szolgáltatás szolgáltatójának konfigurációs** lapját. Ezek az értékek a fenti 4. lépésben látható értékeknek megfelelően ellenőrizhetők.

    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

> [!NOTE]
> Ha azt szeretné, hogy az összes Azure-felhasználó egyszeri bejelentkezéses hozzáférést biztosítson a végtelen kampuszhoz, és a hozzáférés szabályozása érdekében a végtelen kampusz belső engedélyek rendszerét használja, akkor az alkalmazás **felhasználó-hozzárendelés szükséges** tulajdonságát a nem értékre állíthatja, és kihagyhatja a következő lépéseket.

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a végtelen kampuszhoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **végtelen Campus** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **végtelen Campus** elemet.

    ![A végtelen Campus hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-infinite-campus-test-user"></a>Végtelen kampusz tesztelési felhasználó létrehozása

A végtelen Campus demográfiai központú architektúrával rendelkezik. Vegye fel a kapcsolatot a [végtelen Campus támogatási csapatával](mailto:sales@infinitecampus.com) , és vegye fel a felhasználókat a végtelen Campus platformba.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a végtelen kampusz csempére kattint, automatikusan be kell jelentkeznie arra a végtelen Kampuszra, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)