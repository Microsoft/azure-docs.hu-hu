---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a JIRA by Solution GmbH használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAML SSO között a JIRA by Solution GmbH használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e1440feacd10935b15195110cd7ea3e20b4d523c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687634"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a JIRA by Solution GmbH által

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az SAML SSO-t a JIRA for Solution GmbH Azure Active Directory (Azure AD) használatával.
Az SAML SSO az Azure AD-vel való JIRA általi integrációja a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki jelentkezhet be a JIRA-be az SAML SSO beépülő modullal a Solution GmbH használatával.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a JIRA az Azure AD-fiókjával, ha SAML SSO-t használ a JIRA by Solution GmbH (egyszeri bejelentkezés) használatával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt és az SAML SSO-t a JIRA által használt megoldáshoz szeretné konfigurálni, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAML egyszeri bejelentkezés a JIRA-hez az egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A JIRA által használt SAML SSO támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Vállalati alkalmazás hozzáadása egyszeri bejelentkezéshez

Az egyszeri bejelentkezés az Azure AD-ben való beállításához hozzá kell adnia egy új vállalati alkalmazást. A katalógusban van egy előre konfigurált alkalmazás, amely **SAML egyszeri bejelentkezést biztosít a JIRA-hez**.

**A következő lépésekkel adhatja hozzá az SAML SSO-t a JIRA by Solution GmbH által a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd kattintson a **minden alkalmazás** elemre.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **SAML SSO a JIRA by Solution GmbH** esetében, válassza ki az SAML SSO-t a **JIRA by Solution GmbH** alapján az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához. Módosíthatja a vállalati alkalmazás nevét is.

     ![SAML SSO az JIRA az eredmények listájában a feloldási GmbH által](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Egyszeri bejelentkezés konfigurálása és tesztelése az SAML SSO beépülő modullal és az Azure AD-vel

Ebben a szakaszban egy Azure AD-felhasználó JIRA tesztelheti és konfigurálhatja az egyszeri bejelentkezést. Ezt egy **Britta Simon** nevű teszt felhasználó fogja elvégezni.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó SAML SSO-kapcsolaton keresztüli JIRA kell létrehozni.

Az egyszeri bejelentkezés konfigurálásához és teszteléséhez végre kell hajtania a következő lépéseket:

1. **[Az Azure ad Enterprise alkalmazás konfigurálása egyszeri bejelentkezéshez](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – konfigurálja az Azure ad Enterprise alkalmazást az egyszeri bejelentkezéshez
2. **[Konfigurálja az JIRA-példány SAML SSO beépülő modulját](#configure-the-saml-sso-plugin-of-your-jira-instance)** – konfigurálja az egyes Sign-On beállításokat az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – tesztelési felhasználó létrehozása az Azure ad-ben.
1. **[Az Azure ad-tesztkörnyezet kiosztása](#assign-the-azure-ad-test-user)** – lehetővé teszi, hogy a felhasználó az egyszeri bejelentkezést használja az Azure oldalon.
1. **[A JIRA-felhasználó létrehozása](#create-the-test-user-also-in-jira)** a JIRA-ben az Azure ad-tesztkörnyezet felhasználói számára.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – ellenőrizze, hogy a konfiguráció működik-e.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Az Azure AD Enterprise alkalmazás konfigurálása egyszeri bejelentkezéshez

Ebben a szakaszban az egyszeri bejelentkezést a Azure Portalban állíthatja be.

A következő lépések végrehajtásával konfigurálhatja az egyszeri bejelentkezést az SAML SSO-vel a JIRA-hez a Solution GmbH használatával:

1. A [Azure Portal](https://portal.azure.com/)az imént létrehozott **SAML SSO for JIRA by Resolution GmbH** Enterprise Application esetében válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali panelen.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri **bejelentkezési módszer kiválasztásához** válassza ki az **SAML** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Ezt követően kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni az alkalmazást a **identitásszolgáltató** által kezdeményezett módban, hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "alapszintű k L konfiguráció" szakaszt jeleníti meg az "azonosító" és a "válasz U R L" szövegmezővel, valamint a "Mentés" gomb kiválasztásával.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást az **SP** által kezdeményezett módban szeretné konfigurálni:

    ![SAML egyszeri bejelentkezés a JIRA és az URL-címek egyszeri bejelentkezési adatai alapján](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Az azonosító, a válasz URL-cím és a bejelentkezési URL-cím helyére írja **\<server-base-url>** be az JIRA-példány alap URL-címét. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti. Ha problémája van, vegye fel velünk a kapcsolatot az [SAML SSO-val a JIRA by Solution GmbH ügyfélszolgálati csapatával](https://www.resolution.de/go/support).

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban töltse le az **összevonási metaadatok XML-fájlját** , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>A JIRA-példány SAML SSO beépülő moduljának konfigurálása 

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a JIRA-példányba.

2. Vigye a kurzort a jobb oldalon található fogaskerék fölé, majd kattintson az **Alkalmazások kezelése** lehetőségre.
    
    ![Képernyőkép, amely egy nyilat mutat a fogaskerék ikonra, és a legördülő menüből kiválasztott "alkalmazások kezelése" lehetőség látható.](./media/samlssojira-tutorial/addon1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lapra, adja meg a **jelszót** , és kattintson a **Confirm (megerősítés** ) gombra.

    ![A "rendszergazdai hozzáférés" oldalt megjelenítő képernyőkép.](./media/samlssojira-tutorial/addon2.png)

4. A JIRA általában átirányítja Önt a Atlassian Marketplace-re. Ha nem, kattintson az **új alkalmazások keresése** elemre a bal oldali panelen. Keressen **SAML egyszeri bejelentkezést (SSO) a JIRA** , és kattintson a **telepítés** gombra az SAML beépülő modul telepítéséhez.

    ![Képernyőkép, amely a "Atlassian Marketplace for JIRA" oldalt jeleníti meg, és egy nyíl mutat a "telepítés" gombra az "S az M L Single Sign on (S O) JIRA, S A M L/S S O" alkalmazásban.](./media/samlssojira-tutorial/store.png)

5. A beépülő modul telepítése megkezdődik. Ha elkészült, kattintson a **Bezárás** gombra.

    ![A "telepítés" párbeszédpanelt megjelenítő képernyőkép.](./media/samlssojira-tutorial/store-2.png)

    ![Képernyőkép, amely a "telepített és kész" állapotot mutatja. a "Bezárás" gomb kiválasztásával rendelkező párbeszédpanel.](./media/samlssojira-tutorial/store-3.png)

6. Ezután kattintson a **kezelés** elemre.

    ![Képernyőkép, amely megjeleníti a "kezelés" gomb kiválasztásával megjelenő "S L-es egyszeri bejelentkezést (S O) JIRA, S L/S S O" alkalmazást.](./media/samlssojira-tutorial/store-4.png)
    
8. Ezt követően kattintson a **configure (Konfigurálás** ) elemre az imént telepített beépülő modul konfigurálásához.

    ![Képernyőkép az "alkalmazások kezelése" oldalról, a "configure" gomb kiválasztásával a "S A JIRA" SingleSignOn.](./media/samlssojira-tutorial/store-5.png)

9. Az **SAML SingleSignon beépülő modul konfigurálása** varázslóban kattintson az **új identitásszolgáltató hozzáadása** lehetőségre az Azure ad új identitás-szolgáltatóként való konfigurálásához.

    ![A képernyőképen az "üdvözlő" oldal jelenik meg, az "új I d P hozzáadása" gomb kiválasztásával.](./media/samlssojira-tutorial/addon4.png) 

10. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: "az S A M L Identity Provider kiválasztása" lap "I d P type" és "Name" szövegmező kiemelve, valamint a "tovább" gomb kiválasztásával.](./media/samlssojira-tutorial/addon5a.png)
 
    a. Állítsa be az **Azure ad** -t identitásszolgáltató típusként.
    
    b. Adja hozzá az identitás-szolgáltató **nevét** (például Azure ad).
    
    c. Adja hozzá az identitás-szolgáltató (például az Azure AD) **leírását** (nem kötelező).
    
    d. Kattintson a **Tovább** gombra.
    
11. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább** gombra.
 
    ![A "személyazonosság-szolgáltatói konfiguráció" oldalt megjelenítő képernyőkép.](./media/samlssojira-tutorial/addon5b.png)

12. Az **SAML-identitásszolgáltató metaadatainak importálása** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "M L I d P-Metaadatok importálása" lapot jeleníti meg a "metaadatok kiválasztása X M L fájl kijelölése" művelet kiválasztásával.](./media/samlssojira-tutorial/addon5c.png)

    a. Kattintson a **metaadatok XML-fájljának kiválasztása** gombra, és válassza ki a letöltött **összevonási METAADATOKAT tartalmazó XML-** fájlt.

    b. Kattintson az **Importálás** gombra.
     
    c. Várjon röviden, amíg az importálás sikeres lesz.  
     
    d. Kattintson a **tovább** gombra.
    
13. A **felhasználói azonosító attribútum és átalakítás** lapon kattintson a **tovább** gombra.

    ![Képernyőfelvétel: "a felhasználó I D attribútuma és átalakítása" oldal, amelyen a "Next" gomb van kiválasztva.](./media/samlssojira-tutorial/addon5d.png)
    
14. A beállítások mentéséhez kattintson a **felhasználó létrehozása és frissítése** lapon a **Mentés &** elemre.
    
    ![A "felhasználó létrehozása és frissítése" oldalt megjelenítő képernyőkép, amely a "Mentés & Next" (következő) gombra kattint.](./media/samlssojira-tutorial/addon6a.png)
    
15. A **beállítások tesztelése** lapon kattintson a **teszt kihagyása & a manuális konfigurálás** lehetőségre a felhasználói teszt azonnali kihagyása érdekében. Ez a következő szakaszban lesz elvégezve, és néhány beállítást igényel a Azure Portal.
    
    ![Képernyőfelvétel: "a beállítások tesztelése" lapon a "teszt kihagyása & manuális beállítása" gomb látható.](./media/samlssojira-tutorial/addon6b.png)
    
16. A figyelmeztetés kihagyásához kattintson **az OK** gombra.
    
    ![Képernyőkép, amely az "O K" gombbal jelölt figyelmeztető párbeszédpanelt jeleníti meg.](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban. A felhasználóval tesztelheti az egyszeri bejelentkezést.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza a képernyő felső részén található **új felhasználó** lehetőséget.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználó tulajdonságaiban** hajtsa végre a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **Britta Simon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a nevet <b>BrittaSimon@contoso.com</b> .

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban Simon Britta-t ad hozzá a vállalati alkalmazáshoz, amely lehetővé teszi az egyszeri bejelentkezés használatát.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget. 

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában keresse meg az oktatóanyag elején létrehozott vállalati alkalmazást. Ha követi az oktatóanyag lépéseit, a rendszer az **SAML SSO-t az JIRA általi feloldáshoz**. Ha egy másik nevet adott meg, keresse meg a nevet.

    ![Az alkalmazások listájában a JIRA által a megoldási GmbH-hez csatolt, SAML SSO](common/all-applications.png)

3. A bal oldali panelen kattintson a **felhasználók és csoportok** elemre.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-the-test-user-also-in-jira"></a>A teszt felhasználó létrehozása a JIRA-ben is

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAML SSO-ba a JIRA by Solution GmbH használatával, ezeket az SAML SSO-nal kell kiépíteni a JIRA by Solution GmbH használatával. Ebben az oktatóanyagban az üzembe helyezést kézzel kell elvégeznie. Más kiépítési modellek azonban az SAML SSO beépülő modulhoz is elérhetők megoldásként **, például igény szerint.** Tekintse meg a dokumentációt az [SAML SSO által a Solution GmbH használatával](https://wiki.resolution.de/doc/saml-sso/latest/all). Ha kérdése van, forduljon a támogatási szolgálathoz a [megoldás támogatásához](https://www.resolution.de/go/support).

**Felhasználói fiók manuális kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA-példányba rendszergazdaként.

2. Vigye a kurzort a fogaskerék fölé, és válassza a **felhasználói kezelés** lehetőséget.

   ![Képernyőfelvétel: a "fogaskerék" ikont ábrázoló nyíl a legördülő menüből kiválasztott "felhasználói kezelés".](./media/samlssojira-tutorial/user1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lapra, írja be a **jelszót** , és kattintson a **Confirm (megerősítés** ) gombra.

    ![Képernyőfelvétel: a "jelszó" szövegmezőben a "rendszergazdai hozzáférés" oldal jelenik meg.](./media/samlssojira-tutorial/user2.png) 

4. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása** elemre.

    ![Képernyőkép, amely a "felhasználó-kezelés" fület jeleníti meg a "felhasználó létrehozása" gomb kiválasztásával.](./media/samlssojira-tutorial/user3-new.png) 

5. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket. A felhasználót pontosan ugyanúgy kell létrehoznia, mint az Azure AD-ben:

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user4-new.png) 

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználó e-mail-címét:  <b>BrittaSimon@contoso.com</b> .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét: **Britta Simon**.

    c. A **Felhasználónév** szövegmezőbe írja be a felhasználó e-mail-címét: <b>BrittaSimon@contoso.com</b> . 

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása** lehetőségre a felhasználó létrehozásának befejezéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a JIRA által használt SAML SSO-ra kattint, automatikusan be kell jelentkeznie az SAML SSO-ba a JIRA by Solution GmbH számára, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

Az egyszeri bejelentkezést is tesztelheti, ha megnyitja a következőt: `https://<server-base-url>/plugins/servlet/samlsso` . Helyettesítse **\<server-base-url>** be a JIRA-példány alap URL-címét.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Egyszeri bejelentkezéses átirányítás engedélyezése a JIRA

Ahogy a korábban is említettük, az egyszeri bejelentkezést jelenleg kétféleképpen indíthatjuk el. Vagy a **Azure Portal** használatával vagy a **JIRA-példányra mutató speciális hivatkozás** használatával. A Solution GmbH SAML SSO beépülő modulja is lehetővé teszi az egyszeri bejelentkezés aktiválását, ha egyszerűen **hozzáfér az JIRA-példányra mutató URL-címekhez**.

Lényegében az JIRA-t elérő összes felhasználó át lesz irányítva az egyszeri bejelentkezésre, miután aktiválta egy lehetőséget a beépülő modulban.

Az SSO-átirányítás aktiválásához tegye a következőket az **JIRA-példányban**:

1. Nyissa meg az SAML SSO beépülő modul konfigurációs lapját a JIRA-ben.
1. Kattintson a bal oldali panelen található **átirányítás** elemre.

   ![A JIRA SAML SingleSignOn beépülő modul konfigurációs oldalának részleges képernyőképe, amely kiemeli az átirányítási hivatkozást a bal oldali navigációs sávon.](./media/samlssojira-tutorial/ssore1.png)

1. Jelölje be az **SSO-átirányítás engedélyezése** jelölőnégyzetet.

   ![A JIRA SAML SingleSignOn beépülő modul konfigurációs oldalának részleges képernyőképe, amely kiemeli a kiválasztott "SSO-átirányítás engedélyezése" jelölőnégyzetet.](./media/samlssojira-tutorial/ssore2.png) 

1. Nyomja meg a **Beállítások mentése** gombot a jobb felső sarokban.

A beállítás aktiválása után továbbra is elérheti a Felhasználónév/jelszó kérést, ha az **Nosso engedélyezése** beállítás be van jelölve `https://<server-base-url>/login.jsp?nosso` . Mint mindig, helyettesítse **\<server-base-url>** be az alap URL-címet.


## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)