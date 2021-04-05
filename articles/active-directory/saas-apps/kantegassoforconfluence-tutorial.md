---
title: 'Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a torkolatánál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Kantega SSO között a torkolatánál.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646214"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a torkolatánál

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kantega SSO-t a Azure Active Directory (Azure AD) összefolyásánál.
Az Kantega SSO az Azure AD-vel való összevonáshoz való integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Kantega SSO-hoz a torkolatánál.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Kantega SSO-ba az Azure AD-fiókjával való összefolyásánál (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Kantega SSO-nal szeretné konfigurálni a torkolatánál, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kantega SSO a torkolatánál egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Kantega SSO a torkolatánál támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Kantega SSO hozzáadása a gyűjteményhez való összefolyásánál

A Kantega SSO Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a Kantega SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Kantega SSO-t szeretne hozzáadni a gyűjteményhez, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KANTEGA SSO for torkolatánál**, válassza a **Kantega SSO** lehetőséget az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO az eredmények listáján való összefolyásánál](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Kantega SSO-val a **Britta Simon** nevű teszt felhasználója alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítése szükséges a Kantega SSO-hoz.

Az Azure AD egyszeri bejelentkezés a Kantega SSO-val való összekapcsoláshoz való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[KANTEGA SSO konfigurálása a torkolatánál történő egyszeri bejelentkezéshez](#configure-kantega-sso-for-confluence-single-sign-on)** – az alkalmazás oldalának egyetlen Sign-On beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre KANTEGA SSO-t az összefolyásánál tesztelési felhasználó](#create-kantega-sso-for-confluence-test-user)** számára, hogy a Britta Simon partnere legyen a Kantega SSO-ban olyan összefolyásánál, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést Kantega SSO-val szeretné konfigurálni a torkolatánál, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Kantega SSO for torkolatánál** Application Integration oldalon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű S A M L konfiguráció" szakaszt jeleníti meg az "azonosító" és a "válasz U R L" mezők kiválasztásával és a "Mentés" gomb kiválasztásával.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Kantega SSO a torkolatánál tartomány és az URL-címek egyszeri bejelentkezési adataihoz](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. Ezek az értékek az összefolyásánál beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **KANTEGA SSO beállítása a torkolatánál** szakaszban másolja a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Kantega SSO konfigurálása egyetlen Sign-Onhoz

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az **összefolyásánál felügyeleti portálra** .

1. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![Képernyőfelvétel: a "fogaskerék" menü ikon és a "bővítmények" lehetőség van kiválasztva.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Az **ATLASSIAN-piactér** lapon kattintson az **új bővítmények keresése** elemre.

    ![Képernyőkép, amely a "ATTLASSIAN MARKETPLACE" fület jeleníti meg az "új bővítmények keresése" beállítással.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Keresse **meg a KANTEGA SSO-t az SAML-Kerberos összefolyásánál** , majd kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Képernyőkép: az "új bővítmények keresése" lap "Kantega S S O for összefolyásánál S A" M L Kerberos "a keresőmezőbe, és a" telepítés "gomb kiválasztva.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Elindul a beépülő modul telepítése.

    ![Képernyőfelvétel: a "telepítés" képernyőt bemutató képernyőkép.](./media/kantegassoforconfluence-tutorial/addon3.png)

1. A telepítés befejezését követően. Kattintson a **Bezárás** gombra.

    ![Képernyőfelvétel: a "telepített és kész" állapotú képernyő megjelenítése a "Bezárás" művelet kiválasztásával.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![A "kezelés" gomb kiválasztásával megjelenő képernyőkép, amely az "Kantega egyszeri bejelentkezés Kerberos-és S-L" beépülő modult jeleníti meg.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Képernyőfelvétel: a "configure" (Konfigurálás) gombbal kiválasztva az "Kantega egyszeri bejelentkezés Kerberos-és S-L" oldalát.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Ez az új beépülő modul a **felhasználók & biztonság** lapon is megtalálható.

    ![Képernyőfelvétel: a "felhasználók & biztonság" lap "Kantega egyszeri bejelentkezés" művelete van kiválasztva.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. Az **SAML** szakaszban. Válassza az **Azure Active Directory (Azure ad)** elemet az **identitás-szolgáltató hozzáadása** legördülő listából.

    ![Képernyőkép, amely megjeleníti az "S A M L" szakaszt a "személyazonosság-szolgáltató hozzáadása" és "Azure Active Directory (Azure AD)" beállítással.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Válassza az előfizetési szint **alapszintű** lehetőséget.

    ![Képernyőkép, amely az "alapszintű" kiválasztott "Azure AD" oldal előkészítését jeleníti meg.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Az **alkalmazás tulajdonságai** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alkalmazás tulajdonságai" szakaszt jeleníti meg az "app I n R L" mező és a "másolás" gomb kiválasztásával, valamint a "Next" (tovább) gombra kattintva.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Másolja az **alkalmazás-azonosító URI** -értékét, és használja **azonosítóként, válasz URL-címként és Sign-On URL-címként** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre a következő lépéseket: 

    ![A "Metaadatok importálása" szakaszt a "metaadatok a számítógépen" elemre kiválasztva képernyőkép jelenik meg.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Válassza ki a **metaadatokat a számítógépen**, és töltse fel a metaadat-fájlt, amelyet a Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A **név és az SSO hely** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a "név és S S O-hely" a "személyazonosság-szolgáltató neve" szövegmezővel és a "tovább" gomb kiválasztásával jelenik meg.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Adja hozzá az Identitáskezelő nevét a **személyazonosság-szolgáltató neve** szövegmezőben (például Azure ad).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább** gombra.

    ![A "Next" gomb kiválasztásával megjelenő "aláírás-ellenőrzés" szakaszt bemutató képernyőkép.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Az **összefolyásánál felhasználói fiókok** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: "a felhasználók létrehozása az összefolyásánál belső címtárban, ha szükséges" beállítás és a "tovább" gomb kiválasztásakor megjelenik a "torkolatánál felhasználói fiókok" szakasz.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Ha szükséges, válassza a **felhasználók létrehozása az összefolyásánál belső címtárban** lehetőséget, és írja be a csoport megfelelő nevét a felhasználók számára (több nem lehet. a csoportok vesszővel elválasztva).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Finish** (Befejezés) gombra.

    ![Képernyőkép az "összefoglalás" lapról a "Befejezés" gomb kiválasztásával.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Az **Azure ad ismert tartományai** szakaszban hajtsa végre a következő lépéseket: 

    ![Képernyőkép, amely az "ismert tartományok" szövegmezővel és a "Mentés" gomb kiválasztásával jeleníti meg az "ismert tartományok az Azure AD-hez" lapot.](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Az oldal bal oldali paneljén válassza az **ismert tartományok** elemet.

    b. Adja meg a tartomány nevét az **ismert tartományok** szövegmezőben.

    c. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Kantega SSO számára a torkolatánál.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Kantega SSO** lehetőséget a torkolatánál.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **KANTEGA SSO** lehetőséget a torkolatánál.

    ![Az Kantega SSO az összefolyásánál hivatkozáshoz az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Kantega SSO létrehozása az összefolyásánál tesztelő felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a torkolatánál, összefolyásánál kell őket kiépíteni. A Kantega egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Kantega SSO-ra az összefolyásánál vállalati webhelyre rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![Képernyőfelvétel: a "fogaskerék" ikon és a "felhasználói kezelés" lehetőség van kiválasztva.](./media/kantegassoforconfluence-tutorial/user1.png)

1. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. A **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforconfluence-tutorial/user2.png)

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **Jelszó megerősítése** gombra a jelszó újbóli megadásához.

    f. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kantega SSO for összefolyásánál csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO-ba olyan összefolyásánál, amelyhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)