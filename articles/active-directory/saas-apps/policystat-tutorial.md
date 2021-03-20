---
title: 'Oktatóanyag: Azure Active Directory integráció a PolicyStat-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és PolicyStat között.
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
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893068"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Oktatóanyag: Azure Active Directory integráció a PolicyStat

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PolicyStat a Azure Active Directory (Azure AD) szolgáltatással.
A PolicyStat és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a PolicyStat.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a PolicyStat (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció PolicyStat való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* PolicyStat egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A PolicyStat támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A PolicyStat **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-policystat-from-the-gallery"></a>PolicyStat hozzáadása a gyűjteményből

A PolicyStat Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PolicyStat a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha PolicyStat szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **PolicyStat** kifejezést, válassza a **PolicyStat** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PolicyStat az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az PolicyStat-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a PolicyStat kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés PolicyStat való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[PolicyStat egyszeri bejelentkezés konfigurálása](#configure-policystat-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
4. **[Hozzon létre PolicyStat-teszt felhasználót](#create-policystat-test-user)** – hogy a PolicyStat Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés PolicyStat való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PolicyStat** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![PolicyStat tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.policystat.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a PolicyStat ügyfélszolgálati [csapatához](http://www.policystat.com/support/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

5. A PolicyStat-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A "felhasználó attribútumai" párbeszédpanelt megjelenítő képernyőkép, amelyben a "szerkesztés" ikon van kiválasztva.](common/edit-attribute.png)

6. A fentieken kívül a PolicyStat alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name | Forrás attribútum |
    |------------------- | -------------------- |
    | UID | ExtractMailPrefix ([mail]) |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.
    
    ![Képernyőfelvétel: a "felhasználói jogcímek" szakasz az "új jogcím hozzáadása" és a "Mentés" művelet kiemelve.](common/new-save-attribute.png)

    ![Képernyőkép: "a felhasználói jogcímek kezelése" párbeszédpanel a "név", "transzformáció" és "paraméter" szövegmezők kiemelésével és a "Mentés" gomb kiválasztásával.](./media/policystat-tutorial/attribute01.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza ki a forrást **átalakításként**.

    e. Az **átalakítás** listából írja be az adott sorhoz megjelenő attribútum értékét.
    
    f. Az **1. paraméter** listából írja be az adott sorhoz megjelenő attribútum értékét.

    : Kattintson a **Mentés** gombra.

7. A **PolicyStat beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-policystat-single-sign-on"></a>PolicyStat egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a PolicyStat vállalati webhelyre rendszergazdaként.

2. Kattintson a **felügyelet lapra,** majd kattintson az **egyszeres Sign-On konfiguráció** elemre a bal oldali navigációs ablaktáblán.
   
    ![Rendszergazdai menü](./media/policystat-tutorial/ic808633.png "Rendszergazdai menü")

3. Kattintson a **identitásszolgáltató-metaadatokra**, majd a **identitásszolgáltató-metaadatok** szakaszban hajtsa végre a következő lépéseket:
   
    ![A "saját I D P metaadatok" műveletet megjelenítő képernyőkép.](./media/policystat-tutorial/ic808636.png "Egy Sign-On konfiguráció")
   
    a. Nyissa meg a letöltött metaadat-fájlt, másolja a tartalmat, majd illessze be a **saját identitás-szolgáltató metaadatok** szövegmezőbe.

    b. Kattintson a **Save Changes** (Módosítások mentése) gombra.

4. Kattintson az **attribútumok konfigurálása** elemre, majd az **attribútumok konfigurálása** szakaszban hajtsa végre a következő lépéseket:
   
    a. A **username attribútum** szövegmezőbe írja be a következőt: **UID**.

    b. Az **Utónév attribútum** szövegmezőbe írja be az első név attribútum-jogcím nevét az Azure-ból **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. A **vezetéknév attribútum** szövegmezőbe írja be a vezetékneve nevét az Azure-ból **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. Az **e-mail-attribútum** szövegmezőbe írja be az e-mail-attribútum jogcím nevét az Azure-ból **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

5. A **telepítés** szakaszban válassza az **egyszeri bejelentkezéses integráció engedélyezése** lehetőséget.
   
    ![Egy Sign-On konfiguráció](./media/policystat-tutorial/ic808634.png "Egy Sign-On konfiguráció")


### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti saját fiókja számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a PolicyStat.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **PolicyStat** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PolicyStat** lehetőséget.

    ![Az PolicyStat hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza ki a fiókot a felhasználók listájában, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-policystat-test-user"></a>PolicyStat-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a PolicyStat-ben. A PolicyStat támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a PolicyStat-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a PolicyStat által biztosított PolicyStat felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a PolicyStat csempére kattint, automatikusan be kell jelentkeznie arra a PolicyStat, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)