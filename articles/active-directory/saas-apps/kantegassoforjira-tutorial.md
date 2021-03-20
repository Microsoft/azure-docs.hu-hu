---
title: 'Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a JIRA-hez | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a Kantega SSO között a JIRA számára.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 9643d0e63e85a9b500021a415e3cdaf3edc756c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608731"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a JIRA-hez

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JIRA Kantega SSO-t a Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel való JIRA-Kantega SSO-integráció az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a JIRA Kantega SSO-hoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a JIRA (egyszeri bejelentkezés) Kantega SSO-ra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a JIRA Kantega SSO-val szeretné konfigurálni, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kantega SSO JIRA egyszeri bejelentkezésre alkalmas előfizetés esetén

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Kantega SSO for JIRA támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Kantega SSO hozzáadása a JIRA a katalógusból

Ahhoz, hogy a JIRA-hez készült Kantega SSO-t az Azure AD-be konfigurálja, hozzá kell adnia a Kantega SSO-t a JIRA a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Kantega SSO-t szeretne hozzáadni a JIRA a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KANTEGA SSO az JIRA-hez**, válassza a **Kantega SSO elemet a JIRA** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO az JIRA az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a JIRA Kantega SSO-hoz a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolat szükséges a JIRA Kantega SSO-hoz.

Az Azure AD egyszeri bejelentkezés és a JIRA Kantega SSO használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[KANTEGA SSO konfigurálása a JIRA egyszeri bejelentkezéshez](#configure-kantega-sso-for-jira-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre KANTEGA SSO-t a JIRA-teszt felhasználója](#create-kantega-sso-for-jira-test-user)** számára – ha a felhasználó Azure ad-képviseletéhez kapcsolódó JIRA, a Kantega SSO-hoz tartozó Britta
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a JIRA Kantega SSO-val szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/) **KANTEGA SSO for JIRA** Application Integration lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű S L konfiguráció" elemet mutatja az "azonosító" és a "válasz U R L" szövegmezőben, és a "Mentés" gomb van kiválasztva.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Kantega egyszeri bejelentkezés JIRA tartományhoz és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. Ezek az értékek a JIRA beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **KANTEGA SSO beállítása JIRA** szakaszban másolja ki a megfelelő URL-címeket (ek) a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Kantega egyszeri bejelentkezés konfigurálása a JIRA-hez egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be a JIRA a helyszíni kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![A legördülő menüben kiválasztott "fogaskerék" ikont és "bővítményeket" megjelenítő képernyőkép.](./media/kantegassoforjira-tutorial/addon1.png)

1. A Bővítmények lap beállítások területén kattintson az **új bővítmények keresése** elemre. Keressen **KANTEGA SSO-t a JIRA (SAML & Kerberos)** elemre, majd kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Képernyőkép: az "új bővítmények keresése" szakasz "Kantego S S O for JIRA (S A S & A Kerberos)" szakaszában, a "telepítés" gomb kiválasztásával.](./media/kantegassoforjira-tutorial/addon2.png)

1. Elindul a beépülő modul telepítése.

    ![Képernyőkép, amely a "telepítés" párbeszédpanelt mutatja.](./media/kantegassoforjira-tutorial/addon3.png)

1. A telepítés befejezését követően. Kattintson a **Bezárás** gombra.

    ![Képernyőkép, amely a "telepített és kész" állapotot mutatja. párbeszédablak a "Bezárás" művelettel kiválasztva.](./media/kantegassoforjira-tutorial/addon33.png)

1.  Kattintson a **Kezelés** gombra.

    ![Képernyőfelvétel: a "kezelés" gomb kiválasztásával megjelenített "Kantega S S O" alkalmazás lapja.](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Az új beépülő modult az **integrációk** területen találja. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Képernyőfelvétel: a bal oldali navigációs menüben az "INTEGRÁCIÓk" felirat jelenik meg, a "Konfigurálás" gomb pedig a "bővítmények kezelése" szakaszban van kiválasztva.](./media/kantegassoforjira-tutorial/addon35.png)

1. Az **SAML** szakaszban. Válassza az **Azure Active Directory (Azure ad)** elemet az **identitás-szolgáltató hozzáadása** legördülő listából.

    ![A "személyazonosság-szolgáltató hozzáadása" legördülő lista "Azure Active Directory (az Azure A D-t)" beállítását megjelenítő képernyőkép.](./media/kantegassoforjira-tutorial/addon4.png)

1. Válassza az előfizetési szint **alapszintű** lehetőséget.

    ![Képernyőkép: "alapszintű", "alapszintű", "az Azure A D" szakaszának előkészítése.](./media/kantegassoforjira-tutorial/addon5.png)

1. Az **alkalmazás tulajdonságai** szakaszban hajtsa végre a következő lépéseket: 

    ![Képernyőkép, amely az "alkalmazás tulajdonságai" szakaszt jeleníti meg az "app I D R L" szövegmezővel és a másolás gomb kiválasztásával, valamint a "Next" (tovább) gombra kattintva.](./media/kantegassoforjira-tutorial/addon6.png)

    1. Másolja az **alkalmazás-azonosító URI** -értékét, és használja **azonosítóként, válasz URL-címként és Sign-On URL-címként** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    1. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre a következő lépéseket: 

    ![A "Metaadatok importálása" szakaszt a "metaadatok a számítógépen" elemre kiválasztva képernyőkép jelenik meg.](./media/kantegassoforjira-tutorial/addon7.png)

    1. Válassza ki a **metaadatokat a számítógépen**, és töltse fel a metaadat-fájlt, amelyet a Azure Portalról töltött le.

    1. Kattintson a **Tovább** gombra.

1. A **név és az SSO hely** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a "név és S S O-hely" a "személyazonosság-szolgáltató neve" szövegmezővel és a "tovább" gomb kiválasztásával jelenik meg.](./media/kantegassoforjira-tutorial/addon8.png)

    1. Adja hozzá az Identitáskezelő nevét a **személyazonosság-szolgáltató neve** szövegmezőben (például Azure ad).

    1. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább** gombra.

    ![A "Next" gomb kiválasztásával megjelenő "aláírás-ellenőrzés" szakaszt bemutató képernyőkép.](./media/kantegassoforjira-tutorial/addon9.png)

1. A **JIRA felhasználói fiókok** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: "JIRA felhasználói fiókok" a "felhasználók létrehozása a JIRA belső könyvtárában, ha szükséges" beállítás ki van választva, és a "tovább" gomb van kiválasztva.](./media/kantegassoforjira-tutorial/addon10.png)

    1. Ha szükséges, válassza a **felhasználók létrehozása a JIRA belső könyvtárában** lehetőséget, és írja be a csoport megfelelő nevét a felhasználók számára (több nem lehet. a csoportok vesszővel elválasztva).

    1. Kattintson a **Tovább** gombra.

1. Kattintson a **Finish** (Befejezés) gombra.

    ![A "Befejezés" gomb kiválasztásával megjelenő "Summary" (összefoglalás) szakaszt bemutató képernyőkép.](./media/kantegassoforjira-tutorial/addon11.png)

1. Az **Azure ad ismert tartományai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása](./media/kantegassoforjira-tutorial/addon12.png)

    1. Az oldal bal oldali paneljén válassza az **ismert tartományok** elemet.

    2. Adja meg a tartomány nevét az **ismert tartományok** szövegmezőben.

    3. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .

    1. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a JIRA Kantega SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Kantega egyszeri bejelentkezés JIRA** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Kantega egyszeri bejelentkezés JIRA** lehetőséget.

    ![Az Kantega SSO a JIRA hivatkozása az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kantega-sso-for-jira-test-user"></a>Kantega SSO létrehozása JIRA-teszt felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a JIRA, a JIRA kell kiépíteni őket. A JIRA-ben a Kantega SSO esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA a helyszíni kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![Képernyőfelvétel: a "fogaskerék" ikon ki van választva, és a "felhasználói kezelés" lehetőség ki van választva a legördülő listából.](./media/kantegassoforjira-tutorial/user1.png) 

1. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása** elemre.

    ![A "felhasználói kezelés" szakaszt tartalmazó képernyőkép, amely a "felhasználó létrehozása" gomb kiválasztásával jelenik meg.](./media/kantegassoforjira-tutorial/user2.png) 

1. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforjira-tutorial/user3.png) 

    1. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    2. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    3. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    4. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    5. Kattintson a **felhasználó létrehozása** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kantega SSO for JIRA csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO azon JIRA, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)