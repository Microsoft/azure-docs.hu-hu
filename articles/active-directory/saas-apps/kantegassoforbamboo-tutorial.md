---
title: 'Oktatóanyag: Azure Active Directory integráció a Bamboo Kantega SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Bamboo Azure Active Directory és a Kantega SSO között.
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
ms.openlocfilehash: aa5f908cdf25925db63054adaf1e6dab15f5260b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459305"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Oktatóanyag: Azure Active Directory integráció a Bamboo Kantega SSO-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Bamboo Kantega SSO-t a Azure Active Directory (Azure AD) használatával.
A Kantega SSO for Bamboo integrációja az Azure AD-val a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Bamboo Kantega SSO-hoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Bamboo (egyszeri bejelentkezés) Azure AD-fiókjával rendelkező Kantega egyszeri bejelentkezésre.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Bamboo Kantega egyszeri bejelentkezéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kantega SSO a bambusz egyszeri bejelentkezésre alkalmas előfizetés esetén

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Bamboo Kantega SSO támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Kantega SSO hozzáadása a Bamboo-hoz a katalógusból

A Bamboo Kantega egyszeri bejelentkezésének Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Bamboo-hoz készült Kantega SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépéseket követve adhat hozzá Kantega SSO-t a Bambuszhoz a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KANTEGA SSO for Bamboo**, válassza a **Kantega SSO for Bamboo** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO a Bamboo-hoz az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Kantega SSO-hoz a Bamboo-hoz a **Britta Simon** nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Kantega SSO-hoz kapcsolódó felhasználó közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés és a Bamboo Kantega SSO konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[KANTEGA SSO konfigurálása a bambusz egyszeri bejelentkezéshez](#configure-kantega-sso-for-bamboo-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre KANTEGA SSO-t a Bamboo test User-hez](#create-kantega-sso-for-bamboo-test-user)** , hogy a Britta-hez tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó Kantega
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Bamboo Kantega SSO-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a Bamboo Application Integration **(Kantega SSO** ) lapon válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és Sign-On URL-címmel. Ezek az értékek a Bamboo beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **KANTEGA SSO beállítása a Bamboo-hoz** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Kantega egyszeri bejelentkezés beállítása a Bamboo egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a bambusz helyszíni kiszolgálóra.

1. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.

    ![A képernyőképen a beállítások menüből kiválasztott bővítmények láthatók.](./media/kantegassoforbamboo-tutorial/addon1.png)

1. A Bővítmények lap beállítások területén kattintson az **új bővítmények keresése** elemre. Keressen rá a **KANTEGA SSO for Bamboo (SAML & Kerberos)** kifejezésre, majd kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Képernyőfelvétel: a bambusz-felügyelet a Kantega S S O-vel a kiválasztott Bamboo-hoz.](./media/kantegassoforbamboo-tutorial/addon2.png)

1. A beépülő modul telepítése megkezdődik.

    ![A képernyőképen a Bamboo Kantega S S O-re való telepítésének folyamata látható.](./media/kantegassoforbamboo-tutorial/addon21.png)

1. A telepítés befejezését követően. Kattintson a **Bezárás** gombra.

    ![A képernyőképen a Bezárás gomb látható.](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![Képernyőfelvétel: a kezelés gomb.](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![A képernyőfelvételen a felhasználó által telepített bővítmények láthatók a configure beállítással.](./media/kantegassoforbamboo-tutorial/addon3.png)

1. Az **SAML** szakaszban. Válassza az **Azure Active Directory (Azure ad)** elemet az **identitás-szolgáltató hozzáadása** legördülő listából.

    ![Képernyőfelvétel: az Kantega egyetlen Sign-On az Azure A D-vel kiválasztva az identitás-szolgáltatóként.](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Válassza az előfizetési szint **alapszintű** lehetőséget.

    ![A képernyőképen az Azure A D előkészítése alapszintű beállítás látható.](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Az **alkalmazás tulajdonságai** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: az alkalmazás tulajdonságai szakasz, ahol megadhatja az adatokat ebben a lépésben.](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Másolja az **alkalmazás-azonosító URI** -értékét, és használja **azonosítóként, válasz URL-címként és Sign-On URL-címként** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a metaadatok importálási szakasza, ahol megkeresheti a metaadat-fájlt.](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Válassza ki a **metaadatokat a számítógépen**, és töltse fel a metaadat-fájlt, amelyet a Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A **név és az SSO hely** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen a név és a S S O helye látható, ahol az Azure a D az identitás-szolgáltató neve.](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Adja hozzá az Identitáskezelő nevét a **személyazonosság-szolgáltató neve** szövegmezőben (például Azure ad).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább** gombra.

    ![A képernyőképen az aláírás-ellenőrzés látható.](./media/kantegassoforbamboo-tutorial/addon9.png)

1. A **Bamboo User accounts (bambusz felhasználói fiókok** ) szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőfelvételen a bambusz felhasználói fiókjai láthatók, ahol lehetősége van felhasználókat létrehozni.](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. **Szükség esetén válassza a felhasználók létrehozása a bambusz belső könyvtárában** lehetőséget, és adja meg a felhasználók csoportjának megfelelő nevét (ez lehet több nem. a csoportok vesszővel elválasztva).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Finish** (Befejezés) gombra.

    ![A képernyőképen az összefoglalás lap látható.](./media/kantegassoforbamboo-tutorial/addon11.png)

1. Az **Azure ad ismert tartományai** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen az Azure A D-n ismert tartományait láthatja, ahol elvégezheti ezeket a lépéseket.](./media/kantegassoforbamboo-tutorial/addon12.png)

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

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Bamboo Kantega egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Kantega egyszeri bejelentkezés a Bamboo**-hoz lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Kantega egyszeri bejelentkezés a Bamboo**-hoz lehetőséget.

    ![Az Kantega egyszeri bejelentkezés a Bamboo-hez hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Kantega SSO létrehozása a Bamboo test userhez

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Bambuszba, ki kell építeni őket a Bambuszba. A Bamboo Kantega egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a bambusz helyi kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![A képernyőfelvételen a beállítások menüből kiválasztott felhasználói felügyelet látható.](./media/kantegassoforbamboo-tutorial/user1.png)

1. Kattintson a **felhasználók** elemre. A **felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: a felhasználó hozzáadása panel, ahol elvégezheti ezeket a lépéseket.](./media/kantegassoforbamboo-tutorial/user2.png)

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    b. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    c. A **Jelszó megerősítése** szövegmezőbe írja be újra a felhasználó jelszavát.

    d. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    e. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    f. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kantega SSO for Bamboo csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO for Bamboo szolgáltatásba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)