---
title: 'Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a bitbucket-hez | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a Kantega SSO között a bitbucket számára.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099214"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a bitbucket-hez

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a bitbucket Kantega SSO-t a Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel való bitbucket-Kantega SSO-integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a bitbucket Kantega SSO-hoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a bitbucket (egyszeri bejelentkezés) Kantega SSO-ra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a bitbucket Kantega SSO-val szeretné konfigurálni, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kantega SSO bitbucket egyszeri bejelentkezésre alkalmas előfizetés esetén

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Kantega SSO for bitbucket támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Kantega SSO hozzáadása a bitbucket a katalógusból

Ahhoz, hogy a bitbucket-hez készült Kantega SSO-t az Azure AD-be konfigurálja, hozzá kell adnia a Kantega SSO-t a bitbucket a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Kantega SSO-t szeretne hozzáadni a bitbucket a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KANTEGA SSO az bitbucket-hez**, válassza a **Kantega SSO elemet a bitbucket** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO az bitbucket az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a bitbucket Kantega SSO-hoz a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolat szükséges a bitbucket Kantega SSO-hoz.

Az Azure AD egyszeri bejelentkezés és a bitbucket Kantega SSO használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[KANTEGA SSO konfigurálása az bitbucket egyszeri bejelentkezéshez](#configure-kantega-sso-for-bitbucket-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre KANTEGA SSO-t a bitbucket-teszt felhasználója](#create-kantega-sso-for-bitbucket-test-user)** számára – ha a felhasználó Azure ad-képviseletéhez kapcsolódó bitbucket, a Kantega SSO-hoz tartozó Britta
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a bitbucket Kantega SSO-val szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/) **Kantega SSO for bitbucket** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Kantega egyszeri bejelentkezés bitbucket tartományhoz és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Kantega egyszeri bejelentkezés bitbucket tartományhoz és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezek az értékek a bitbucket beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **KANTEGA SSO beállítása bitbucket** szakaszban másolja ki a megfelelő URL-címeket (ek) a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Kantega SSO konfigurálása bitbucket egyszeri bejelentkezéshez

1. Egy másik böngészőablakban jelentkezzen be a bitbucket felügyeleti portálra rendszergazdaként.

1. Kattintson a fogaskerék elemre, majd az **új bővítmények keresése**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Keressen **KANTEGA SSO-t a BITBUCKET SAML & Kerberos-hez** , és kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Elindul a beépülő modul telepítése.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. A telepítés befejezését követően. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. Az **SAML** szakaszban. Válassza az **Azure Active Directory (Azure ad)** elemet az **identitás-szolgáltató hozzáadása** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Válassza az előfizetési szint **alapszintű**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Az **alkalmazás tulajdonságai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Másolja az **alkalmazás-azonosító URI** -értékét, és használja **azonosítóként, válasz URL-címként és bejelentkezési URL-címként** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Válassza ki a **metaadatokat a számítógépen**, és töltse fel a metaadat-fájlt, amelyet a Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A **név és az SSO hely** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Adja hozzá az Identitáskezelő nevét a **személyazonosság-szolgáltató neve** szövegmezőben (például Azure ad).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. A **bitbucket felhasználói fiókok** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Ha szükséges, válassza a **felhasználók létrehozása a bitbucket belső könyvtárában** lehetőséget, és írja be a csoport megfelelő nevét a felhasználók számára (több nem lehet. a csoportok vesszővel elválasztva).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Az **Azure ad ismert tartományai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Az oldal bal oldali paneljén válassza az **ismert tartományok** elemet.

    b. Adja meg a tartomány nevét az **ismert tartományok** szövegmezőben.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt:`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a bitbucket Kantega SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Kantega egyszeri bejelentkezés bitbucket**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Kantega egyszeri bejelentkezés bitbucket**lehetőséget.

    ![Az Kantega SSO a bitbucket hivatkozása az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Kantega SSO létrehozása bitbucket-teszt felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a bitbucket, a bitbucket kell kiépíteni őket. A bitbucket Kantega egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a bitbucket vállalati webhelyre rendszergazdaként.

1. Kattintson a Beállítások ikonra.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Az **Adminisztráció** lap szakaszban kattintson a **felhasználók**elemre.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kattintson a **felhasználó létrehozása**gombra.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. A **felhasználó létrehozása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló Brittasimon@contoso.come-mail címet.

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló Brittasimon@contoso.come-mail címet.

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. A **Jelszó megerősítése** szövegmezőbe írja be újra a felhasználó jelszavát.

    f. Kattintson a **felhasználó létrehozása**gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kantega SSO for bitbucket csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO azon bitbucket, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

