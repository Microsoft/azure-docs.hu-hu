---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a Solution GmbH összefolyásánál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAML SSO között a Solution GmbH összefolyásánál.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651280"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a Solution GmbH összefolyásánál

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a Solution GmbH Azure Active Directory (Azure AD) szolgáltatással való összefolyásánál. Ha integrálja az SAML SSO-t az Azure AD-vel való összefolyásánál, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az SAML SSO-hoz a Resolution GmbH általi összefolyásánál.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az SAML SSO-ba az Azure AD-fiókkal való összefolyásánál.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAML SSO a Resolution GmbH egyszeri bejelentkezéses (SSO) engedélyezett előfizetésének összefolyásánál.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Resolution GmbH által összefolyásánál az SAML SSO támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása az összefolyásánál a Resolution GmbH által a katalógusból

Az SAML SSO az Azure AD-be való összevonáshoz való integrálásának konfigurálásához az SAML SSO-t hozzá kell adnia a katalógusból a Managed SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **SAML SSO az összefolyásánál a Resolution GmbH által** a keresőmezőbe.
1. Válassza ki az **SAML SSO-t a Resolution GmbH által** az eredmények panelről való összefolyásánál, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Az Azure AD SSO konfigurálása és tesztelése az SAML SSO-hoz a Solution GmbH összefolyásánál

Konfigurálja és tesztelje az Azure AD SSO-t az SAML SSO-nal a Resolution GmbH által a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO-ben a megoldási szolgáltatás összefolyásánál.

A következő lépések végrehajtásával konfigurálhatja és tesztelheti az Azure AD SSO-t az SAML SSO-nal a Solution GmbH összefolyásánál:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. Az **[SAML SSO konfigurálása a Resolution GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** -hoz való összekapcsoláshoz – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
    1. **[SAML SSO létrehozása a Solution GmbH által végzett összekapcsoláshoz](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó Britta Simon-nek a SAML SSO-ban való összekapcsolásához.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portalban, az SAML SSO-hoz való összevonáshoz a **Resolution GmbH** Application Integration lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-val a megoldási](https://www.resolution.de/go/support) feladatokkal való összefolyásánál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az SAML SSO-hoz a Solution GmbH összefolyásánál.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listájában válassza ki az **SAML SSO-t a megoldási GmbH-hoz való összefolyásánál**.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>SAML SSO konfigurálása a Resolution GmbH SSO-hoz való összefolyásánál

1. Egy másik böngészőablakban jelentkezzen be az SAML SSO-ba a **Resolution GmbH felügyeleti portáljának** rendszergazdájaként.

2. Mutasson a fogaskerékre, és kattintson a **bővítmények** elemre.
    
    ![A legördülő menüben kiválasztott "fogaskerék" ikont és "bővítményeket" megjelenítő képernyőkép.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. A rendszer átirányítja a rendszergazdai hozzáférés lapra. Adja meg a jelszót, és kattintson a **Confirm (megerősítés** ) gombra.

    ![Képernyőkép a "rendszergazdai hozzáférés" oldalon a "megerősítés" gomb kiválasztásával.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Az **ATLASSIAN-piactér** lapon kattintson az **új bővítmények keresése** elemre. 

    ![Képernyőkép, amely a "Attlassian Marketplace" fület jeleníti meg az "új bővítmények keresése" beállítással.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Keressen **SAML egyszeri bejelentkezést (SSO) a torkolatánál** , és kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Képernyőkép: az "új bővítmények keresése" lap "S A M L egyszeri bejelentkezés (S O) a" összefolyásánál "a keresőmezőbe és a" telepítés "gomb kiválasztásával.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. A beépülő modul telepítése megkezdődik. Kattintson a **Bezárás** gombra.

    ![A "telepítés" párbeszédpanelt megjelenítő képernyőkép.](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Képernyőkép, amely a "telepített és kész" állapotot mutatja. párbeszédablak a "Bezárás" művelettel kiválasztva.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Kattintson a **Kezelés** gombra.

    ![Képernyőfelvétel: "az M L egyszeri bejelentkezés (S O)" a "Manage" (kezelés) gombbal kiválasztva az alkalmazás oldalára.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Képernyőkép, amely a "felügyelet" lapot jeleníti meg a "Konfigurálás" gomb kiválasztásával.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Ez az új beépülő modul a **felhasználók & biztonság** lapon is megtalálható.

    ![Képernyőfelvétel: a "felhasználók & biztonság" lapot jelölte meg "S A M L SingleSignOn".](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Az **SAML SingleSignon beépülő modul konfigurálása** lapon kattintson az **új identitásszolgáltató hozzáadása** gombra az Identitáskezelő beállításainak konfigurálásához.

    ![Képernyőfelvétel: az "S A M SingleSignOn beépülő modul konfigurálása" oldal, az "új I d P hozzáadása" gomb kiválasztásával.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Az **SAML-identitás szolgáltatójának kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Az "I d P type", a "Name" és a "Description" szövegmezők kiemelésével megjelenő képernyőkép.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Állítsa be az **Azure ad** -t identitásszolgáltató típusként.
    
    b. Adja meg az identitás-szolgáltató **nevét** (például Azure ad).
    
    c. Adja meg az identitás-szolgáltató (például az Azure AD) **leírását** .
    
    d. Kattintson a **Tovább** gombra.
    
12. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább** gombra.

    ![Képernyőkép, amely a "következő" gomb kiválasztásával jeleníti meg az "identitás-szolgáltató konfigurációja" lapot.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Az **SAML-identitásszolgáltató metaadatainak importálása** oldalon hajtsa végre a következő lépéseket:

    ![Képernyőkép: az "Importálás", "fájl betöltése" és "tovább" gombokkal rendelkező "Importálás" M L I d P metaadatok "oldal.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Kattintson a **fájl betöltése** gombra, és válassza ki az 5. lépésben letöltött metaadatokat tartalmazó XML-fájlt.

    b. Kattintson az **Importálás** gombra.
    
    c. Várjon röviden, amíg az importálás sikeres lesz.
    
    d. Kattintson a **tovább** gombra.
    
14. A **felhasználói azonosító attribútum és átalakítás** lapon kattintson a **tovább** gombra.

    ![Képernyőfelvétel: a "Next" gomb kiválasztásával megjelenített "felhasználói azonosító attribútum és átalakítás" oldal.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. A **felhasználók létrehozása és frissítése** lapon kattintson a **Mentés gombra &** a beállítások mentése elem mellett.   
    
    ![A "felhasználó létrehozása és frissítése" oldalt megjelenítő képernyőkép, amely a "Mentés & Next" (következő) gombra kattint.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. A **beállítások tesztelése** lapon kattintson a **teszt kihagyása & a manuális konfigurálás** lehetőségre a felhasználói teszt azonnali kihagyása érdekében. Ez a következő szakaszban lesz elvégezve, és néhány beállítást igényel a Azure Portalban. 
    
    ![Képernyőfelvétel: "a beállítások tesztelése" lapon a "teszt kihagyása & manuális beállítása" gomb látható.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. A megjelenő párbeszédablakban a **teszt kihagyása azt jelenti**, hogy..., kattintson **az OK** gombra.
    
    ![Egyetlen Sign-On konfigurálása](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a Resolution GmbH által végzett összefolyásánál

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAML SSO-ba a megoldási szolgáltatással való összefolyásánál, ezeket az SAML SSO-nal kell kiépíteni a Solution GmbH összefolyásánál.  
Az SAML SSO a Solution GmbH összefolyásánál a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az SAML SSO-ba, hogy az a megoldás a vállalat webhelye rendszergazdaként való összefolyásánál.

2. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet** elemre.

    ![Képernyőkép, amely a menüből kiválasztott "fogaskerék" ikont és a "felhasználói kezelés" elemet mutatja.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. A **"felhasználó hozzáadása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználó e-mail-címét, például Britta Simon.

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a Britta Simon jelszavát.

    e. Kattintson a **Jelszó megerősítése** gombra a jelszó újbóli megadásához.
    
    f. Kattintson a **Hozzáadás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja az SAML SSO-ra a Resolution GmbH bejelentkezési URL-címével való összekapcsoláshoz, ahol elindíthatja a bejelentkezési folyamatot.  

* Nyissa meg az SAML SSO-t a Resolution GmbH közvetlen bejelentkezési URL-címével való összekapcsoláshoz, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie az SAML SSO-ba olyan összefolyásánál, amely az SSO-t állítja be. 

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a My apps-ben a saját alkalmazások megoldási lehetőségeit tartalmazó SAML SSO-hoz való összekapcsolásra kattint, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie az SAML SSO-ba olyan összekapcsoláshoz, amely az SSO-t állítja be. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az SAML SSO-t a Solution GmbH összefolyásánál, kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).