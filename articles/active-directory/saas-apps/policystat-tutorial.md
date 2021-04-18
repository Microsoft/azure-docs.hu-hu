---
title: 'Oktatóanyag: Azure Active Directory PolicyStat-integráció | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a PolicyStat között.
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
ms.openlocfilehash: 5c2520c8e209ab8319cbc5a369b70d247a52232c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600998"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Oktatóanyag: Azure Active Directory PolicyStat-integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja a PolicyStatt a Azure Active Directory (Azure AD) használatával.
A PolicyStat integrálása az Azure AD-val az alábbi előnyöket biztosítja:

* Az Azure AD-ban szabályozhatja, hogy ki férhet hozzá a PolicyStat szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkezzenek be a PolicyStat (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne tudni az SaaS-alkalmazások Azure [AD-val](../manage-apps/what-is-single-sign-on.md)való integrációjáról, tekintse meg a Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD És a PolicyStat integrációjának konfiguráláshoz a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nincs Azure AD-környezete, itt kaphat egy hónapos [próbaverziót](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat egyszeri bejelentkezést engedélyező előfizetés

> [!NOTE]
> Ez az integráció az Azure AD US Government Cloud-környezetből is használható. Ezt az alkalmazást az Azure AD US Government Cloud Application Galleryben találhatja meg, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* A PolicyStat támogatja **az SP** által kezdeményezett SSO-t

* A PolicyStat támogatja **az időponthoz időben való** felhasználóáthelyezést

## <a name="adding-policystat-from-the-gallery"></a>PolicyStat hozzáadása a katalógusból

A PolicyStat Azure AD-be való integrálásának konfigurálához hozzá kell adni a PolicyStat szolgáltatásokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A PolicyStat katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** bal oldali navigációs panelen kattintson a Azure Active Directory **ikonra.**

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás** lehetőséget.

    ![A Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **párbeszédpanel tetején** található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **PolicyStat** parancsot, válassza ki a **PolicyStat** gombra a találatok panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PolicyStat az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli a PolicyStat beállítással egy Britta Simon nevű **tesztfelhasználó alapján.**
Az egyszeri bejelentkezéshez kapcsolati kapcsolatot kell létrehozni egy Azure AD-felhasználó és a kapcsolódó felhasználó között a PolicyStat táblában.

Az Azure AD-beli egyszeri bejelentkezés PolicyStat használatával való konfiguráláshoz és teszteléshez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** – ezzel a funkcióval engedélyezheti a felhasználók számára.
2. **[Konfigurálja a PolicyStat egyszeri bejelentkezést](#configure-policystat-single-sign-on)** – az Sign-On konfigurálhatja az alkalmazásoldalon.
3. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti Britta Simonnak az Azure AD egyszeri bejelentkezés használatát.
4. **[Hozzon létre egy](#create-policystat-test-user)** PolicyStat-tesztfelhasználót, hogy Britta Simon egy megfelelője legyen a PolicyStat-ban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
5. **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD-beli egyszeri bejelentkezés a PolicyStat használatával való konfiguráláshoz hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PolicyStat alkalmazásintegráció** lapján válassza az **Egyszeri bejelentkezés lehetőséget.**

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az Egyszeri **bejelentkezési módszer** kiválasztása párbeszédpanelen válassza az **SAML/WS-Fed mód** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési módja](common/select-saml-option.png)

3. A Set up Single Sign-On with SAML (Egyetlen alkalmazás beállítása **SAML-sel)** lapon kattintson az **Edit** (Szerkesztés) ikonra az SamL Basic Configuration (SamL-alapkonfiguráció) **párbeszédpanel megnyitásához.**

    ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    ![A PolicyStat tartományra és URL-címekre vonatkozó egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<companyname>.policystat.com`

    b. Az **Azonosító (Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket az [értékeket a PolicyStat ügyféltámogatási](https://rldatix.com/services-support/support) csapattól kapják meg. Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

4. A **Set up Single Sign-On with SAML** (Egyszeri bejelentkezés beállítása SAML-tanúsítványsal) lapon, az **SAML signing Certificate (SAML-aláíró** tanúsítvány) szakaszban kattintson a **Download** (Letöltés) elemre az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

5. A PolicyStat alkalmazás egy adott formátumban várja az SAML helyességi feltételeket, amelyhez hozzá kell adni egyéni attribútumleképezéseket az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson **a Szerkesztés ikonra** a **Felhasználói attribútumok párbeszédpanel megnyitásához.**

    ![Képernyőkép a "Felhasználói attribútumok" párbeszédpanelről, a "Szerkesztés" ikonnal.](common/edit-attribute.png)

6. A fentiek mellett a PolicyStat alkalmazás arra számít, hogy néhány további attribútumot kell visszaosztanunk AZ SAML-válaszban. A Felhasználói **attribútumok**  párbeszédpanel Felhasználói jogcímek szakaszában hajtsa végre a következő lépéseket az SAML-jogkivonat attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Name | Forrásattribútum |
    |------------------- | -------------------- |
    | Uid | ExtractMailPrefix([mail]) |

    a. Kattintson **az Add new claim (Új jogcím hozzáadása)** elemre a Manage user claims **(Felhasználói jogcímek kezelése) párbeszédpanel megnyitásához.**
    
    ![Képernyőkép a "Felhasználói jogcímek" szakaszról, kiemelt "Új jogcím hozzáadása" és "Mentés" műveletekkel.](common/new-save-attribute.png)

    ![Képernyőkép a "Felhasználói jogcímek kezelése" párbeszédpanelről, kiemelt "Név", "Átalakítás" és "Paraméter" szövegmezővel, valamint a "Mentés" gombbal.](./media/policystat-tutorial/attribute01.png)

    b. A Név **szövegmezőbe** írja be az adott sorhoz látható attribútumnevet.

    c. Hagyja **üresen a névteret.**

    d. Válassza a Forrás **átalakítási lehetőséget.**

    e. Az Átalakítás **listából** írja be az adott sorhoz látható attribútumértéket.
    
    f. Az **1.** paraméter listából írja be az adott sorhoz látható attribútumértéket.

    : Kattintson a **Mentés** gombra.

7. A **PolicyStat beállítása szakaszban** másolja ki a megfelelő URL-cím(eke)t a saját követelményének megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-policystat-single-sign-on&quot;></a>A PolicyStat single Sign-On

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a PolicyStat vállalati webhelyre.

2. Kattintson **a Felügyelet lapra,** majd a bal Sign-On a Single **Sign-On Configuration** elemre.
   
    ![Rendszergazda menü](./media/policystat-tutorial/ic808633.png &quot;Rendszergazda menü")

3. Kattintson a Your IDP Metadata (Az **idP-metaadatok)** elemre, majd az IdP Metadata (Saját **idP-metaadatok) szakaszban** hajtsa végre a következő lépéseket:
   
    ![A kiválasztott "Az I D P-metaadatok" műveletet bemutató képernyőkép.](./media/policystat-tutorial/ic808636.png "Egyetlen Sign-On konfigurálása")
   
    a. Nyissa meg a letöltött metaadatfájlt, másolja ki a tartalmat, majd illessze be az **Identitásszolgáltatói metaadatok** szövegmezőbe.

    b. Kattintson a **Save Changes** (Módosítások mentése) gombra.

4. Kattintson **a Configure Attributes (Attribútumok konfigurálása)** elemre, majd a **Configure Attributes** (Attribútumok konfigurálása) szakaszban hajtsa végre a következő lépéseket:
   
    a. A Felhasználónév **attribútuma szövegmezőbe** írja be a **uid nevet.**

    b. Az First **Name Attribute (Első név attribútuma)** szövegmezőbe írja be az Azure first name attribute jogcímnevét. **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`**

    c. A **Vezetéknév attribútuma szövegmezőbe** írja be a Vezetéknév attribútum jogcímnevét az Azure-ból. **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`**

    d. Az **E-mail attribútum** szövegmezőbe írja be az E-mail attribútum jogcímének nevét az **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** Azure-ból.

    e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

5. A Beállítás **szakaszban** válassza az **Egyszeri bejelentkezés integrációjának engedélyezése lehetőséget.**
   
    ![Egy Sign-On konfigurálása](./media/policystat-tutorial/ic808634.png "Egyetlen Sign-On konfigurálása")


### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a saját fiókjának az Azure-beli egyszeri bejelentkezés használatát úgy, hogy hozzáférést biztosít a PolicyStat szolgáltatáshoz.

1. A Azure Portal válassza a Vállalati **alkalmazások** lehetőséget, válassza a **Minden alkalmazás** lehetőséget, majd válassza a **HázirendÁllapot lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **PolicyStat lehetőséget.**

    ![A PolicyStat hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása gombra,** majd válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés hozzáadása **párbeszédpanelen.**

    ![A Hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A Felhasználók **és csoportok** párbeszédpanelen válassza ki a  fiókját a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.

6. Ha bármilyen szerepkörértéket vár az SAML helyességi feltételben, akkor a Szerepkör kiválasztása párbeszédpanelen  válassza ki a felhasználónak megfelelő szerepkört a listából, majd kattintson a képernyő alján található Kijelölés gombra. 

7. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

### <a name="create-policystat-test-user"></a>PolicyStat tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hozunk létre a PolicyStat területen. A PolicyStat támogatja az alapértelmezés szerint engedélyezett, felhasználóhoz szükséges felhasználóáthelyezést. Ebben a szakaszban nem található műveletelem. Ha a felhasználó még nem létezik a PolicyStatban, a hitelesítés után létrejön egy új.

>[!NOTE]
>Az Azure AD felhasználói fiókok létrehozására bármely más PolicyStat felhasználóifiók-létrehozási eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel.

Amikor az alkalmazás hozzáférési panel PolicyStat csempére kattint, a rendszer automatikusan bejelentkezik a PolicyStatba, amelyhez beállította az SSO-t. További információ a [hozzáférési panel: Bevezetés a](../user-help/my-apps-portal-end-user-access.md)hozzáférési panel.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok listája az SaaS-alkalmazások Azure Active Directory](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
