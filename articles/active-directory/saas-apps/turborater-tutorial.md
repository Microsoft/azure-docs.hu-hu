---
title: 'Oktatóanyag: Azure Active Directory integráció a TurboRater-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TurboRater között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 7483e7a9300088d07e85d238ad897522d59c5464
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519384"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Oktatóanyag: Azure Active Directory integráció a TurboRater

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TurboRater a Azure Active Directory (Azure AD) szolgáltatással.

A TurboRater és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a TurboRater.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a TurboRater (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való szolgáltatott szoftverek (SaaS) alkalmazással kapcsolatos részletekért tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció TurboRater való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Egyszeri bejelentkezést használó TurboRater-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

A TurboRater támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>TurboRater hozzáadása az Azure Marketplace-ről

A TurboRater Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TurboRater az Azure Marketplace-ből a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások lehetőség](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **+ új alkalmazás** elemet a panel tetején.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **TurboRater** kifejezést. A keresési eredmények között válassza a **TurboRater** lehetőséget, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![TurboRater az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az TurboRater-mel konfigurálja és teszteli a  **B Simon** nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a TurboRater-ben.

Az Azure AD egyszeri bejelentkezés TurboRater való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on)** engedélyezheti a felhasználók számára a funkció használatát.
1. **[Konfigurálja az TurboRater egyszeri bejelentkezést](#configure-turborater-single-sign-on)** az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre egy TurboRater-teszt felhasználót](#create-a-turborater-test-user)** , hogy egy b. Simon nevű felhasználó legyen a TurboRater, aki a b. Simon nevű Azure ad-felhasználóhoz van társítva.
1. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés TurboRater való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **TurboRater** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépéseket:

    ![TurboRater tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    1. Az **azonosító (entitás azonosítója)** mezőben adjon meg egy URL-címet:

       `https://www.itcdataservices.com`

    1. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** mezőben adjon meg egy URL-címet a következő minta használatával:

       | Környezet | URL-cím |
       | ---------------| --------------- |
       | Tesztelés  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Élő  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez forduljon a [TurboRater támogatási csapatához](https://www.getitc.com/support). A Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintázatokat is megtekintheti.

1. Az **egyszeri Sign-On beállítása az SAML** -panellel lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget, hogy letöltse az **összevonási metaadatok XML-** fájlját a megadott beállításokból, és mentse a számítógépre.

    ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **TurboRater beállítása** szakaszban másolja ki a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezési URL-cím**
   * **Azure AD-azonosító**
   * **Kijelentkezési URL-cím**

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>TurboRater egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés az TurboRater oldalon való konfigurálásához el kell küldenie a letöltött összevonási metaadatok XML-jét és a Azure Portal megfelelő másolt URL-címeket a [TurboRater támogató csapatnak](https://www.getitc.com/support). A TurboRater-csoport gondoskodik arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**    >  **felhasználók**  >  **minden felhasználó** lehetőséget.

    ![A felhasználók és a "minden felhasználó" lehetőség](common/users.png)

1. A képernyő felső részén válassza az **+ új felhasználó** lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    ![A felhasználó panel](common/user-properties.png)

    1. A név mezőbe írja be a **BSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BSimon \@ \<yourcompanydomain> . \<extension>**. Például **BSimon \@ contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a TurboRater.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **TurboRater** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **TurboRater** lehetőséget.

    ![TurboRater az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktábla **kezelés** területén válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **B. Simon** elemet a **felhasználók** listán, majd válassza a **kiválasztás** elemet a panel alján.

1. Ha az SAML-állításban a szerepkör értékét várja, akkor a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. A panel alján válassza a **kiválasztás** lehetőséget.

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-turborater-test-user"></a>TurboRater-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a TurboRater-ben. Működjön együtt a [TurboRater támogatási csapatával](https://www.getitc.com/support) , és adja hozzá a B. Simon felhasználót a TurboRater-ben. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a **TurboRater** lehetőséget a saját alkalmazások portálján, automatikusan be kell jelentkeznie a TurboRater-előfizetésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](./tutorial-list.md)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

* [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)