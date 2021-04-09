---
title: 'Oktatóanyag: Azure Active Directory integráció a TOPdesk – nyilvános | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TOPdesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520438"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Oktatóanyag: Azure Active Directory integráció a TOPdesk-vel – nyilvános

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TOPdesk-t a Azure Active Directory (Azure AD) szolgáltatással.
A TOPdesk integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TOPdesk.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a TOPdesk (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a TOPdesk-mel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* TOPdesk – nyilvános egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* TOPdesk – az **SP** által kezdeményezett SSO-t támogatja

## <a name="adding-topdesk---public-from-the-gallery"></a>Nyilvános TOPdesk hozzáadása a katalógusból

A TOPdesk és az Azure AD integrálásának konfigurálásához hozzá kell adnia a TOPdesk-Public elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha TOPdesk szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TOPdesk-Public** kifejezést, válassza a **TOPdesk-Public** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![TOPdesk – nyilvános az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a TOPdesk-Public alapján egy **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a TOPdesk-nyilvános felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés TOPdesk-nyilvános használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[TOPdesk konfigurálása – nyilvános egyszeri bejelentkezés](#configure-topdesk---public-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[TOPdesk létrehozása – nyilvános tesztelési felhasználó](#create-topdesk---public-test-user)** – a TOPdesk-Public Britta, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az TOPdesk-Public használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **TOPdesk-Public** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4.  Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, hajtsa végre a következő lépéseket:

    >[!NOTE]
    >A **szolgáltatói metaadatokat tartalmazó fájlt** a **TOPdesk konfigurálása – nyilvános egyszeri bejelentkezés** szakasz ismerteti, amely az oktatóanyag későbbi részében is megtalálható.

    a. Kattintson a **metaadat-fájl feltöltése** elemre.
    
    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer az alapszintű SAML-konfiguráció szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket.

    ![TOPdesk – nyilvános tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    d. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net`

    e. Az **azonosító URL-címe** szövegmezőbe írja be a TOPdesk metaadat URL-címét, amelyet a TOPdesk-konfigurációból kérhet le. A következő mintát kell használnia: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Ha az **azonosító** és a **Válasz URL-címe** nem lesz automatikusan feltöltve, manuálisan kell megadnia azokat. Az azonosító esetében kövesse a fentiekben említett mintát, és a válasz URL-cím értékének **megadásával adja meg a TOPdesk-nyilvános egyszeri bejelentkezés** szakaszát, amelyet az oktatóanyag későbbi részében talál. A **bejelentkezési URL-cím** értéke nem valódi, ezért frissítenie kell az értéket a tényleges Sign-On URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [TOPdesk – nyilvános ügyfél-támogatási csapattal](https://help.topdesk.com/saas/enterprise/user/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **TOPdesk-Public beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-topdesk---public-single-sign-on&quot;></a>TOPdesk konfigurálása – egyetlen Sign-On

1. Jelentkezzen be a **TOPdesk-nyilvános** vállalati webhelyre rendszergazdaként.

2. A **TOPdesk** menüben kattintson a **Beállítások** elemre.
   
    ![Beállítások](./media/topdesk-public-tutorial/ic790598.png &quot;Beállítások")

3. Kattintson a **bejelentkezési beállítások** elemre.
   
    ![Bejelentkezési beállítások](./media/topdesk-public-tutorial/ic790599.png "Bejelentkezési beállítások")

4. Bontsa ki a **bejelentkezési beállítások** menüt, majd kattintson az **általános** elemre.
   
    ![Általános](./media/topdesk-public-tutorial/ic790600.png "Általános kérdések")

5. Az **SAML bejelentkezési** konfiguráció szakasz **nyilvános** részében hajtsa végre a következő lépéseket:
   
    ![Technikai beállítások](./media/topdesk-public-tutorial/ic790601.png "Technikai beállítások")
   
    a. Kattintson a **Letöltés** gombra a nyilvános metaadat-fájl letöltéséhez, majd mentse helyileg a számítógépén.
   
    b. Nyissa meg a letöltött metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópontot.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Másolja a **AssertionConsumerService** értéket, illessze be ezt az értéket a **Válasz URL-címe** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszban.      
   
6. Tanúsítványfájl létrehozásához hajtsa végre a következő lépéseket:
    
    ![Tanúsítvány](./media/topdesk-public-tutorial/ic790606.png "Tanúsítvány")
    
    a. Nyissa meg a letöltött metaadat-fájlt a Azure Portal.
    
    b. Bontsa ki a **securitytokenservicetype** csomópontot, amely egy **xsi: Type** of **Fed: ApplicationServiceType**.
    
    c. Másolja a **x509** csomópont értékét.
    
    d. Mentse a másolt **x509** -értéket helyileg a számítógépen egy fájlba.

7. A **nyilvános** szakaszban kattintson a **Hozzáadás** gombra.
    
    ![SAML-bejelentkezés](./media/topdesk-public-tutorial/ic790625.png "SAML-bejelentkezés")

8. A **SAML konfigurációs segéd** párbeszédpanelen hajtsa végre a következő lépéseket:
    
    ![SAML-konfigurációs segéd](./media/topdesk-public-tutorial/ic790608.png "SAML-konfigurációs segéd")
    
    a. A letöltött metaadat-fájl Azure Portalból való feltöltéséhez az **összevonási metaadatok** területen kattintson a **Tallózás** gombra.

    b. A tanúsítványfájl feltöltéséhez a **tanúsítvány (RSA)** alatt kattintson a **Tallózás** gombra.

    c. Ha fel szeretné tölteni a TOPdesk támogatási csapatának emblémáját, kattintson a **logo ikon** alatt található **Tallózás** gombra.

    d. A **Felhasználónév attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. A **megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a nyilvános TOPdesk.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **TOPdesk-Public** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **TOPdesk-Public** elemet.

    ![TOPdesk – nyilvános hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-topdesk---public-test-user&quot;></a>TOPdesk létrehozása – nyilvános teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a TOPdesk-Nyilvánosba, a TOPdesk-nyilvánosságnak kell kiépíteni őket. TOPdesk esetén a kiépítés manuális feladat.

### <a name=&quot;to-configure-user-provisioning-perform-the-following-steps&quot;></a>A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk-nyilvános** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk \> új \> támogatási fájlok \> személy** elemre.
   
    ![Személy](./media/topdesk-public-tutorial/ic790628.png &quot;Személy")

3. Az új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új személy](./media/topdesk-public-tutorial/ic790629.png "Új személy")
   
    a. Kattintson az Általános lapra.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon
 
    c. Válasszon egy **helyet** a fiókhoz.
 
    d. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a TOPdesk által biztosított TOPdesk-nyilvános felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a TOPdesk-nyilvános csempére kattint, akkor automatikusan be kell jelentkeznie arra a TOPdesk, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)