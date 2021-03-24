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
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 827a05a8dfbf05b0dacb0bd812fb964567f39b3f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954207"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a JIRA by Solution GmbH által

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a JIRA for Solution GmbH Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a JIRA-hez készült SAML SSO-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az SAML SSO-hoz a JIRA by Solution GmbH használatával.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO-ba a JIRA által az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAML egyszeri bejelentkezés (SSO) a JIRA-alapú egyszeri bejelentkezés (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A JIRA által használt SAML SSO az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a JIRA by Solution GmbH által a katalógusból

Ha az SAML SSO-t az Azure AD-be való JIRA szeretné konfigurálni, az SAML SSO-t hozzá kell adnia az JIRA-hez készült Solution GmbH által a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **SAML SSO a JIRA by Solution GmbH** a keresőmezőbe.
1. Válassza ki az **SAML SSO elemet a JIRA által** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Az Azure AD SSO konfigurálása és tesztelése az SAML SSO-hoz a JIRA by Solution GmbH használatával

Konfigurálja és tesztelje az Azure AD SSO-t az SAML SSO-vel a JIRA by Solution GmbH használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO-ben a JIRA by Solution GmbH használatával.

Az Azure AD SSO és az SAML SSO JIRA általi konfigurálásához és teszteléséhez az alábbi lépéseket kell végrehajtania:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja az SAML SSO-t a JIRA by Resolution GmbH SSO használatával](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    1. **[SAML SSO létrehozása a JIRA által a Resolution GmbH test User](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** -to have a B. Simon a SAML SSO-ben a JIRA by Solution GmbH, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **JIRA SAML SSO esetén a megoldási GmbH** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni az alkalmazást a **identitásszolgáltató** által kezdeményezett módban, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást az **SP** által kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Az azonosító, a válasz URL-cím és a bejelentkezési URL-cím helyére írja **\<server-base-url>** be az JIRA-példány alap URL-címét. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti. Ha problémája van, vegye fel velünk a kapcsolatot az [SAML SSO-val a JIRA by Solution GmbH ügyfélszolgálati csapatával](https://www.resolution.de/go/support).

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban töltse le az **összevonási metaadatok XML-fájlját** , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az SAML SSO-hoz a JIRA by Solution GmbH által.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listáról válassza ki az **SAML SSO elemet a JIRA by Solution GmbH által**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>SAML SSO konfigurálása a JIRA által a Solution GmbH SSO-val 

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a JIRA-példányba.

2. Vigye a kurzort a jobb oldalon található fogaskerék fölé, majd kattintson az **Alkalmazások kezelése** lehetőségre.
    
    ![Képernyőkép, amely egy nyilat mutat a fogaskerék ikonra, és a legördülő menüből kiválasztott "alkalmazások kezelése" lehetőség látható.](./media/samlssojira-tutorial/add-on-1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lapra, adja meg a **jelszót** , és kattintson a **Confirm (megerősítés** ) gombra.

    ![A "rendszergazdai hozzáférés" oldalt megjelenítő képernyőkép.](./media/samlssojira-tutorial/add-on-2.png)

4. A JIRA általában átirányítja Önt a Atlassian Marketplace-re. Ha nem, kattintson az **új alkalmazások keresése** elemre a bal oldali panelen. Keressen **SAML egyszeri bejelentkezést (SSO) a JIRA** , és kattintson a **telepítés** gombra az SAML beépülő modul telepítéséhez.

    ![Képernyőkép, amely a "Atlassian Marketplace for JIRA" oldalt jeleníti meg, és egy nyíl mutat a "telepítés" gombra az "S az M L Single Sign on (S O) JIRA, S A M L/S S O" alkalmazásban.](./media/samlssojira-tutorial/store.png)

5. A beépülő modul telepítése megkezdődik. Ha elkészült, kattintson a **Bezárás** gombra.

    ![A "telepítés" párbeszédpanelt megjelenítő képernyőkép.](./media/samlssojira-tutorial/store-2.png)

    ![Képernyőkép, amely a "telepített és kész" állapotot mutatja. a "Bezárás" gomb kiválasztásával rendelkező párbeszédpanel.](./media/samlssojira-tutorial/store-3.png)

6. Ezután kattintson a **kezelés** elemre.

    ![Képernyőkép, amely megjeleníti a "kezelés" gomb kiválasztásával megjelenő "S L-es egyszeri bejelentkezést (S O) JIRA, S L/S S O" alkalmazást.](./media/samlssojira-tutorial/store-4.png)
    
7. Ezt követően kattintson a **configure (Konfigurálás** ) elemre az imént telepített beépülő modul konfigurálásához.

    ![Képernyőkép az "alkalmazások kezelése" oldalról, a "configure" gomb kiválasztásával a "S A JIRA" SingleSignOn.](./media/samlssojira-tutorial/store-5.png)

8. Az **SAML SingleSignon beépülő modul konfigurálása** varázslóban kattintson az **új identitásszolgáltató hozzáadása** lehetőségre az Azure ad új identitás-szolgáltatóként való konfigurálásához.

    ![A képernyőképen az "üdvözlő" oldal jelenik meg, az "új I d P hozzáadása" gomb kiválasztásával.](./media/samlssojira-tutorial/add-on-4.png) 

9. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Képernyőfelvétel: "az S A M L Identity Provider kiválasztása" lap "I d P type" és "Name" szövegmező kiemelve, valamint a "tovább" gomb kiválasztásával.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Állítsa be az **Azure ad** -t identitásszolgáltató típusként.
    
    b. Adja hozzá az identitás-szolgáltató **nevét** (például Azure ad).
    
    c. Adja hozzá az identitás-szolgáltató (például az Azure AD) **leírását** (nem kötelező).
    
    d. Kattintson a **Tovább** gombra.
    
10. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább** gombra.
 
    ![A "személyazonosság-szolgáltatói konfiguráció" oldalt megjelenítő képernyőkép.](./media/samlssojira-tutorial/configuration.png)

11. Az **SAML-identitásszolgáltató metaadatainak importálása** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép, amely az "M L I d P-Metaadatok importálása" lapot jeleníti meg a "metaadatok kiválasztása X M L fájl kijelölése" művelet kiválasztásával.](./media/samlssojira-tutorial/metadata.png)

    a. Kattintson a **metaadatok XML-fájljának kiválasztása** gombra, és válassza ki a letöltött **összevonási METAADATOKAT tartalmazó XML-** fájlt.

    b. Kattintson az **Importálás** gombra.
     
    c. Várjon röviden, amíg az importálás sikeres lesz.  
     
    d. Kattintson a **tovább** gombra.
    
12. A **felhasználói azonosító attribútum és átalakítás** lapon kattintson a **tovább** gombra.

    ![Képernyőfelvétel: "a felhasználó I D attribútuma és átalakítása" oldal, amelyen a "Next" gomb van kiválasztva.](./media/samlssojira-tutorial/transformation.png)
    
13. A beállítások mentéséhez kattintson a **felhasználó létrehozása és frissítése** lapon a **Mentés &** elemre.
    
    ![A "felhasználó létrehozása és frissítése" oldalt megjelenítő képernyőkép, amely a "Mentés & Next" (következő) gombra kattint.](./media/samlssojira-tutorial/update.png)
    
14. A **beállítások tesztelése** lapon kattintson a **teszt kihagyása & a manuális konfigurálás** lehetőségre a felhasználói teszt azonnali kihagyása érdekében. Ez a következő szakaszban lesz elvégezve, és néhány beállítást igényel a Azure Portal.
    
    ![Képernyőfelvétel: "a beállítások tesztelése" lapon a "teszt kihagyása & manuális beállítása" gomb látható.](./media/samlssojira-tutorial/test.png)
    
15. A figyelmeztetés kihagyásához kattintson **az OK** gombra.
    
    ![Képernyőkép, amely az "O K" gombbal jelölt figyelmeztető párbeszédpanelt jeleníti meg.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a JIRA által a Resolution GmbH test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAML SSO-ba a JIRA by Solution GmbH használatával, ezeket az SAML SSO-nal kell kiépíteni a JIRA by Solution GmbH használatával. Ebben az oktatóanyagban az üzembe helyezést kézzel kell elvégeznie. Más kiépítési modellek azonban az SAML SSO beépülő modulhoz is elérhetők megoldásként **, például igény szerint.** Tekintse meg a dokumentációt az [SAML SSO által a Solution GmbH használatával](https://wiki.resolution.de/doc/saml-sso/latest/all). Ha kérdése van, forduljon a támogatási szolgálathoz a [megoldás támogatásához](https://www.resolution.de/go/support).

**Felhasználói fiók manuális kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA-példányba rendszergazdaként.

2. Vigye a kurzort a fogaskerék fölé, és válassza a **felhasználói kezelés** lehetőséget.

   ![Képernyőfelvétel: a "fogaskerék" ikont ábrázoló nyíl a legördülő menüből kiválasztott "felhasználói kezelés".](./media/samlssojira-tutorial/user-1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lapra, írja be a **jelszót** , és kattintson a **Confirm (megerősítés** ) gombra.

    ![Képernyőfelvétel: a "jelszó" szövegmezőben a "rendszergazdai hozzáférés" oldal jelenik meg.](./media/samlssojira-tutorial/user-2.png) 

4. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása** elemre.

    ![Képernyőkép, amely a "felhasználó-kezelés" fület jeleníti meg a "felhasználó létrehozása" gomb kiválasztásával.](./media/samlssojira-tutorial/user-3-new.png) 

5. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket. A felhasználót pontosan ugyanúgy kell létrehoznia, mint az Azure AD-ben:

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user-4-new.png) 

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználó e-mail-címét:  <b>BrittaSimon@contoso.com</b> .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét: **Britta Simon**.

    c. A **Felhasználónév** szövegmezőbe írja be a felhasználó e-mail-címét: <b>BrittaSimon@contoso.com</b> . 

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása** lehetőségre a felhasználó létrehozásának befejezéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a rendszer átirányítja az SAML SSO-ra a JIRA által az alábbi URL-címen, ahol kezdeményezheti a bejelentkezési folyamatot.  

* Nyissa meg az SAML SSO-t a JIRA by Resolution GmbH közvetlen bejelentkezési URL-címével, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az SAML SSO-ba a JIRA által a megoldási GmbH számára, amelyhez be kell állítania az egyszeri bejelentkezést. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások JIRA által használt SAML SSO-ra kattint, ha az SP módban van konfigurálva, akkor a bejelentkezési folyamat elindításához, illetve ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az SAML SSO-ra a JIRA by Solution GmbH számára, amelyhez be kell állítania az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="enable-sso-redirection-for-jira"></a>SSO-átirányítás engedélyezése a JIRA

Ahogy a korábban is említettük, az egyszeri bejelentkezést jelenleg kétféleképpen indíthatjuk el. Vagy a **Azure Portal** használatával vagy a **JIRA-példányra mutató speciális hivatkozás** használatával. A Solution GmbH SAML SSO beépülő modulja is lehetővé teszi az egyszeri bejelentkezés aktiválását, ha egyszerűen **hozzáfér az JIRA-példányra mutató URL-címekhez**.

Lényegében az JIRA-t elérő összes felhasználó át lesz irányítva az egyszeri bejelentkezésre, miután aktiválta egy lehetőséget a beépülő modulban.

Az SSO-átirányítás aktiválásához tegye a következőket az **JIRA-példányban**:

1. Nyissa meg az SAML SSO beépülő modul konfigurációs lapját a JIRA-ben.
1. Kattintson a bal oldali panelen található **átirányítás** elemre.

   ![A JIRA SAML SingleSignOn beépülő modul konfigurációs oldalának részleges képernyőképe, amely kiemeli az átirányítási hivatkozást a bal oldali navigációs sávon.](./media/samlssojira-tutorial/configure-1.png)

1. Jelölje be az **SSO-átirányítás engedélyezése** jelölőnégyzetet.

   ![A JIRA SAML SingleSignOn beépülő modul konfigurációs oldalának részleges képernyőképe, amely kiemeli a kiválasztott "SSO-átirányítás engedélyezése" jelölőnégyzetet.](./media/samlssojira-tutorial/configure-2.png) 

1. Nyomja meg a **Beállítások mentése** gombot a jobb felső sarokban.

A beállítás aktiválása után továbbra is elérheti a Felhasználónév/jelszó kérést, ha az **Nosso engedélyezése** beállítás be van jelölve `https://<server-base-url>/login.jsp?nosso` . Mint mindig, helyettesítse **\<server-base-url>** be az alap URL-címet.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SAML SSO-t a JIRA by Solution GmbH használatával, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).